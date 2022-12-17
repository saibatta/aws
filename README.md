### Deploying DynamoDB locally on your computer

DynamoDB Local is provided as an **executable .jar** file. The application runs on **Windows**, Linux, macOS, and other platforms that support **Java.**

**To set up DynamoDB on your computer**

Refere this link: [DynamoDB Local Setup](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.DownloadingAndRunning.html#DynamoDBLocal.DownloadingAndRunning.title "DynamoDB Local Setup")

[DynamoDB Table Config](https://dev.to/ajinkabeer/run-a-dynamodb-instance-locally-with-node-js-without-an-aws-account-58k6 "DynamoDB Table Config")

#### Difference Between Query and Scan in DynamoDB

Query and Scan are two operations available in DynamoDB SDK and CLI for fetching a collection of items. While they might seem to serve a similar purpose, the difference between them is vital. While Scan is **"scanning" through the whole table looking for elements matching criteria, Query is performing a direct lookup to a selected partition based on primary or secondary partition/hash key.**

You will also see the difference in speed. While** Query** usually returns results within **100ms**, **Scan** might even take a **few hours** to find the relevant piece of data.

##### When querying DynamoDB, When You Should Use Scan vs Query

Generally speaking, you should** always favor Query over Scan**. When its not possible (for example, when you're **looking for a piece of data** with a key that is** unknown** to you), and if it's a frequently used pattern, consider adding a GSI (Global Secondary Index) to index that attribute and enable Query. In the last resort, use Scan.


[![Scan vs Query](https://dynobase-assets.s3-us-west-2.amazonaws.com/scan-vs-query.png "Scan vs Query")](https://dynobase-assets.s3-us-west-2.amazonaws.com/scan-vs-query.png "Scan vs Query")


> Scan is also useful when you need to retrieve all the table data.

[Scan vs Query](https://dynobase.dev/dynamodb-scan-vs-query/ "Scan vs Query")


