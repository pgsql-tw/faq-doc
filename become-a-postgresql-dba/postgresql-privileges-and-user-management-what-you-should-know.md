# PostgreSQL 權限管理，你應該知道的事[^1]

PostgreSQL 中的使用者管理有時候非常棘手。通常情況下，新使用者會在環境中的幾個關鍵區域內一同管理。通常，權限在某些方面是完美的，但在某些方面的問題是設定不正確。這篇文章將為使用者或角色提供實用的「提示和技巧」，我們將會讓你了解它，並在 PostgreSQL 中進行設定。

在這裡我們關注的主題是：

* PostgreSQL 賦予 Role （角色）的責任

您將了解角色、角色屬性、命名角色的最佳實作以及常見角色設定。

* pg\_hba.conf 設定檔

在本節中，我們將看看其中一個關鍵設定檔及其設定，用於用戶端連接和與伺服器的通訊。

* 資料庫、資料表和欄位層級的權限和限制。

希望設定角色以獲得最佳效能和使用率？你的資料表是否包含敏感資料，只有特權角色才能存取？但是，是否需要允許不同的角色執行限制性的工作？這些問題和更多內容將在本節中說明。

## PostgreSQL 的 Role - 什麼是「Role」以及如何建立 Role？

PostgreSQL 中的資料庫存取權限是通過角色（Role）的概念處理的，類似於使用者（user）。但角色也可以代表 PostgreSQL 生態系統中的群組（group）。

PostgreSQL 賦予角色的能力來為他們擁有的資料庫物件分配權限，從而啟用對這些物件的存取和操作。角色有能力授予其他角色的使用者資格。為允許的用戶端驗證身份，也提供了自訂選項的屬性。

