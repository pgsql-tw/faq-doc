# 忘記密碼怎麼辦？[^1]

**解決的辦法就是使用密碼以外的方式登入再給一個新的密碼。先決條件是你擁有該系統的管理者權限。**

找到你的`pg_hba.conf`，可能會在`/etc/postgresql-9.1/pg_hba.conf`，像這樣的路徑下。

1. `cd /etc/postgresql-9.1/`

2. 先備份一下

   `cp pg_hba.conf pg_hba.conf-backup`

3. 把下面這行放進檔案中（可以把其他行都註解掉）：

   `local all all trust`

4. 重新啓動你的 PostgreSQL （以 Linux 為例）

   `sudo /etc/init.d/postgresql restart`

   如果服務無法順利啓動，且在 log 裡出現下面這個訊息的話

   > local connections are not supported by this build

   那就把這行

   `local all all trust`

   改成這行

   `host all all 127.0.0.1/32 trust`

5. 你現在可以以任何使用者登入了。那就以超級使用者 postgres 登入吧（如果沒有被特別改過的話，應該是 postgres，有些系統會是 pgsql）

   `psql -U postgres`

   或

   `psql -h 127.0.0.1 -U postgres`

   （前面那行可能不會被認為是從 localhost 登入，所以要特別指出。）

6. 重置你的密碼

   `ALTER USER my_user_name with password 'my_secure_password';`

7. 記得回存你的 pg\_hba.conf，不然你的系統還是處在風險之中

   `cp pg_hba.conf-backup pg_hba.conf`

8. 再一次重啓系統，確認原來你的 pg\_hba.conf 設定也有生效

   `sudo /etc/init.d/postgresql restart`

---



[^1]:  [postgresql - I forgot the password I entered during postgres installation - Stack Overflow](https://stackoverflow.com/questions/10845998/i-forgot-the-password-i-entered-during-postgres-installation)

