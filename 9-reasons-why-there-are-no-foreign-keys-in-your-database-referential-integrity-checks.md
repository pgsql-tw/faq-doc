# 9 Reasons Why There Are No Foreign Keys in Your Database \(Referential Integrity Checks\)[^1]

> 待譯

I got into an argument with a few DBAs and architects recently, and they were shocked that some databases don't have foreign keys and claimed that this is a design flaw and shouldn't take place. And if it does, should be immediately fixed. I would like to argue with that. My experience tells me that many of the databases \(the majority of those I have worked with\) do not contain foreign keys and that it's not always a bad thing. In this article, I want to focus on the reasons for why it is so.

## Why is That a Problem? {#toc_0}

### 1. Potential Data Integrity Issues, Duh

The obvious problem with the lack of foreign keys is that a database can't enforce referential integrity and if it wasn't taken care of properly at the higher level then this might lead to inconsistent data \(child rows without corresponding parent rows\).

### 2. Tables Relations are Not Clear

Another, less visible, negative effect of lack of foreign keys in a database is that people who don't know the schema have a hard time finding the right tables and figuring out table relations. This may lead to[serious problems](https://dataedo.com/blog/2-common-sql-join-traps-with-test-queries)with querying and reporting from the database.

## Why Databases Don't Have Foreign Keys? {#toc_1}

Let's have a look at the reasons why databases don't have foreign keys. But first, a short**disclaimer**\(because article sparkled some controversy on LinkedIn groups\):

Reasons presented below are**in no way encouragement not to use foreign key constraints**in the databases. It is merely a collection of reasons I was able to find in various sources \(internet forums mostly\) on why many developers, architects or vendors do not use them. I personally \(and many other experienced database professionals\) advise to use them wherever you can \(where they are not causing more problems than they solve\). I leave you decision which of those reasons actually make a good case. More about problems lack of FKs cause in another article.

### 1. Performance

Having active foreign keys on tables improves data quality but hurts performance of insert, update and delete operations. Before those tasks database needs to check if it doesn't violate data integrity. This is a reason why some architects and DBAs give up on foreign keys at all. This is especially the case in data warehouses and analytical databases that do not process data in a transactional manner \(one row at a time\) but in bulk. And performance is everything in data warehousing and business intelligence.

### 2. Legacy Data

Many databases when they were designed required to store legacy data from the older databases and sources which may not have been so strict on data quality and integrity. To be able to contain old dirty data architect had a choice of a\) cleansing and transforming legacy data \(expensive exercise\) or b\) give up on enforcing referential integrity on the database level. Some packaged ERP and CRM applications also use this approach.

### 3. Full Table Reload

Some databases, like data warehouses, staging or interfacing databases, require for the data to be often reloaded from external sources. This causes the data to be inconsistent at the time of reloading \(child table may be fully loaded while parent table is empty\). That could be bypassed by disabling keys for the time of the reload. However, this introduces additional logic and complexity and another point of failure. And as mentioned above, has a negative effect on performance. Often, costs outweigh benefits and developers just don't bother with the keys.

### 4. Higher Level Framework

Some applications use programming frameworks that make another logical layer on top of physical databases. Developers don't use_insert_or_update_statements to modify data but use an API, or the framework does everything in the background. This is the case in**ORM**\(Object-Relational Mapping\) frameworks or frameworks like**Ruby on Rails**. Those tools take care of the referential integrity and together with RDBMS create a higher level database engine. Those frameworks can create database tables themselves and not always create foreign keys. Developers using such tools, rarely interfere with automatically generated schema and leave it without the keys.

### 5. Cross Database Relations

This one is not the right reason why database doesn't have foreign keys but why it might be missing some. Some databases span across more physical databases and even engines, and it might not be technically possible to create keys that span across databases. SQL Server is a good example here - it doesn't enable creating keys across two databases on the same server. And this architecture is common in larger scale systems.

### 6. Database Platform Agnostic

Similar to the previous one, some applications were designed to be database platform \(DBMS\) agnostic and to be able to work on various databases like Oracle, SQL Server, DB/2 or Sybase. This is what I've read about**PeopleSoft**\(currently owned by Oracle\). Designers didn't want to tie to any particular platform and pushed all the logic to the application layer leaving database layer as plain as possible.

### 7. Open For Change

Sticking with Oracle, another story I've heard about its applications, this time its own child -**Oracle E-Business Suite**- is that it was designed to be as customizable as possible. Oracle provided a solid foundation and left implementation teams elasticity to decide on as many aspects of the design as possible. At least this is what_they_say. Maybe the reason was the same as in the previous point or was it the next one:

### 8. Lazy Architect

When you are creating a database, and you want to store data you need to create some tables and columns. This is a minimum. But you don't have to create structures that keep data consistency, like primary, unique, foreign keys or constraints. It requires some effort and has no immediate benefit. Some of the architects and DBAs just ignore that part.

### 9. Keep Model a Secret

Maybe this one is far fetched, but maybe sometimes it is because people don't want other to have it too easy and know too much. People, in general, want to be needed and irreplaceable. A perfect self-explanatory design might make them obsolete. But this is just my theory.

## Conclusion {#toc_2}

I hope I managed to present sensible reasons why foreign keys aren't used as widely as many might expect. If you know any other cause or don't agree with my arguments, please comment.

## Learn more {#toc_3}

1. [Why Is Metadata Important for Databases](https://dataedo.com/blog/why-is-metadata-important-for-databases)
2. [6 Typical Metadata Fields Stored by Applications](https://dataedo.com/blog/typical-metadata-fields-stroed-by-applications)
3. [8 Good Occasions to Start Documenting Your Databases](https://dataedo.com/blog/good-occasions-to-start-documenting-your-databases)

---

[^1]:  [9 Reasons Why There Are No Foreign Keys in Your Database \(Referential Integrity Checks\)](https://dataedo.com/blog/why-there-are-no-foreign-keys-in-your-database-referential-integrity-checks)

