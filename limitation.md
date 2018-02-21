# PostgreSQL 資料庫可以有多大？[^1]

| **限制** | **範圍** |
| :--- | :--- |
| 資料庫（Database）大小限制 | 無限制 |
| 最大資料表（Table）（註） | 32 TB \(9.6及之前版本\)、2 EB \(10\)、131 YB \(11\) |
| 最大資料列（Row） | 1.6 TB |
| 最大欄位（Field）內容 | 1 GB |
| 資料表中最多可以有幾個資料列 | 無限制 |
| 資料表中最多可以有幾個欄位 | 250 - 1600 （與欄位型別有關） |
| 資料表最多可以有幾個索引（Index） | 無限制 |

* 最大資料表後來修正了宣告問題[^2]，所以在 10 之後的版本就不再受限於 32 TB 了。
* 大型資料表只代表可以「儲存」的資料量，不一定是可以「運算」的資料量，仍然要保持資料表在適當的大小。

---

[^1]: [PostgreSQL: About](https://www.postgresql.org/about/)
[^2]: [PostgreSQL Maximum Table Size](https://blog.2ndquadrant.com/postgresql-maximum-table-size/)
