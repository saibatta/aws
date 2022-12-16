### Creating A Simple Crud Service With Documentdb:

#### Our service will consist of the following resources:

1. DocumentDB cluster (with one instance) - to use as our database.
2. Two Lambda functions - one to write documents to our DB, and the other to read documents from the DB.
3. REST API Gateway - to get the requests and direct them to our relevant Lambda functions.


[![](https://www.altostra.com/images/blog/aws-documentdb/project.png)](https://www.altostra.com/images/blog/aws-documentdb/project.png)

#### Creating our project:
- Add a **DocumentDB** cluster resource with one instance (with default configurations).
    -  The clusters password is generated automatically with Secret Manager.
    - The secret ID will be stored in an environment variable that you can access in the code, without exposing the stored value of the password.
    -     If you want to see the the secret value, its accessible in the AWS Secrets Manager web console*. 
	
- Add a **Lambda** Function  that connects to** MongoDB and inserts a document to the DB**.

- Add a **Lambda** Function that connects to** MongoDB and gets an existing document from the DB**.

- Connect **both Lambda** functions to **DocumentDB**.
    - **Insert** document should have a** read-write** connection.
    - **Get** document should have a** read-only **connection.

When we create a DocumentDB Cluster, it's created by default with TLS enabled, so we need a certificate for connecting to the DB. We will download the certificate file in our Lambdas code, and send it to the connection method.

####  insert-doc Lambda:

- First, install mongodb and axios packages

     `$ npm i mongodb axios`
	 
```javaacript
const MongoClient = require('mongodb').MongoClient;
const aws = require('aws-sdk');
const path = require('path');
const axios = require('axios');
const fs = require('fs');
const { tmpdir } = require('os');

exports.handler = async (event, context) => {
  try {
    //get environment variables values
    const [host, port] = process.env.DOC_DB_DOCUMENTDB01.split(':');  // cluster Host and Port
    const secretManager = new aws.SecretsManager()
    const secret = await secretManager.getSecretValue({
      SecretId: process.env.DB_SECRET_DOCUMENTDB01
    }).promise()
    //create MongoDB connection uri
    let { username, password } = JSON.parse(secret.SecretString);  // DB User name & Password
    username = encodeURIComponent(username);
    password = encodeURIComponent(password);
    const uri = `mongodb://${username}:${password}@${host}:${port}/sample-database?tls=true&replicaSet=rs0&readPreference=secondaryPreferred&retryWrites=false`;
    //download the certificate and create a pem file
    const { data: certificate } = await axios.get('https://s3.amazonaws.com/rds-downloads/rds-combined-ca-bundle.pem');
    const dirPath = path.join(tmpdir(), 'rds-combined-ca-bundle.pem');
    try {
      fs.writeFileSync(dirPath, certificate, {
        encoding: 'utf-8'
      });
    }
    catch (err) {
      console.error(err);
    }
    //connect to MongoDB with certificate
    const client = await MongoClient.connect(
      uri,
      {
        useNewUrlParser: true,
        tlsCAFile: dirPath,
      }
    );
    //Specify the database and collection to be used
    const db = client.db('sample-database');
    const col = db.collection('sample-collection');
    //insert a new document to MongoDB
    const doc = await col.insertOne({ 'hello': 'Amazon DocumentDB;' });
    client.close();
    return {
      statusCode: 200,
      body: JSON.stringify(doc)
    };
  }
  catch (err) {
    return {
      statusCode: 500,
      body: err.message
    };
  }
}
```
####  get-doc Lambda:
Use the same code for the get-doc Lambda, but with a call to **fineOne instead of insertOne**:
```javascript
//get an existing document from MongoDB
const doc = await col.findOne({ 'hello': 'Amazon DocumentDB;' });
```
#### Deploy:
Now that we added the code, lets deploy the first version of our project

#### Test:
- Insert Doc:
 ` Request URL: <Insert document lambda endpoint >/insert-doc`
	`Response : {
	insertId:'DB random generated ID', 	acknowledged:'true;' 
	}`
	
- Get Doc:
    ` Request URL: <Get document lambda endpoint >/get-doc`
	`Response : {
	_id:'DB random generated ID', 	hello:'Amazon DocumentDB;' 
	}`

##### Troubleshoot
If we faced any issue relate dto Task timed out after some time in lambda function logs while post/get the documents to DB

- As a rule, databases should be secured, and only accessible from within our internal network, which consists of the private subnets of our VPC.
- In this case, to allow our Lambda functions to access our DB, they need to run in the same subnets. When we deploy a DocumentDB without specifying a VPC, it will be deployed to the default VPC in the region you're working on.

##### Configuring the VPC:
Let's update the Lambda function's VPC's  by setting the subnets to the same subnets of DocumentDB.

- Our cluster is in the default VPC. We can see the default subnets in the AWS DocumentDB web console:.
- In addition, MongoDB default port is 27017, so (only) this port should be allowed in the Security Group of the DocumentDB Cluster. We will define the port in the inbound rules of the Security Group of the Cluster. The Security Group of the Cluster should only allow traffic from inside the VPC, meaning, only from the private subnets of the VPC.
- First, will create a security group for the Lambda functions (with an inbound rule of all traffic from the current security group, for simplicity). 
- Second, will create a security group with an inbound rule for the MongoDB port, that only allows traffic from our Lambda functions Security Group. 
- Now we have a list of subnets from the DocumentDB clusters default VPC, and two security groups  one for the Lambda functions and the other for our cluster. Lets  configure the Subnets and the Security Group in our project
- The cluster's subnets update requires cluster deletion (and recreation), so you should also disable deletion protection (to avoid errors when updating the stack). 

###### Reference :
- [AWS DocumentDB docs](https://docs.aws.amazon.com/documentdb/latest/developerguide/how-it-works.html "AWS DocumentDB docs")
-  [AWS DocumentDB](https://www.altostra.com/blog/aws-documentdb "AWS DocumentDB")