PostgreSQL 文件中提供了透過 [CREATE ROLE](https://www.postgresql.org/docs/10/static/sql-createrole.html) 指令取得角色的屬性。

以下是你在設定新角色時通常會分配的屬性，它們大部分都是不需要特別指定的。然而，這裡提供了一個簡短的描述來清除任何混淆以及範例用法。

SUPERUSER - 資料庫的 SUPERUSER 必須要謹慎。 最基本的，具有此屬性的角色可以建立另一個 SUPERUSER。事實上，建立另一個 SUPERUSER 角色這個屬性是必備的。由於具有此屬性的角色會繞過所有的權限檢查，因此應明智地授予此權限。

CREATEDB - 允許角色建立資料庫。

CREATEROLE - 使用此屬性，角色可以發出 CREATE ROLE 指令。也就是，建立其他角色。

LOGIN - 啟用登入的功能。用戶端連線命令中可以使用具有此屬性的角色名稱。有關此屬性的更多詳細訊息，會在後續的例子中說明。

某些屬性具有功能之間相反的命令，並且通常在未指定時是預設值。

例如：  
SUPERUSER \| NOSUPERUSER  
CREATEROLE \| NOCREATEROLE  
LOGIN \| NOLOGIN

讓我們來看看這些屬性在實際操作中可以設定的各種配置方式。

#### 建立和移除角色

建立一個角色相對比較直覺。這裡有個簡單的例子：

```
postgres=\# CREATE ROLE $money\_man;
ERROR: syntax error at or near "$" 
LINE 1: CREATE ROLE $money\_man;
```

哪裡裡出了什麼問題？事實上，角色的名稱不能以英文字母以外的任何其他字元開頭。

「把這個名字用雙引號括起來怎麼樣？」，我們來試試看：

postgres=\# CREATE ROLE "$money\_man";   
CREATEROLE

看起來可以，雖然可能不是一個好的方式。那麼名字中間有特殊字元如何？

postgres=\# CREATE ROLE money$\_man;  
CREATEROLE

一樣沒問題。即使沒有雙引號，也不會回傳任何錯誤。

但我不會喜歡 $money\_man 的名稱結構。我決定放棄 $money\_man 並且重新開始。DROP ROLE 指令負責刪除角色。來使用它吧。

postgres=\# DROP ROLE $money\_man;  
ERROR: syntax error at or near "$"  
LINE 1: DROP ROLE $money\_man;

又有 $money\_man 角色的另一個錯誤。 一樣，再使出雙引號吧。

postgres=\# DROP ROLE "$money\_man";  
DROPROLE

#### The LOGIN privilege

Let's look at two different users, one with the LOGIN privilege and one without. I'll assign them passwords as well.

| 12 | `postgres=#CREATEROLE nolog_userWITHPASSWORD'pass1';CREATEROLE` |
| :--- | :--- |


| 12 | `postgres=#CREATEROLE log_userWITHLOGINPASSWORD'pass2';CREATEROLE` |
| :--- | :--- |


Note: The passwords provided to the above fictional roles are for demonstration purposes only. You should always strive to provide unique and hardened passwords when implementing roles. While a password is better than no password, a hardened password is even better than a trivial one.

Let's assign log\_user theCREATEDBandCREATEROLEattributes with theALTER ROLEcommand.

| 12 | `postgres=#ALTERROLE log_user CREATEROLE CREATEDB;ALTERROLE` |
| :--- | :--- |


You can verify these set attributes, by checking thepg\_rolecatalog. Two columns of interest arerolcreateroleandrolcreatedb. Both are of theBooleandata type so they should be set totfor true for these attributes.

Confirm with a similarSELECTquery.

| 12345 | \`postgres=\#SELECTrolcreaterole, rolcreatedbFROMpg\_rolesWHERErolname ='log\_user';rolcreaterole | rolcreatedb---------------+-------------t | t\(1 row\)\` |
| :--- | :--- | :--- | :--- |


ClusterControl

Single Console for Your Entire Database Infrastructure

Find out what else is new in ClusterControl

[Install ClusterControl for FREE](https://severalnines.com/getting-started)

How can you determine the existing roles present in the database?

Two available methods are the psql\ducommand or selecting from thepg\_rolescatalog.

Here they both are in use.

| 12345678910111213 | \`postgres=&gt; \duListofrolesRolename | Attributes | Memberof------------+------------------------------------------------------------+-----------log\_user | Createrole,CreateDB | {}nolog\_user | Cannot login | {}postgres=&gt;SELECTrolnameFROMpg\_roles;rolname----------------------nolog\_userlog\_user\(2rows\)\` |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |


#### Logging in

Let's give both roles, an opportunity to login to the server.

| 123456 | `psql -U nolog_user -W postgresPasswordforusernolog_user:psql: FATAL:nopg_hba.conf entryforhost"[local]",user"nolog_user",database"postgres", SSLoffpsql -U log_user -W postgresPasswordforuserlog_user:psql: FATAL:nopg_hba.conf entryforhost"[local]",user"log_user",database"postgres", SSLoff` |
| :--- | :--- |


To resolve this issue, we have to dig into thatpg\_hba.conffile. The solution is discussed as we continue in this post, to that specific section.

### Actionable Takeaways

* CREATE ROLE
  and its counterpart,
  DROP ROLE
  , are your go-to commands for implementing and removing roles.
* ALTER ROLE
  handles changing the attributes of a role.
* Roles are valid within all databases due to definition at the database cluster level.
* Keep in mind, creating a role name beginning with a special character, requires you to 'address' it with double quotes.
* Roles and their privileges are established using attributes.
* To establish roles needing the
  LOGIN
  attribute by default,
  CREATE USER
  is an optional command at your disposal. Used in lieu of
  CREATE ROLE role\_name LOGIN
  , they are essentially equal.

## The pg\_hba.conf file - Establishing common ground between the server and the client

Covering all aspects and settings for thepg\_hba.conffile in one blog post would be daunting at best. Instead, this section will present common pitfalls you may encounter and solutions to remedy them.

Successful connections require a conjunctive effort from both parts as a whole. Roles connecting to the server, must still meet access restrictions set at the database level, after passing the settings in thepg\_hba.conffile.

Relevant examples of this relationship are included as this section progresses.

To locate yourpg\_hba.conffile, issue a similarSELECTquery, on thepg\_settingsVIEW. You must be logged in as aSUPERUSERto query thisVIEW.

| 123456 | \`postgres=\#SELECTname, settingFROMpg\_settingsWHEREnameLIKE'%hba%';name | setting----------+-------------------------------------hba\_file | /etc/postgresql/10/main/pg\_hba.conf\(1 row\)\` |
| :--- | :--- | :--- | :--- |


Thepg\_hba.conffile contains records specifying one of seven available formats for a given connection request. See the full spectrum[here](https://www.postgresql.org/docs/10/static/auth-pg-hba-conf.html).

For the purpose of this blog post, we will look at settings you can use for a local environment.

Perhaps this server is for your continued learning and study \(as mine is\).

I must make special note that these settings are not the optimal settings for a hardened system containing multiple users.

The fields for this type of connection are:

| 1 | `localdatabase user auth-method [auth-options]` |
| :--- | :--- |


Where they mean:

local- connections are attempted with Unix-domain sockets.

database- Specifies the database\(s\) named for this record match.

user- The database user name matched for this record. A comma-separated list of multiple users or all is allowed for this field as well.

auth-method- Is used when a connection matches this unique record. The possible choices for this field is:

* trust
* reject
* scram-sha-256
* md5
* password
* gss
* sspi
* ident
* peer
* ldap
* radius
* cert
* pam
* bsd

The lines set inpg\_hba.conffile for roles nolog\_user and log\_user look like this:

| 12 | `localall nolog_user passwordlocalall log_user password` |
| :--- | :--- |


Note: Sincepasswordis sent in clear text, this should not be used in untrusted environments with untrusted networks.

Let's look at three interesting columns from thepg\_hba\_file\_rulesVIEWwith the below query. Again your role needs the SUPERUSER attribute to query thisVIEW.

| 12345678 | \`postgres=\#SELECTdatabase, user\_name, auth\_methodpostgres-\#FROMpg\_hba\_file\_rulespostgres-\#WHERECAST\(user\_nameASTEXT\)LIKE'%log\_user%';database | user\_name | auth\_method----------+--------------+-------------{all} | {nolog\_user} | password{all} | {log\_user} | password\(2rows\)\` |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |


We can see identical information from the lines provided above found in thepg\_hba.conffile as we can from the accompanying query. At first glance, it looks as if both roles can log in.

We will test and confirm.

| 12345678 | `psql -U nolog_user -W postgresPasswordforuser nolog_user:psql: FATAL: role"nolog_user"is not permitted to loginpsql -U log_user -W postgresPasswordforuser log_user:psql (10.1)Type"help"forhelp.postgres=>` |
| :--- | :--- |


The key point here is, although nolog\_user and log\_user are both able to login according to thepg\_hba.conffile, only log\_user is allowed to actually login.

Where log\_user passed the database level access restrictions \(By having theLOGINattribute\), nolog\_user did not.

Let's edit log\_user's line in thepg\_hba.conffile and change the database name this role is allowed to access. Here is the change, indicating log\_user can now login to the trial database only.

| 1 | `localtrial log_user password` |
| :--- | :--- |


First let's try to login to the postgres database, which log\_user previously had access to due to theallflag.

| 123 | `$ psql -U log_user -W postgresPasswordforuser log_user:psql: FATAL: no pg_hba.conf entryforhost"[local]", user"log_user", database"postgres", SSL off` |
| :--- | :--- |


Now with the trial database log\_user does have privilege to

| 12345 | `$ psql -U log_user -W trialPasswordforuser log_user:psql (10.1)Type"help"forhelp.trial=>` |
| :--- | :--- |


No error there and thetrial=&gt;prompt shows the currently connected database.

These settings apply within the server environment as well, once a connection is established.

Let's attempt a connection to thatpostgresdatabase again:

| 1234 | `trial=> \c postgres;Passwordforuser log_user:FATAL: no pg_hba.conf entryforhost"[local]", user"log_user", database"postgres", SSL offPrevious connection kept` |
| :--- | :--- |


Through the examples presented here, you should be aware of the customization options for the roles in your cluster.

Note: Oftentimes, reloading thepg\_hba.conffile is required for changes to take effect.

Use the[pg\_ctl utility](https://www.postgresql.org/docs/10/static/app-pg-ctl.html)to reload your server.

The syntax would be:

| 1 | `pg_ctl reload [-D datadir] [-s]` |
| :--- | :--- |


To know where yourdatadiris, you can query thepg\_settingssystemVIEW, if logged in as aSUPERUSERwith a similarSELECTquery as below.

| 12345 | `postgres=#SELECTsettingFROMpg_settingsWHEREname='data_directory';setting          -----------------------------/var/lib/postgresql/10/main(1 row)` |
| :--- | :--- |


Then, give your shell to the postgres user \(or otherSUPERUSER\) with:

| 1 | `$sudo-u postgresbash` |
| :--- | :--- |


Unless you have added thepg\_ctlutility to your$PATH, you must fully qualify it for use, then pass the command to execute, along with thedatadirlocation.

Here is an example:

| 12 | `$/usr/lib/postgresql/10/bin/pg_ctlreload -D/var/lib/postgresql/10/mainserver signaled` |
| :--- | :--- |


Let’s check the server's status with:

| 123 | `$/usr/lib/postgresql/10/bin/pg_ctlstatus -D/var/lib/postgresql/10/mainpg_ctl: server is running (PID: 1415)/usr/lib/postgresql/10/bin/postgres"-D""/var/lib/postgresql/10/main""-c""config_file=/etc/postgresql/10/main/postgresql.conf"` |
| :--- | :--- |


### Actionable takeaways

* Roles must pass requirements from both the
  pg\_hba.conf
  file and database level access privileges.
* pg\_hba.conf
  file is checked from the top down, for each connection request. Order in the file is significant.

## Database, Table, and Column privileges and restrictions - Tailor fit roles for tasks and responsibilities

In order for roles to use database objects \(tables, views, columns, functions, etc...\), they must be granted access privileges to them.

TheGRANTcommand defines these essential privileges.

We'll go over a few examples to get the essence of its use.

#### Creating databases

Since log\_user was granted theCREATEDBandCREATEROLEattributes, we can use this role to create a test database named trial.

| 12 | `postgres=>CREATEDATABASEtrial:CREATEDATABASE` |
| :--- | :--- |


In addition to creating a newROLE:

| 12 | `postgres=>CREATEROLE db_userWITHLOGINPASSWORD'scooby';CREATEROLE` |
| :--- | :--- |


Finally, log\_user will connect to the new trial database:

| 1234 | `postgres=> \c trial;Passwordforuser log_user:You are now connected to database"trial"as user"log_user".trial=>` |
| :--- | :--- |


Notice the prompt changed to the name 'trial' indicating that we are connected to that database.

Let's utilize log\_user toCREATEa mock table.

| 123456 | `trial=> CREATE TABLE another_workload(trial(>idINTEGER,trial(> first_name VARCHAR(20),trial(> last_name VARCHAR(20),trial(> sensitive_info TEXT);CREATE TABLE` |
| :--- | :--- |


Role log\_user recently created a helper role, db\_user. We require db\_user to have limited privileges for table another\_workload.

Undoubtedly, the sensitive\_info column should not be accessed by this role.INSERT,UPDATE, andDELETEcommands should not be granted at this time either, until db\_user meets certain expectations.

However, db\_user is required to issueSELECTqueries. How can we limit this roles abilities within the another\_workload table?

First let's examine the exact syntax found in the[PostgreSQL GRANT command](https://www.postgresql.org/docs/10/static/sql-grant.html)docs, at the table level.

| 1234 | \`GRANT{ {SELECT | INSERT | UPDATE | REFERENCES} \( column\_name \[, ...\] \)\[, ...\] | ALL\[PRIVILEGES\] \( column\_name \[, ...\] \) }ON\[TABLE\] table\_name \[, ...\]TOrole\_specification \[, ...\] \[WITHGRANTOPTION\]\` |
| :--- | :--- | :--- | :--- | :--- | :--- |


Next, we implement the requirements set forth for role db\_user, applying specific syntax.

| 12 | `trial=>GRANTSELECT(id, first_name, last_name)ONTABLEanother_workloadTOdb_user;GRANT` |
| :--- | :--- |


Notice just after theSELECTkeyword, we listed the columns that db\_user can access. Until changed, should db\_user attemptSELECTqueries on the sensitive\_info column, or any other command for that matter, those queries will not be executed.

With db\_user logged in, we'll put this into practice, attempting aSELECTquery to return all columns and records from the table.

| 12 | `trial=>SELECT*FROManother_workload;ERROR: permission deniedforrelation another_workload` |
| :--- | :--- |


Column sensitive\_info is included in this query. Therefore, no records are returned to db\_user.

But db\_user canSELECTthe allowable columns

| 12345678 | \`trial=&gt;SELECTid, first\_name, last\_nametrial-&gt;FROManother\_workload;id | first\_name | last\_name-----+------------+-----------10 | John | Morris191 | Jannis | Harper2 | Remmy | Rosebuilt\(3rows\)\` |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |


That works just fine.

We will testINSERT,UPDATE, andDELETEcommands as well.

| 123 | `trial=>INSERTINTOanother_workload(id,first_name,last_name,sensitive_info)VALUES(17,'Jeremy','Stillman','key code:400Z');ERROR: permission deniedforrelation another_workload` |
| :--- | :--- |


| 1234 | `trial=>UPDATEanother_workloadtrial->SETid = 101trial->WHEREid = 10;ERROR: permission deniedforrelation another_workload` |
| :--- | :--- |


| 123 | `trial=>DELETEFROManother_workloadtrial->WHEREid = 2;;ERROR: permission deniedforrelation another_workload` |
| :--- | :--- |


By not assigningINSERT,UPDATE, orDELETEcommands to db\_user, the role is denied access to using them.

With the plethora of available options, configuring your role is virtually limitless. You can make them fully functional, able to execute any command, or as constrained as your requirements dictate.

Related resources

[ClusterControl for PostgreSQL](https://severalnines.com/product/clustercontrol/for_postgresql)

[Become a PostgreSQL DBA Blog Series](https://severalnines.com/blog?series=690)

[Become a ClusterControl DBA: User Management](https://severalnines.com/blog/become-clustercontrol-dba-user-management)

### Actionable takeaways

* Roles are provided access privileges to database objects via the
  GRANT
  command.
* Database objects and commands against those objects, is highly configurable within the PostgreSQL environment.

## Closing

Through this blog post's provided examples, you should have a better understanding of:

1. Creating a role with specific attributes.
2. Setting a workable connection between the client and server, allowing roles login access to databases.
3. Highly customizing your roles to meet individual requirements for database, table, and column level access by implementing necessary attributes.

[^1]: [PostgreSQL Privileges & User Management - What You Should Know](https://severalnines.com/blog/postgresql-privileges-user-management-what-you-should-know)

