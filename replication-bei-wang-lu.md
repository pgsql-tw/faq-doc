# Replication 備忘錄

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

## 我應該使用 Slony, Bucardo, 或 Londiste 嗎？

1. Slony 整體表現上比 Bucardo 或 Londiste 有更好。
2. Slony 需要安裝 C-level 的延伸套件。
3. Bucardo 和 Londiste 更容易安裝和管理。
4. Bucardo 和 Londiste 分別使用 Perl 和 Python，如果你真的很關心這些工具使用什麼語言的話。

## 我需要多個主要主機，其中兩個伺服器可以互相寫入和複製對方。

請使用 Bucardo.

## 我希望 Amazon PostgreSQL RDS 伺服器作為邏輯複製的目標。

請使用 Bucardo 或 Londiste.

## 我想從 RDS 複製到非 RDS 資料庫。

目前沒有解決方案。

## 我想要一個 RDS資料庫作為邏輯複製的來源。

目前沒有解決方案。

## 等等，RDS 文件說它「supports logical replication」。

它只支援 PostgreSQL 作為 contrib / modules 的一部分提供的測試外掛元件，實際上它並沒有做複製。

## 我想使用 Amazon 的資料遷移服務來進行 PostgreSQL 到 PostgreSQL 的複製。

你可能不會。 它不支援主要的 PostgreSQL 資料型別，如 TIMESTAMP WITH TIME ZONE。

## 我想同時使用 streaming replication 和 logical replication。

這是可能的，但是這個作法的外圍還有很多複雜性，如果你的主要工作失敗，如何成功地進行故障切換。

## 這真的非常複雜。

不是這樣的！ 基本規則是使用 streaming replication 實現高可用性，以 logical replication 解決資料倉庫或其他資料分散問題。其餘的都是細節！

