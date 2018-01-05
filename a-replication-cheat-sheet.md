# Replication 備忘錄[^1]

如此多的 Replication 類型，不過時間不多！ 你應該使用什麼樣的 PostgreSQL Replication 呢？

## 如果主服務器出現故障，我想要一個備用服務器來接管。

請使用 [streaming replication](https://www.postgresql.org/docs/current/static/warm-standby.html#STREAMING-REPLICATION)。

## 我想要做唯讀查詢的負載平衡。

使用 streaming replication，但請注意，接收查詢的副本主機可能落後於主節點。

## 我想在一個緩慢或不可靠的網絡連線進行複寫。

請使用 [WAL Shipping](https://www.postgresql.org/docs/current/static/warm-standby.html).

## I want to upgrade from before 9.4 to 9.4 or higher.

Use[Slony](http://www.slony.info/),[Londiste](https://wiki.postgresql.org/wiki/SkyTools), or[Bucardo](https://bucardo.org/Bucardo/).

## I want to upgrade from 9.4 or later.

Use[pglogical](https://www.2ndquadrant.com/en/resources/pglogical/).

## I want to consolidate multiple servers down to a single data warehouse, or only replicate some tables/databases/etc.

1. If you are on 9.3 or earlier, use Slony, Bucardo, or Londiste. \(But you should probably upgrade first.\)
2. If you are on 9.4, 9.5, or 9.6, use pglogical.
3. If you are on 10, use
   [built-in logical replication](https://www.postgresql.org/docs/current/static/logical-replication.html)
   .

## I want to only replicate some columns in a table

1. If you are on 9.3 or earlier, use Slony.
2. If you are on 9.4 or later, use pglogical.

## I want to replicate tables that do not have a primary \(or other unique\) key.

Upgrade to version 10 and use built-in logical replication.

## Should I use Slony, Bucardo, or Londiste?

1. Slony will have better overall performance than Bucardo or Londiste.
2. Slony requires C-level extensions to be installed.
3. Bucardo and Londiste are \(somewhat\) easier to install and administer.
4. Bucardo and Londiste are in Perl and Python, respectively, if you really strongly care about what language the tool use.

## I need multi-master, where two servers can both take writes and replicate to each other.

Use Bucardo.

## I want an Amazon PostgreSQL RDS server to be the target for logical replication.

Use Bucardo or Londiste.

## I want to replicate from RDS to a non-RDS database.

No current way of doing this.

## I want an RDS database to be the source for logical replication.

No current way of doing this.

## Wait, the RDS documentation says it “supports logical replication”.

It only supports the test plugin that PostgreSQL provides as part of the contrib/ modules, which doesn’t actually do replication.

## I want to use Amazon’s Data Migration Service for PostgreSQL-to-PostgreSQL replication.

You probably don’t. It doesn’t suport important PostgreSQL types, like`TIMESTAMP WITH TIME ZONE`.

## I want to use both streaming and logical replication at the same time.

This is possible, but there are complexities beyond the scope of this cheat-sheet as to how to successfully fail over if your primary goes down.

## This is awfully complicated.

Not really! The basic rule is to use streaming replication for high availability, and logical replication for data warehousing or other data distribution problems. The rest is all details!

---

[^1]:  [A Replication Cheat-Sheet](http://thebuild.com/blog/2018/01/02/a-replication-cheat-sheat/)

