### Purpose-built databases
[![Purpose built databases](https://github.com/saibatta/aws/blob/assets/purpose%20built%20databases.png?raw=true "Purpose built databases")](https://github.com/saibatta/aws/blob/assets/purpose%20built%20databases.png?raw=true "Purpose built databases")
 Assume around three decades ago most of them(developers) use Relational database/SQL databases becase we have only one choice & no matter of how big or type  that application . Now there are severale data bases are avilble for different application purpose. developer can choose based on that pplication requirements. let say for 
-  Mobile applications document database that provides greate scalability and performance .
-  Tightly related data  develpers can use grapg database to explore hidden connection betweeen records. 
-  High speed applications in-memory cache/databases  to get submilleseconds response.

### Application Architecture 
Three tire architecture is more prominent in the recent developments. Application split into three tiers/layers

[![AWS 3 Tier Architecture](https://github.com/saibatta/aws/blob/assets/aws%203%20tier%20architecture.png?raw=true "AWS 3 Tier Architecture")](https://github.com/saibatta/aws/blob/assets/aws%203%20tier%20architecture.png?raw=true "AWS 3 Tier Architecture")
1. **Presentation tier:** it server as the user interface.
2. **Application tier:** It will handle the business logic and processing
3. **Data tier:**  it will provide long term persistent storage.

*mainframes* to* client-server*  and from *client-server* to *three tier architecture* it's a kind of** horizonal splits**.
**veritical splits** with the microservices rather than handling one application , we can split the services into separate domain(ex: users data, purchase order history,) so that it will  allows for the scaling of the two or more  services independently and for better agility between development teams.

#### Factors to consider when choosing a purpose-built database
There are several factors needs to consider while choosing the databases. It will affects the access pattern of data , the performance of application and operations for which team will be responsible.

1. **Application workload:**
It will describes the type of data being stored by application & data access pattern
So there are mainly three types of workload categories
     - **Transactional**: also called OLTP(Online transactional processing). application use case that are charaterized by high no of concurrent operations and where each operation reading or writing  a small number of rows.
     - **Analytical**: alos called OLAP(Online analtic processing ).kind of aggeregate  and summarize the large volumes of data
	 - **Caching**: storing frquently used data in a separate database for faster reponse time. This can help to reduce the load on transactional database and improve the application performance.
2. **Data Shape:**
data shape will describes about the type of entity and relation ship between diffrent entities.
    - **Relational**: Strict schema validation and data modeling will improves the data integrtity across the application. It will have diffrent tables for the maintating the diffrent entity data.
    - **Key-value or wide-columns**: data store is mainly designed for the scale. data split into multiple stoarge nodes & we can increase multiple storage nodes as data grows.The partitioning schema allows for nearly infine scalability with no performance degradation.
    - **Document**: Document data model uses large records(documents) stored  in a single table rather than spreading across diffent tables. grouping hetorogeneous data in a single row  to access faster.
    - **Graph**: A graph data model emphasizes relationships between data. With a graph database, you traverse relationships between objects to find hidden connections between data. A graph database is commonly used for social networking or fraud-detection services.

3. **Performance requirements:**
If your service is serving a critical workload for users that are awaiting a response, speed is of the utmost importance. You may want to use an in-memory cache to help decrease latency to users.
     
	 On the other hand, if your service is serving internal analytics or is doing background data processing, speed might be less of a factor. You might be more concerned with whether your service can handle the amount of data coming into your system.

4. **Operations burden:** 
need to ensure that you have prepared for instance failures, configured backups, and created a plan for upgrades.
 AWS databases can automatically promote a replica instance in the event that your primary instance fails. Backups and restores are fully managed for you, and you don't need to think about software upgrades
