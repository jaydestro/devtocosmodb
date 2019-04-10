---
title: Uploading your JSON data to Azure Cosmos DB for MongoDB API
published: false
description: Azure April post on uploading data to Cosmos DB
tags: azure, mongodb, cosmosdb, azureapril
---


If you have built an application and are currently storing the data in a static JSON file, you may want to consider the MongoDB API for [Microsoft Azure's Cosmos DB](https://cda.ms/Rd). You will have the document data storage you require for your application with the full management of Microsoft Azure with Cosmos DB along with the ability to scale out globally.  This will permit you to create replication to regions where your customers are.  

 

![Image by Microsoft](https://thepracticaldev.s3.amazonaws.com/i/2uue7l9h1ogz8c1ksz8h.png)


Today we'll take an existing database from an application and use some simple native MongoDB tools to teach you how to easily move some data over to Cosmos DB.


Required:

* [Azure Account](https://aka.ms/AA4qq58)
* [Azure Cosmos DB for MongoDB API](https://aka.ms/devto1jg)
* [Example Database JSON File](https://github.com/jaydestro/devtocosmodb)
* Git
* [`mongoimport` for your local shell](https://docs.mongodb.com/manual/reference/program/mongoimport/)

This example will focus on using a bash terminal which is possible on Mac OS X, Linux and Windows using [Windows Subsystem for Linux](https://aka.ms/AA4qj2b).

To get started, the JSON file that's provided in the repository is a pretty simple dummy data DB created using [json-generator.com](https://www.json-generator.com/).  


![Example JSON Document](https://thepracticaldev.s3.amazonaws.com/i/zflegv1l94c4k02pqndn.png)


You can create your own if you like, but to make it simple this tutorial provides a 1052 entry example document database to import into Cosmos DB.

Go to the Azure Portal and create an Azure Cosmos DB API for MongoDB deployment for our data.

It's very easy to get started by just typing "Cosmos Azure DB" in the search bar, you can then click on the selection that comes up under "Services."

![Search in Azure Portal](https://thepracticaldev.s3.amazonaws.com/i/6ro0uowa8nr5eue5ioma.png)

Now click Add on the upper left and you'll be brought to the blade where you can begin selecting your new Cosmos DB account.  There you can select a subscription you have linked to your account and select a [resource group](https://aka.ms/AA4qb4f) or create a new one.  An account name will represent what hostname will be added to the fully qualified domain name created as part of the endpoint you'll connect your applications to. In this example a new resource group is created along with selecting the Azure Cosmos DB for MongoDB API and a region the data will be hosted in.   

For this example we won't enable either Geo-Redundancy or Multi-Region writes, these are great ways to expand your data's availability but aren't necessary for a demo. 

![Adding Cosmos DB](https://thepracticaldev.s3.amazonaws.com/i/cojlvgkrdrv2v7m6zts6.png)

Click next and move on to the Network section, in this section, we'll need to create a virtual network and then permit ourselves to connect to our API endpoint. 

We begin by creating a new VNET and selecting the `10.2.0.0/16` network.  You will create a default subnet for our tutorial.  In more complex solutions, you may want to create the subnet for this to be private only to further restrict your data's exposure.  For now this example will use a single `10.2.0.0/24` subnet.  Make sure you permit your IP so that you are able to access the endpoint created.


![Adding VNET 1](https://thepracticaldev.s3.amazonaws.com/i/oex9mdgz8wjl7qc4f0qg.png)
![Adding VNET 2](https://thepracticaldev.s3.amazonaws.com/i/bzzmmc52wjs83294z7hk.png)

Click `Review and Create` your new Cosmos DB deployment and validate the settings. Now click Create and wait for your new deployment to be created.


![Deploying Cosmos DB](https://thepracticaldev.s3.amazonaws.com/i/13iqeap3d82r5bhq637w.png)


Now let's make sure you've cloned the repository with your database example file:

```git clone git@github.com:jaydestro/devtocosmodb.git```

Now enter the directory and ensure you can start using the mongo utilities we need:

```
cd devtocosmodb
mongoimport --version
```

You should get some form of output like depending on what OS you are using for this tutorial:

```
mongoimport version: 4.0.2
git version: homebrew
Go version: go1.11
   os: darwin
   arch: amd64
   compiler: gc
OpenSSL version: OpenSSL 1.0.2r  26 Feb 2019
```

By now the Cosmos DB deployment should be ready to go and get the connection string you need in order to import the data.

![created](https://thepracticaldev.s3.amazonaws.com/i/uo0zyy4re4tz9hym53ld.png)

Click go to resource and find the Cosmos DB portal for your new database where we can begin storing collections of documents we can query using the MongoDB API.

Click Quickstart and find the "Others" section, this will provide you with the login information required to use `mongoimport` and import your json.

![Quickstart info](https://thepracticaldev.s3.amazonaws.com/i/tid208yjucvfpmjhhcn8.png)

Now import example-docs.json file to Cosmos DB using `mongoimport` :


```
mongoimport -h exampledevto.documents.azure.com:10255 \
 -d exampledevto -c example -u exampledevto \
  -p  YOURPASSWORDHERE \
  --ssl --jsonArray  --file example-docs.json 
```

An expected output should look something like this:

```
2019-04-10T13:06:08.163-0400	connected to: exampledevto.documents.azure.com:10255
2019-04-10T13:06:11.051-0400	[##############..........] exampledevto.example	1.36MB/2.25MB (60.5%)
2019-04-10T13:06:14.155-0400	[########################] exampledevto.example	2.25MB/2.25MB (100.0%)
2019-04-10T13:06:14.155-0400	imported 1051 documents

```

Now let's look at the new collection and documents in the Data Explorer within the portal in Cosmos DB:


![Data Explorer](https://thepracticaldev.s3.amazonaws.com/i/4bd4uuj6ndm78oq1btw4.png)

Now go to the "Connection String" section to start connecting this data to an application and select your favorite language such as JavaScript or Golang to query and manipulate the data.



