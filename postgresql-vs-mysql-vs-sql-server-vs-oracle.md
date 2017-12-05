# PostGRESQL vs MySQL vs SQL Server vs Oracle：哪個資料庫是你的最佳選擇？[^1]

對於哪個 SQL 資料庫系統比較優秀，總是存在著爭議。PostgreSQL 和 MySQL 之間的真正區別是什麼？每個管理者都有自己的偏好，每個程式設計師都有自己獨特的程式處理方式。因此，如果您詢問五個不同的系統管理員，他們更喜歡哪個平台，那麼你會得到五個不同的答案。

讓我們來看看我們是否能幫助你自己回答。

今天，我們將看看目前網路上可用的一些主要資料庫管理系統的優缺點。一旦我們完成了這一部分，我們將提供一些建議，以確定每個項目最適合哪個項目。讓我們直接來看看：

## Oracle

* 閉源軟體；免費版本的功能非常有限
* 暫時資料表在不同連線間是持續存在的，必須由使用者自行刪除
* 支援四種不同的字元/字串類型：CHAR，VARCHAR2，NCHAR，NVARCHAR2
* 提供資料表和資料列層級的鎖定
* 廣泛和靈活的指令來自訂儲存引擎，像是資料表空間（tablespace）、同義詞（synonym）和套件（package）
* 廣泛的備份機制
* 設計目標在於管理大規模的資料表和資料庫

## MySQL

* Open-source
* Compatible with a wide range of engines and interfaces; one of the most mature databases on the market
* Lightweight
* One of the most popular database tools; easy to find support online
* Temporary tables are only visible within the current active session, and are removed automatically afterwards.
* Lacks ACID compliance
* Can partition tables via LIST, HASH, RANGE, and SET
* Support for two different character/string types: CHAR and VARCHAR
* Offers only table locking
* Lacks options for table views
* Limited storage customization
* Admin tools are incredibly powerful
* Two backup mechanisms: mysqlhotcopy and mysqldump
* Experiences significant performance degradation at high scale.
* Provides little in the way of performance optimization
* Issues with reliability
* Limited security compared to some other database systems.
* Designed for transactional workloads, and as such is ill-suited for analytical workloads

## MS SQL Server

* Closed-source, aimed at corporate/enterprise environments.
* Offers full support for common table expressions
* Requires a deeper understanding of databases/database configuration than other tools
* Can fine-tune security features, such as who can run each stored procedure, who can access data, etc.
* Utilizes an engine that’s slightly slower and resource-heavy, but fully ACID compliant.
* Extremely comprehensive reporting system/storage customization
* High degree of control over transactions and procedures
* Community is not terribly active, not as much support online as MySQL.
* Offers more than simple database functionality through a suite of tools: an ETL tool, analytical DBMS, relational DBMS, and reports server.
* Schema changes do not lock tables.
* Relatively high resource footprint

## DB2

* Closed-source. Enterprise version only available for a price
* Schema-based table management
* Does not support XML
* Can only partition tables via sharding
* No in-memory capabilities
* Designed for relational integrity
* More robust table/data management than MySQL
* Materialized table views
* Lacks native character/string support
* Multiple options for disaster recovery, availability, and scalability

## PostgreSQL

* Open-source
* Adheres well to current SQL standards, and easier to learn as a result
* Large footprint makes it ill-suited for read-heavy operations
* Advanced business/location analytics features
* Rich variety of data and character types
* Fully ACID-compliant
* Designed for reliability and data-integrity; developer-focused
* Full-text search, support for powerful server-side procedural languages
* Full support for advanced SQL features such as table expressions and window functions
* Can efficiently join large numbers of tables
* Replication is poorly-implemented
* Not well-suited for low-concurrency projects

## So, Which Should You Use?

As you can no doubt tell, each of the database tools we’ve profiled here is vastly different in terms of functionality and design focus. The question of which is the right choice for you therefore depends on the type of project you plan to manage.

### You should use Oracle if…

* You require flexibility in terms of transaction control
* You plan to host a large database
* You require a high degree of scalability
* You want your database to be platform-independent

### 你應該使用 MySQL 如果…

* 你的資料庫不會擴展很大的程度
* 你正在計劃建立一個唯讀的網路應用程式或網站
* 你需要多重的資料複製
* 你的專案只需要簡單的查詢，並且較少處理資料一致性的問題

### You should use MS SQL Server if…

* You’re working in a .NET development environment.
* Your database serves a large corporate/enterprise environment
* You’re processing workloads rather than developing applications
* You require fine-tuned control over your database.

### You should use DB2 if…

* Your company already maintains an in-house DB2 Installation
* You wish to federate data from multiple sources
* You need to be able to access data at high speed
* Performance optimization is extremely important to your project

### 你應該使用 PostgreSQL 如果…

* 你規劃使用複雜的自訂程序（stored procedure）
* 你將要使用 Java 工作
* 你的資料庫是龐大而復雜的，需要高度的資料一致性，並且有許多種查詢類型
* 你將會進行許多寫入操作，而讀取速度並不是唯一的考量
* 你的專案是以開發人員為中心的

希望這些回答為這個問題提供了一個很好的基礎：PostgreSQL、MySQL 和其他更多資料庫之間有什麼差異？

---

[^1]:  [PostGRESQL vs MySQL vs SQL Server vs Oracle: Which DBMS Is The Best Choice For You? \| Liberty Center One](https://www.libertycenterone.com/blog/postgresql-vs-mysql-vs-sql-server-vs-oracle/)

