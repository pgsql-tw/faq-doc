# PostgreSQL vs MySQL[^1]

**When considering which database to use for your business, don’t make the mistake of thinking that all open source RDBMS are the same!**

![](https://www.2ndquadrant.com/media/filer_public_thumbnails/filer_public/ba/ad/baad1700-7359-4a1e-9efc-97b63949f21b/2ndquadrant_postgresqlvmysql_display.png__650x211_q85_subsampling-2.png)

There are radical differences between PostgreSQL and MySQL. An informed decision must be made after evaluating the differences and trade-offs between the two systems.

We've provided a summary of similarities and differences of the most commonly evaluated features and abilities between PostgreSQL and MySQL :

* [Open Source](https://www.2ndquadrant.com/en/postgresql/postgresql-vs-mysql/#OPEN_SOURCE)
* [Acid Compliance](https://www.2ndquadrant.com/en/postgresql/postgresql-vs-mysql/#ACID_COMPLIANCE)
* [SQL Compliance](https://www.2ndquadrant.com/en/postgresql/postgresql-vs-mysql/#SQL_COMPLIANCE)
* [Replication](https://www.2ndquadrant.com/en/postgresql/postgresql-vs-mysql/#REPLICATION)
* [Performance](https://www.2ndquadrant.com/en/postgresql/postgresql-vs-mysql/#PERFORMANCE)
* [Security](https://www.2ndquadrant.com/en/postgresql/postgresql-vs-mysql/#SECURITY)
* [Hosting](https://www.2ndquadrant.com/en/postgresql/postgresql-vs-mysql/#HOSTING)
* [Community Support](https://www.2ndquadrant.com/en/postgresql/postgresql-vs-mysql/#COMMUNITY_SUPPORT)
* [Concurrency Support](https://www.2ndquadrant.com/en/postgresql/postgresql-vs-mysql/#CONCURRENCY_SUPPORT)
* [NoSQL/JSON Support](https://www.2ndquadrant.com/en/postgresql/postgresql-vs-mysql/#NOSQL_JSON_SUPPORT)
* [Materialized Views & Temporary Tables](https://www.2ndquadrant.com/en/postgresql/postgresql-vs-mysql/#MATERIALIZED_VIEWS_TABLES)
* [GeoSpatial Data Support](https://www.2ndquadrant.com/en/postgresql/postgresql-vs-mysql/#GEOSPATIAL_DATA)
* [Programming Languages Support](https://www.2ndquadrant.com/en/postgresql/postgresql-vs-mysql/#PROGRAMMING_LANGUAGES)
* [Extensible Type System](https://www.2ndquadrant.com/en/postgresql/postgresql-vs-mysql/#EXTENSIBLE_TYPE_SYSTEM)
* [Comparison Summary](https://www.2ndquadrant.com/en/postgresql/postgresql-vs-mysql/#SUMMARY)

While there are many similarities and overlaps between the two databases there are also very distinct differences. We've tried to provide you with a fair and accurate comparison between the two, but in the end it is necessary to evaluate your unique scenario and determine which database is best suitable for your specific use-case. 

Obviously, we’re rooting for PostgreSQL, but it is possible there are some scenarios in which MySQL best benefits the end-user.



### **Open Source**

Open source software has unique benefits—cost, flexibility, freedom, security, and accountability—that are unsurpassed by proprietary software solutions. Open source software is**freely available**and may be redistributed and modified by anyone. Open source software has long-term viability and is always on the**cutting-edge of technology**. It's created and supported by a worldwide community of organizations and individual developers, many of whom also live by open source values such collaboration and volunteerism.

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL is developed by the PostgreSQL Global Development Group, a diverse group of multiple companies and individual contributors. It’s free and open-source software. PostgreSQL is released under the PostgreSQL license, a liberal Open Source license, similar to the BSD or MIT licenses. | The MySQL development project has made its source code available under the terms of the GNU General Public License, as well as a variety of proprietary agreements.It’s now owned by Oracle Corporation and offers several paid editions for proprietary use. |

  




### **ACID Compliance**

ACID \(Atomicity, Consistency, Isolation, Durability\) is a set of properties of database transactions. The ACID compliance ensures that**no data is lost or miscommunicated**across the system in case of failure, even when there are multiple changes made during a single transaction.

  


| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL is ACID compliant from ground up and ensures that all requirements are met. | MySQL is only ACID compliant when using InnoDB and NDB Cluster Storage engines. |

  




### **SQL Compliance**

SQL compliance is a standard that a database must meet and implements all the structured query language guidelines and standards. This is very important when companies want to work with heterogeneous databases for an application. 

Having SQL compliance makes it very easy to move data from one SQL compliant database to another \(ex. Oracle to PostgreSQL or SQL Server\).

  


| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL is largely SQL compliant. The level of conformance for each feature is clearly laid out in Appendix D of the manual, and any deviations are clearly documented in the “Reference” section of the PostgreSQL manual.[ Extracted from documentation](https://www.postgresql.org/docs/9.6/static/features.html):_PostgreSQL supports most of the major features of SQL:2011. Out of 179 mandatory features required for full Core conformance, PostgreSQL conforms to at least 160. In addition, there is a long list of supported optional features. It might be worth noting that at the time of writing, no current version of any database management system claims full conformance to Core SQL:2011._ | MySQL is partially compliant on some of the versions \(e.g does not support CHECK constraints\).[ Extracted from documentation](https://dev.mysql.com/doc/refman/5.7/en/compatibility.html):_One of our main goals with the product is to continue to work toward compliance with the SQL standard, but without sacrificing speed or reliability. We are not afraid to add extensions to SQL or support for non-SQL features if this greatly increases the usability of MySQL Server for a large segment of our user base._ |

  




### Replication

Database replication is the frequent electronic copying of data from a database in one computer or server to a database in another so that all users share the same level of information. The result is a**distributed database**in which users can access data relevant to their tasks without interfering with the work of others.

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL supports Master-Standby replication and introduced significant enhancements producing extremely fast WAL processing that results in almost real-time replication and hot standby capabilities for standby servers.Replications offered by PostgreSQL:Single master to one standbySingle master to multiple standbysHot Standby/Streaming ReplicationBi-Directional replicationLogical log streaming replicationCascading replication | MySQL supports Master-Standby replication.Replications offered by MySQL:Single master to one standbySingle master to multiple standbysSingle master to one standby to one or more standbysCircular replication \(A to B to C and back to A\)Master to master |

  




### Performance

Performance is an area that can only be gauged by evaluating the metrics of potential scenarios, as it depends on the pure requirements of the specific user and the nature of the application.

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL is widely used in large systems where read and write speeds are crucial and data needs to validated. In addition, it supports a variety of performance optimizations that are available only in commercial solutions such as Geospatial data support, concurrency without read locks, and so on \(e.g. Oracle, SQL Server\). Overall, PostgreSQL performance is utilized best in systems requiring execution of complex queries. PostgreSQL performs well in OLTP/OLAP systems when read/write speeds are required and extensive data analysis is needed. PostgreSQL also works well with Business Intelligence applications but is better suited for Data Warehousing and data analysis applications that require fast read/write speeds. | MySQL is a widely chosen for web based projects that need a database simply for straightforward data transactions.It is common, though, for MySQL to underperform when strained by a heavy loads or when attempting to complete complex queries.MySQL performs well in OLAP/OLTP systems when only read speeds are required.MySQL + InnoDB provides very good read/write speeds for OLTP scenarios. Overall, MySQL performs well with high concurrency scenarios.MySQL is reliable and works well with Business Intelligence applications, as business intelligence applications are typically read-heavy. |

  




### Security

Database security refers to the collective measures used to**protect and secure a database or dbms**from illegitimate use, malicious threats, and attacks. It is a broad term that includes a multitude of processes, tools, and methodologies that**ensure security**within a database environment.

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL has ROLES and inherited roles to set and maintain permissions. PostgreSQL has native SSL support for connections to encrypt client/server communications. It also has Row Level Security.In addition to this, PostgreSQL comes with a built-in enhancement called SE-PostgreSQL which provides additional access controls based on SELinux security policy. More details[here](https://wiki.postgresql.org/wiki/SEPostgreSQL_Introduction). | MySQL implements security based on Access Control Lists \(ACLs\) for all connections, queries, and other operations that a user may attempt to perform. There is also some support for SSL-encrypted connections between MySQL clients and servers. |

  




### Cloud Hosting

As more and more enterprises opt to shift their data into the cloud, the ability to find cloud providers that support your database becomes increasingly important. Cloud hosting allows elasticity of the servers, allowing you to expand or contract their capacity quickly. It also allows for reducing potential downtime while easily managing peak workloads.

| PostgreSQL | MySQL |
| :--- | :--- |
| Supported by all major cloud service providers, including Amazon, Google, & Microsoft. | Supported by all major cloud service providers, including Amazon, Google, & Microsoft. |

  




### Community Support

  


| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL has a very strong and active community that constantly improves existing features while its innovative committers strive to ensure it remains the most advanced database with new cutting-edge features and security. | MySQL has a large community of contributors who, particularly following the acquisition by Oracle, focus mainly on maintaining existing features with some new features emerging occasionally. |

  




### Concurrency Support

Concurrency means that multiple users can have access to the data at the same time. It is one of the core features considered when developing a system that requires multiple subscribers to access data at the same time because it enhances the ability for many people to access and use the database in multiple locations simultaneously.

  


| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL tackles concurrency efficiently with its MVCC implementation, which achieves very high levels of concurrency. | MySQL only has MVCC support in InnoDB. |

  




### NoSQL Features/JSON Support

NoSQL and JSON are both very popular and NoSQL databases are becoming more common. JSON is a simple data format that allows programmers to store and communicate sets of values, lists, and key-value mappings across systems.

  


| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL supports JSON and other NoSQL features like native XML support and key-value pairs with HSTORE. It also supports indexing JSON data for faster access. | MySQL has JSON data type support but no other NoSQL feature. It does not support indexing for JSON. |

  




### Materialized Views/Temporary Tables

A materialized view is a database object that contains the results of a query which can be updated as needed from the original base table. It can be thought of like a ‘cache’ for databases. 

A temporary table stores data not required to persist beyond the life of the session that creates it. The main way it differs from a Materialized View is that the latter provides the ability to periodically update the data, resulting in better efficiency for that use case.

  


| PostgreSQL | MySQL |
| :--- | :--- |
| Supports materialized views and temporary tables. | Supports temporary tables but does not support materialized views. |

  




### Geospatial Data Support

Geospatial data is all the geographic data points that a database keeps and can provide for analysis. It is the information about a physical object that can be represented by numerical values in a geographic coordinate system.

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL supports Geospatial data via the PostGIS extension. There are dedicated types and functions for geospatial data, available directly at database level, making analysis and coding easier for developers. | Geospatial data support is built in. |

  




### Programming Languages Support

Programming languages support helps a wide range of developers to perform several tasks in the language in which they are most proficient. Developers can freely decide, on a case by case basis, whether to perform a given procedure in the server or in the client, because the server supports a wide range of different programming languages for database functions. Programming languages tend to give more power to the developers.

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL supports a wide variety of programing languages including: C/C++, Java, JavaScript, .Net, R, Perl, Python, Ruby, Tcl and others; it’s even possible to run user-supplied code in separate processes \(i.e. running as background workers\). | Some support for server side programming in a single language which is not extensible. |

  




### Extensible Type System

A database supporting extensible type system can be extended by the user in many ways such as adding new data types, functions, operators, aggregate functions, index methods, and procedural languages.

  


| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL has several features dedicated to extensibility. It is possible to add new types, new functions, new index types, etc. | No support for extensibility. |

  




### Comparison Summary

Below is a summarized comparison chart of PostgreSQL vs MySQL:

| Feature | PostgreSQL | MySQL |
| :--- | :--- | :--- |
| _Open Source_ | Completely Open source | Open source, but owned by Oracle and offers commercial versions |
| _ACID Compliance_ | Complete ACID Compliance | Some versions are compliant |
| _SQL Compliance_ | Almost fully compliant | Some versions are compliant |
| _Concurrency Support_ | MVCC implementation supports multiple requests without read locks | Support in some versions. |
| _Security_ | Secure from ground up with SSL support | SSL support in some versions |
| _NoSQL/JSON Support_ | Multiple supported features | JSON data support only |
| _Access Methods_ | Supports all standards | Supports all standards |
| _Replication_ | Multiple replication technologies available:Single master to one standbySingle master to multiple standbysHot Standby/Streaming ReplicationBi-Directional replicationLogical log streaming replication | Standard master-standby replication:Single master to one standbySingle master to multiple standbysSingle master to one standby to one or more standbysCircular replication \(A to B to C and back to A\)Master to master |
| _Materialized Views_ | Supported | Not supported |
| _Temporary Tables_ | Supported | Supported |
| _GeoSpatial Data_ | Supported | Supported |
| _Programming Languages_ | Supported | Not supported |
| _Extensible Type System_ | Supported | Not supported |

---



[^1]:  [PostgreSQL vs MySQL \| 2ndQuadrant](https://www.2ndquadrant.com/en/postgresql/postgresql-vs-mysql/)

