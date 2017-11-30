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

效能是一種只能通過評估潛在情景的領域，因為它取決於特定用戶或應用的單純化需求。

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL 被廣泛應用於讀寫速度至關重要且資料需要驗證的大型系統中。 此外，它在商業解決方案中，還支持各種效能優化，（如地理空間資料支持，不需要讀取鎖定的資料一致性支援，如 Oracle、SQL Server）。 整體來看，PostgreSQL 的效能在需要執行複雜查詢的系統中會得到了最好的展現。 PostgreSQL 在 OLTP / OLAP 系統中運行良好，尤其在需要讀/寫速度和需要大量的資料分析時。 PostgreSQL 也適用於商業智慧應用服務，但更適合需要快速讀/寫速度的資料倉庫和資料分析應用服務。 | MySQL 被廣泛採用，是由於 Web 應用，只需要一個簡單的資料交易資料庫。然而，在一般的情況下，如果 MySQL 的表現不佳，都是當負載過重或試圖進行複雜查詢的時候。MySQL 在 OLAP / OLTP 系統只需要讀取速度時表現良好。MySQL + InnoDB為 OLTP 場景提供了非常好的讀/寫速度。 整體來說，MySQL在高度資料一致性需求的情況下表現良好。MySQL 是可靠的，並且適用於商業智慧應用服務，因為商業智慧應用服務通常是更重視讀取效能的。 |

### Security

資料庫安全性指的是用於保護資料庫或DBMS免於非法使用、惡意威脅和攻擊的綜合措施。這是一個廣泛的術語，包括確保資料庫環境中安全性的多種程序、工具和方法。

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL 具有角色並可繼承角色來設定和維護權限。 PostgreSQL 內建支援 SSL連線來加密客戶端/伺服器通訊。 它具有資料列級的安全性。除此之外，PostgreSQL 還附帶了一個名為 SE-PostgreSQL 的增強功能，可以根據 SELinux 安全策略提供額外的存取控制。更多細節請參考[這裡](https://wiki.postgresql.org/wiki/SEPostgreSQL_Introduction)。. | MySQL 為所有連線、查詢和用戶可能嘗試執行的其他操作實作了存取控制列表（ACL）的安全性。 對 MySQL 客戶端和伺服器之間的 SSL 加密連接也有支援。 |

### Cloud Hosting

隨著越來越多的企業選擇將資料轉移到公有雲中，尋找支援資料庫的公有雲服務商變得越來越重要。雲端主機允許伺服器的彈性擴展，使你能夠迅速擴大或縮小其容量。它還可以減少潛在的停機時間，同時輕鬆管理尖峰負載。

| PostgreSQL | MySQL |
| :--- | :--- |
| 所有主要雲端服務提供商都支持，包括亞馬遜、谷歌和微軟。 | 所有主要雲端服務提供商都支持，包括亞馬遜、谷歌和微軟。 |

### Community Support

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL 擁有非常強大且活躍的社區，不斷改進現有功能，同時創新的提交者努力確保它保持在最先進的功能和安全性的最先進的數據庫。 | MySQL 擁有大量的貢獻者社區，特別是在甲骨文收購之後，他們主要關注於維護現有功能，並偶爾會出現一些新功能。 |

### Concurrency Support

Concurrency 意味著多個使用者可以同時存取資料。在開發需要多個使用者同時存取資料的系統時，這是核心功能之一，因為它增強了許多人同時在多個來源存取和使用資料庫的能力。

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL 透過實作 MVCC 來有效地處理同時間平行存取，從而達到非常高的平行處理等級。 | MySQL 只在使用 InnoDB 時支援 MVCC。 |

### NoSQL Features/JSON Support

NoSQL 和 JSON 都非常流行，NoSQL資料庫變得越來越普及。JSON 是一種簡單的資料格式，它允許程式設計師儲存和傳遞跨系統的資料內容、資料列表和 key-value 對應。

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL 支援 JSON 和其他 NoSQL 的功能，如內建 XML 支援和 HSTORE 的 key-value 對應。 它還支援將 JSON 資料索引以加快存取速度。 | MySQL 具有 JSON 資料類型支援，但沒有其他 NoSQL功能，也不支援 JSON 索引。 |

### Materialized Views/Temporary Tables

Materialized view 是包含查詢結果的資料庫物件，可根據需要從原始資料表中更新查詢結果。它可以被認為是像資料庫的「快取」。

臨時資料表儲存的資料不需要超出資料庫連線的持續時間。與 materialized view 不同的主要方式是後者提供了定期更新資料的能力，從而提高了該資料案例的使用效率。

| PostgreSQL | MySQL |
| :--- | :--- |
| 支援 materialized views 及 temporary tables。 | 支援 temporary tables 但不支援 materialized views。 |

### Geospatial Data Support

地理資訊資料是資料庫儲存並可用於分析的所有地理位置資訊。它是關於實體物件的訊息，可以用地理坐標系中的數值表示。

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL 通過 PostGIS 延伸套件支持地理空間資料。 地理空間資料有專門的類型和功能，可直接在資料庫級別使用，使開發人員可以更輕鬆地進行分析和撰寫程式。 | 內建地理空間資料支援。 |

### Programming Languages Support

Programming languages support helps a wide range of developers to perform several tasks in the language in which they are most proficient. Developers can freely decide, on a case by case basis, whether to perform a given procedure in the server or in the client, because the server supports a wide range of different programming languages for database functions. Programming languages tend to give more power to the developers.

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL 支援各種程式語言，包括：C / C ++，Java，JavaScript，.Net，R，Perl，Python，Ruby，Tcl 等等。 甚至可以在單獨的程序中執行用戶提供的程式（即作為背景執行程式）。 | 一些支援伺服器端不可擴展的程式語言。 |

### Extensible Type System

支援可延伸套件系統的資料庫可以通過多種方式進行強化其功能，如增加新的資料類型、函數、運算符、彙總函數、索引方法和程式語言。

| PostgreSQL | MySQL |
| :--- | :--- |
| PostgreSQL有幾個專用於延伸套件的功能。 可以增加新的資料型別、新的函數功能、新的索引類型等。 | 不支援增加延伸功能。 |

### Comparison Summary

以下是 PostgreSQL 與 MySQL 的簡要比較表：

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

