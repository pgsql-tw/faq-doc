# Replication 備忘錄[^1]

如此多的 Replication 類型，不過時間不多！ 你應該使用什麼樣的 PostgreSQL Replication 呢？

## 如果主服務器出現故障，我想要一個備用服務器來接管。

請使用 [streaming replication](https://www.postgresql.org/docs/current/static/warm-standby.html#STREAMING-REPLICATION)。

## 我想要做唯讀查詢的負載平衡。

使用 streaming replication，但請注意，接收查詢的副本主機可能落後於主節點。

## 我想在一個緩慢或不可靠的網絡連線進行複寫。

請使用 [WAL Shipping](https://www.postgresql.org/docs/current/static/warm-standby.html).

## 我想從 9.4 之前的版本升級到 9.4 或更新版本。

請使用 [Slony](http://www.slony.info/), [Londiste](https://wiki.postgresql.org/wiki/SkyTools), 或 [Bucardo](https://bucardo.org/Bucardo/).

## 我想從 9.4 或更新版本升級。

請使用 [pglogical](https://www.2ndquadrant.com/en/resources/pglogical/).

## 我想整合多個伺服器到一個單一的資料倉庫，或只複製一些資料表/資料庫/等等。

1. 如果你在 9.3 或更早版本，請使用 Slony、Bucardo 或 Londiste。 （但是你應該先升級吧。）
2. 如果你在 9.4、9.5 或 9.6，請使用 pglogical。
3. 如果你是版本 10，請使用內建的 logical replication。

## 我只想複製資料表中的一些資料列

1. 如果你在 9.3 或更早版本，請使用 Slony。
2. 如果你在 9.4 或更新版本，使用 pglogical。

## 我想複製沒有主鍵或其他唯一鍵的資料表。

升級到版本 10 並使用內建的 logical replication。

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

