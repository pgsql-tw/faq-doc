# 資料庫中不使用外部鍵的 9 個理由 \(Referential Integrity Checks\)[^1]

最近我和幾位DBA和架構師爭論，他們對一些資料庫沒有外鍵感到震驚，並聲稱這是一個設計上的缺陷，不應該發生。如果是這樣，應該馬上予以修正。我想與此進行討論。我的經驗告訴我，很多資料庫（大多數我曾經使用的）不包含外部鍵，並不一定是壞事。在這篇文章中，我想把重點放在為什麼它是這樣的原因。

## 為什麼這會是一個問題？ {#toc_0}

### 1. 潛在的資料完整性問題，當然

缺少外鍵的明顯問題是資料庫不能強制處理引用的完整性，如果在上層應用沒有正確處理，則可能會導致資料不一致（子資料列沒有相對應的父資料列）。

### 2. 資料表關連性不清楚

資料庫中缺少外部鍵另一個不太明顯的負面影響是，不了解該資料模式的人很難找到正確的資料表並找出資料表的關連。這可能會導致嚴重的資料庫查詢和報表問題。[^2]

## 為什麼資料庫不需要有外部鍵？ {#toc_1}

讓我們來看看資料庫不用外部鍵的原因。但首先，一個簡短的免責聲明（因為文章引發了一些關於 LinkedIn 群組的爭議）：

> 因為是免責聲明，所以保留原文，僅就大意翻譯。

Reasons presented below are **in no way encouragement not to use foreign key constraints **in the databases. It is merely a collection of reasons I was able to find in various sources \(internet forums mostly\) on why many developers, architects or vendors do not use them. I personally \(and many other experienced database professionals\) advise to use them wherever you can \(where they are not causing more problems than they solve\). I leave you decision which of those reasons actually make a good case. More about problems lack of FKs cause in another article.

下面的理由絕對不是鼓勵不要在資料庫中使用外部鍵限制條件。這僅僅是我在各種管道（主要是網際網路論壇）能找到的許多開發人員、系統架構師或服務供應商為什麼不使用它們的原因。我個人（和許多其他經驗豐富的資料庫專家）建議在任何可能的地方使用它們（不會導致更多衍生的問題）。我讓你做決定，其中那些原因實際上是一個很好的例子。 在另一篇文章中更多關於缺乏外部鍵的問題。

### 1. 效能

在資料表上擁有有意義的外部鍵可以提高資料品質，但會影響插入資料、更新資料和刪除資料操作的效能。在這些任務之前，資料庫需要檢查它是否違反資料完整性。這也就是為什麼一些系統架構師和 DBA 完全放棄外部鍵的原因。資料倉儲和分析資料庫尤其如此，這些資料倉儲和分析資料庫不以交易方式（一次一個資料列）處理資料，而是批量處理資料。效能是資料倉儲和智慧商業的一切。

### 2. 老舊資料

Many databases when they were designed required to store legacy data from the older databases and sources which may not have been so strict on data quality and integrity. To be able to contain old dirty data architect had a choice of a\) cleansing and transforming legacy data \(expensive exercise\) or b\) give up on enforcing referential integrity on the database level. Some packaged ERP and CRM applications also use this approach.

### 3. 資料表重建

Some databases, like data warehouses, staging or interfacing databases, require for the data to be often reloaded from external sources. This causes the data to be inconsistent at the time of reloading \(child table may be fully loaded while parent table is empty\). That could be bypassed by disabling keys for the time of the reload. However, this introduces additional logic and complexity and another point of failure. And as mentioned above, has a negative effect on performance. Often, costs outweigh benefits and developers just don't bother with the keys.

### 4. 有更高層級的開發管理

Some applications use programming frameworks that make another logical layer on top of physical databases. Developers don't use\_insert\_or\_update\_statements to modify data but use an API, or the framework does everything in the background. This is the case in**ORM**\(Object-Relational Mapping\) frameworks or frameworks like**Ruby on Rails**. Those tools take care of the referential integrity and together with RDBMS create a higher level database engine. Those frameworks can create database tables themselves and not always create foreign keys. Developers using such tools, rarely interfere with automatically generated schema and leave it without the keys.

### 5. 跨資料庫關連

This one is not the right reason why database doesn't have foreign keys but why it might be missing some. Some databases span across more physical databases and even engines, and it might not be technically possible to create keys that span across databases. SQL Server is a good example here - it doesn't enable creating keys across two databases on the same server. And this architecture is common in larger scale systems.

### 6. 資料庫可移植考量

Similar to the previous one, some applications were designed to be database platform \(DBMS\) agnostic and to be able to work on various databases like Oracle, SQL Server, DB/2 or Sybase. This is what I've read about**PeopleSoft**\(currently owned by Oracle\). Designers didn't want to tie to any particular platform and pushed all the logic to the application layer leaving database layer as plain as possible.

### 7. 改變的可能性

Sticking with Oracle, another story I've heard about its applications, this time its own child -**Oracle E-Business Suite**- is that it was designed to be as customizable as possible. Oracle provided a solid foundation and left implementation teams elasticity to decide on as many aspects of the design as possible. At least this is what\_they\_say. Maybe the reason was the same as in the previous point or was it the next one:

### 8. 簡易的軟體工程管理

When you are creating a database, and you want to store data you need to create some tables and columns. This is a minimum. But you don't have to create structures that keep data consistency, like primary, unique, foreign keys or constraints. It requires some effort and has no immediate benefit. Some of the architects and DBAs just ignore that part.

### 9. 隱藏資料關連模型

Maybe this one is far fetched, but maybe sometimes it is because people don't want other to have it too easy and know too much. People, in general, want to be needed and irreplaceable. A perfect self-explanatory design might make them obsolete. But this is just my theory.

## 結論 {#toc_2}

I hope I managed to present sensible reasons why foreign keys aren't used as widely as many might expect. If you know any other cause or don't agree with my arguments, please comment.

## 延伸資訊 {#toc_3}

1. [Why Is Metadata Important for Databases](https://dataedo.com/blog/why-is-metadata-important-for-databases)
2. [6 Typical Metadata Fields Stored by Applications](https://dataedo.com/blog/typical-metadata-fields-stroed-by-applications)
3. [8 Good Occasions to Start Documenting Your Databases](https://dataedo.com/blog/good-occasions-to-start-documenting-your-databases)

---

[^1]:  [9 Reasons Why There Are No Foreign Keys in Your Database \(Referential Integrity Checks\)](https://dataedo.com/blog/why-there-are-no-foreign-keys-in-your-database-referential-integrity-checks)

[^2]:  [Common SQL Join Traps \(with Test Queries\)](https://dataedo.com/blog/2-common-sql-join-traps-with-test-queries)

