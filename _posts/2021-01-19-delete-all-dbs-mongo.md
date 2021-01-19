---
layout: post
title: Delete all databases in MongoDB
date:   2021-01-12
categories: MongoDB
---
To delete all databases from our MongoDB instances, run the following command:
```bash
db.adminCommand("listDatabases").databases.forEach(function(d)
	{
	if(d.name!="admin" && d.name!="local" && d.name!="config")
	{
        	db.getSiblingDB(d.name).dropDatabase();
	}
       }
);
```
