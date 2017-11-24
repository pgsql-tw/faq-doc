# PostgreSQL vs MySQL[^1]

在考慮使用哪個資料庫時，不要認為所有的開源資料庫系統都是一樣的！

![](/assets/postgresql-vs-mysql.png)

PostgreSQL 和 MySQL 之間有一些根本上的差別。 你只有在評估兩個系統之間的差異和取捨之後，才做出明智的決定。

我們提供了PostgreSQL和MySQL之間最常見的特性和能力的相似點和不同點的總結：

* [Open Source](#open-source)
* [Acid Compliance](#acid-compliance)
* [SQL Compliance](#sql-compliance)
* [Replication](#replication)
* [Performance](#performance)
* [Security](#security)
* [Cloud Hosting](#cloud-hosting)
* [Community Support](#community-support)
* [Concurrency Support](#concurrency-support)
* [NoSQL/JSON Support](#nosql-featuresjson-support)
* [Materialized Views & Temporary Tables](#materialized-viewstemporary-tables)
* [GeoSpatial Data Support](#geospatial-data-support)
* [Programming Languages Support](#programming-languages-support)
* [Extensible Type System](#extensible-type-system)
* [Comparison Summary](#comparison-summary)

雖然兩個資料庫之間有許多相似之處和重疊，但也有非常明顯的差異。 我們試圖為你提供兩者之間的公平和準確的比較，但最終仍然有必要評估你的獨特使用情境，再確定哪個資料庫最適合你的特定使用情況。

很顯然，我們是以 PostgreSQL 為主，但也有可能出現一些對使用者最有利的 MySQL 方案。

### **Open Source** {#open-source}

開源軟體具有一些獨特的優勢—成本，靈活性，自由，安全性和問責性—這是商有軟體解決方案所無法比擬的。開源軟體是免費的，而且可以被任何人重新散佈和修改。開源軟體具有長期的生存能力，始終處於技術的領導位置。它由一個由全球性組織和個人開發者組成的社區所建立和支持，他們中的許多人也靠開放原始碼的價值來生活，像是一些協作和志願服務。

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL 由 PostgreSQL 全球開發小組（PostgreSQL Global Development Group）開發，PostgreSQL 全球開發組由多個公司和個人貢獻者所組成。它是免費的開源軟體。PostgreSQL 是在 PostgreSQL 使用許可發布的，這是一個自由的開源許可，類似於 BSD 或 MIT 使用許可。 | MySQL 開發專案根據 GNU GPL 的條款提供了它的原始碼以及各種各樣的專有協議。它現在由 Oracle 公司擁有，並且提供了幾個商業使用的付費版本。 |

### **ACID Compliance**

ACID（Atomicity，Consistency，Isolation，Durability）是一組資料庫交易安全的特性。 ACID 要求確保在單一的資料交易中發生多個更新時也不會在整個系統中遺失數據或發生錯誤。

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL 完全符合 ACID 特性，並確保滿足所有要求。 | 只有在使用 InnoDB 和 NDB 叢集儲存引擎時，MySQL 才符合ACID 標準。 |

### **SQL Compliance**

SQL 相容性是資料庫必須滿足並實現所有結構化查詢語言準則和標準。 當公司想要為應用程式使用多種同類型資料庫時，這一點非常重要。

具有 SQL 相容性使得將資料從一個 SQL 相容的資料庫移動到另一個時（例如 Oracle 到 PostgreSQL 或 SQL Server）非常容易。

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL在很大程度上是SQL相容的。 使用手冊的附錄D清楚地列出了每個功能的相容性情形，PostgreSQL手冊的“Reference”章節清楚地記錄了任何差異。 從文件中摘錄：PostgreSQL支持SQL：2011的大部分主要功能。 在滿足核心一致性所需的179個必要功能中，PostgreSQL至少符合160個。此外，還有一大串支持的可選功能。 值得注意的是，在撰寫本文時，沒有任何資料庫管理系統的當時版本聲明完全符合Core SQL：2011。 | MySQL對 SQL 相容較少的（例如不支持 CHECK 約束）。 從官方文冊中摘錄：我們的產品主要目標之一是繼續努力遵守 SQL 標準，但不會犧牲速度或可靠性。 如果這大大增加了我們用戶群中的大部分MySQL服務器的可用性，我們並不害怕增加 SQL 延伸功能或支持非 SQL 功能。 |

### Replication

資料庫複製是從一台主機或伺服器上的資料庫到另一台資料庫中資料的頻繁電子複製，以便所有資料庫服務提供一致性的資訊。其結果是一個分散式資料庫，使用者可以在其中存取與其事務相關的資料，而不會干擾他人的作業。

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL 支援 Master-Standby 複製，並引入了先進的強化功能，提供極其快速的 WAL 處理，為備用伺服器提供了近乎即時的複製和熱備援功能。PostgreSQL 提供的功能：單主機到一個備用單主機（Single master to one standby）／多個備用主機（multiple standby）、雙向複製（Bi-Directional replication）、邏輯日誌串流複製（Logical log streaming replication）、及多層次複製（Cascading replication） | MySQL 支持 Master-Standby 複製。MySQL提供的備份：單一主機到一個備用主機（Single master to one standby）／單主機到多個備用主機（Single master to multiple standbys）、單主機到一個或多個備用主機（Single master to one standby to one or more standbys）、循環式備援（Circular replication）（ A 到 B到 C 和回到 A）、主要主機對主要主機（Master to master） |

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

As more and more enterprises opt to shift their data into the cloud, the ability to find cloud providers that support your database becomes increasingly important. Cloud hosting allows elasticity of the servers, allowing you to expand or contract their capacity quickly. It also allows for reducing potential downtime while easily managing peak workloads.

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

