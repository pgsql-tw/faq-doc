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

* 開放原始碼
* 相容於各式各樣的引擎和介面；這是市場上最成熟的資料庫之一
* 輕量化
* 最流行的資料庫工具之一；很容易在網路上找到支援的資訊
* 暫時資料表只在目前資料庫連線中可以被看見，並在離線之後自動刪除。
* 缺乏全面的 ACID 合規性
* 分割資料表可以使用 LIST、HASH、RANGE 和 SET 等分割方法
* 支援兩種不同的字元/字串型別：CHAR 和 VARCHAR
* 只提供資料表層次的鎖定
* 資料表 view 的選項較少
* 有限的儲存引擎參數調整
* 管理工具非常強大
* 兩種備份機制：mysqlhotcopy 和 mysqldump
* 在大量使用時，經驗上明顯會感受到性能下降。
* 在效能方面，沒有提供什麼最佳化的方式
* 可靠性的問題
* 與其他資料庫系統相比，安全性有限。
* 專為事務性工作負載而設計，因此不適合分析工作負載

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

## 那麼，你應該使用哪個？

毫無疑問，我們在這裡描述的每個資料庫工具在功能和設計方面都有很大的不同。因此，你選擇哪一個的問題取決於你計劃管理的應用類型。

### 你應該使用 Oracle 如果...

* 你在交易安全控制方面需要更多彈性
* 你計劃管理一個大型資料庫
* 你需要高度的可擴展性
* 你希望你的資料庫是與平台無關的

### 你應該使用 MySQL 如果…

* 你的資料庫不會擴展很大的程度
* 你正在計劃建立一個唯讀的網路應用程式或網站
* 你需要多重的資料複製
* 你的專案只需要簡單的查詢，並且較少處理資料一致性的問題

### 你應該使用 MS SQL Server 如果...

* 你正在 .NET 開發環境中工作。
* 你的資料庫服務於大型的企業環境
* 你關心的是負載量而不是開發應用程式
* 你需要對資料庫進行微調。

### 你應該使用 DB2 如果...

* 你的公司已經在內部建置了一個 DB2 環境
* 你希望串接來自多個來源的資料
* 你需要能夠高速存取資料
* 效能最佳化對你的專案非常重要

### 你應該使用 PostgreSQL 如果…

* 你規劃使用複雜的自訂程序（stored procedure）
* 你將要使用 Java 工作
* 你的資料庫是龐大而復雜的，需要高度的資料一致性，並且有許多種查詢類型
* 你將會進行許多寫入操作，而讀取速度並不是唯一的考量
* 你的專案是以開發人員為中心的

希望這些回答為這個問題提供了一個很好的基礎：PostgreSQL、MySQL 和其他更多資料庫之間有什麼差異？

---

[^1]:  [PostGRESQL vs MySQL vs SQL Server vs Oracle: Which DBMS Is The Best Choice For You? \| Liberty Center One](https://www.libertycenterone.com/blog/postgresql-vs-mysql-vs-sql-server-vs-oracle/)

