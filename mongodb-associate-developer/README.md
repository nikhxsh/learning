
## Table of Contents  
- [Introduction to MongoDB](#introduction-to-mongoDB)
- [Q&N](#q&a)

## [Introduction to MongoDB](https://www.mongodb.com/docs/manual/introduction/)
### Introduction to MongoDB
- You can create a MongoDB database in the following environments:
	- MongoDB Atlas: The fully managed service for MongoDB deployments in the cloud
	- MongoDB Enterprise: The subscription-based, self-managed version of MongoDB
	- MongoDB Community: The source-available, free-to-use, and self-managed version of MongoDB
### Document Database
	- A record in MongoDB is a document, which is a data structure composed of field and value pairs
	```json
	{
		"name": "nik",
		"age": 78,
		"grades" : [ "A", "C", "Z"]
	}
	```
	- The advantages of using documents are:
		- Documents correspond to native data types in many programming languages
		- Embedded documents and arrays reduce need for expensive joins
		- Dynamic schema supports fluent polymorphism
 ### Key Features
	- High Performance : MongoDB provides high performance data persistence
		- Support for embedded data models reduces I/O activity on database system
		- Indexes support faster queries and can include keys from embedded documents and arrays
	- Query API : The MongoDB Query API supports read and write operations (CRUD) as well as:
		- [Data Aggregation](https://www.mongodb.com/docs/manual/core/aggregation-pipeline/#std-label-aggregation-pipeline)
		- [Text Search](https://www.mongodb.com/docs/manual/text-search/#std-label-text-search)
		- [Geospatial Queries](https://www.mongodb.com/docs/manual/tutorial/geospatial-tutorial/)
	- High Availability: MongoDB's replication facility, called [replica set](https://www.mongodb.com/docs/manual/replication/), provides (A replica set is a group of MongoDB servers that maintain the same data set, providing redundancy and increasing data availability):
		- automatic failover
		- data redundancy
	- Horizontal Scalability
		- [Sharding](https://www.mongodb.com/docs/manual/sharding/#std-label-sharding-introduction) distributes data across a cluster of machines
		- Starting in 3.4, MongoDB supports creating zones of data based on the shard key. In a balanced cluster, MongoDB directs reads and writes covered by a zone only to those shards inside the zone
	- Support for Multiple Storage Engines
		- WiredTiger Storage Engine (including support for Encryption at Rest)
		- In-Memory Storage Engine
 
## Q&A
Q: My EC2 Instance does not have the permissions to perform an API call PutObject on S3. What should I do?
> I should ask an administrator to attach a Policy to the IAM Role on my EC2 Instance that authorizes it to do the API call (IAM roles are the right way to provide credentials and permissions to an EC2 instance)
