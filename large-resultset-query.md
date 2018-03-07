# JDBC查詢資料時出現「Out of Memory」？

> ### 注意：
>
> 其他程式語言或資料庫也可能會有這個問題，請查閱相對應的驅動程式手冊。

簡單來說：

JDBC 在查詢資料時，預設會取回所有的資料，所以如果你的資料量過大，且AP端的資源不足時，就可能會產生這個錯誤。

詳細說明，可參閱 [PostgreSQL JDBC 使用手冊第 5 章](https://jdbc.postgresql.org/documentation/head/query.html)的內容。以下內容摘錄自該章[^1]：

任何時候你想要將 SQL 查詢語句送到資料庫，你都需要一個 Statement 或PreparedStatement。一旦你有一個 Statement 或 PreparedStatement 了，你可以就可以開始查詢。這將回傳一個 ResultSet 的實例，該實例包含整個結果（請參閱「[以游標（cursor）取得結果](#以-cursor-取得結果)」一節以了解如何更改此行為）。 範例 1，「[在 JDBC 中進行一個簡單的查詢](#範例 1 在-jdbc-中進行一個簡單的查詢)」說明了這個過程。

##### 範例 1. 在 JDBC 中進行一個簡單的查詢

這個範例將送出一個簡單的查詢並使用一個 Statement 來輸出每一個資料列的第一個欄位：

```
Statement st = conn.createStatement();
ResultSet rs = st.executeQuery("SELECT * FROM mytable WHERE columnfoo = 500");
while(rs.next()){
    System.out.print("Column 1 returned ");
    System.out.println(rs.getString(1));
}
rs.close();
st.close();
```

此範例與之前一樣送出相同的查詢，但在查詢中使用 PreparedStatement 和設定值：

```
int foovalue = 500;
PreparedStatement st = conn.prepareStatement("SELECT * FROM mytable WHERE columnfoo = ?");
st.setInt(1,foovalue);
ResultSet rs = st.executeQuery();
while(rs.next()) {
    System.out.print("Column 1 returned ");
    System.out.println(rs.getString(1));
}
rs.close();
st.close();
```

### 以游標（cursor）取得結果

預設的情況下，驅動程式會一次取回查詢的所有結果。這對於大數據來說可能會不方便，因此 JDBC 驅動程式提供了將 ResultSet 放在資料庫游標上的方法，只提取少量的資料列。

少量的資料列會被暫存在連線的用戶端，當資料取完時，再透過重新定位游標來檢索下一群的資料列。

### 注意

> 以游標的 ResultSet 查詢無法在所有情況下使用。有很多限制會讓驅動程式暗自地回到一次取得整個 ResultSet。

* 與伺服器的連線必須使用 V3 協定。這是伺服器 7.4 版或更高版本的設定（並且僅接受 V3）。
* 連線不得處於自動提交（autocommit）模式。後端在交易事務結束時才關閉游標，而在自動提交模式下，後端將在關閉交易之前關閉游標。 \* Statement 必須用 ResultSet型別的 ResultSet.TYPE\_FORWARD\_ONLY。這是預設值，因此不需要重寫程式就能使用，但這也意味著你無法倒退或以其他方式在 ResultSet 中跳轉。
* 送出的查詢必須是單一個查詢語句，而不是以多個分號串在一起的查詢語句。

##### 範例 2. 以設定暫存大小來開關游標的使用

將程式更改為游標模式非常簡單，只需將語句的暫存大小設定為適當值即可。將暫存大小設定回 0，就會導致所有資料列被暫存（預設行為）。

```
// make sure autocommit is off
conn.setAutoCommit(false);
Statement st = conn.createStatement();
// Turn use of the cursor on.
st.setFetchSize(50);
ResultSet rs = st.executeQuery("SELECT * FROM mytable");
while(rs.next()) {
    System.out.print("a row was returned.");
}
rs.close();
// Turn the cursor off.
st.setFetchSize(0);
rs = st.executeQuery("SELECT * FROM mytable");
while(rs.next()) {
    System.out.print("many rows were returned.");
}
rs.close();
// Close the statement.
st.close();
```

---



[^1]:  [Chapter 5. Issuing a Query and Processing the Result](https://jdbc.postgresql.org/documentation/head/query.html)

