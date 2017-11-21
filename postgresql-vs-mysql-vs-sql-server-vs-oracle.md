# PostGRESQL vs MySQL vs SQL Server vs Oracle：哪個資料庫是你的最佳選擇？[^1]

There’s always been some contention over which SQL database tool is superior. What’s the real difference between PostGRESQL vs MySQL and more? Every administrator has their preferences, and every programmer has their own unique way of dealing with code. For that reason, if you ask five different sysadmins which platform they prefer, you’re like to receive five different answers.

Let’s see if we can’t help you come to your own answer.

Today, we’re going to look at some of the major database management systems currently available on the web in regards to their pros and cons. Once we’ve worked that part out, we’ll offer up a few recommendations as to which projects each one is best-suited for. Let’s dive right in.

## Oracle

* Closed-source; free version has very limited feature set
* Temporary tables persist across sessions, and must be removed by the user
* Support for four different character/string types: CHAR, VARCHAR2, NCHAR, NVARCHAR2
* Offers both table and row locking
* Extensive and flexible storage customization with commands like tablespace, synonym, and packages
* Extensive backup mechanisms
* Designed to manage tables and databases on a large-scale basis

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

