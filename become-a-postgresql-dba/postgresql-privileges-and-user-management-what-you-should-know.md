# PostgreSQL 權限管理，你應該知道的事[^1]

PostgreSQL 中的使用者管理有時候非常棘手。通常情況下，新使用者會在環境中的幾個關鍵區域內一同管理。通常，權限在某些方面是完美的，但在某些方面的問題是設定不正確。這篇文章將為使用者或角色提供實用的「提示和技巧」，我們將會讓你了解它，並在 PostgreSQL 中進行設定。

在這裡我們關注的主題是：

* PostgreSQL 賦予 Role （角色）的責任

您將了解角色、角色屬性、命名角色的最佳實作以及常見角色設定。

* pg\_hba.conf 設定檔

在本節中，我們將看看其中一個關鍵設定檔及其設定，用於用戶端連接和與伺服器的通訊。

* 資料庫、資料表和欄位層級的權限和限制。

希望設定角色以獲得最佳效能和使用率？你的資料表是否包含敏感資料，只有特權角色才能存取？但是，是否需要允許不同的角色執行限制性的工作？這些問題和更多內容將在本節中說明。

## PostgreSQL's Take on Roles - What is a 'Role' and how to create one?

Permissions for database access within PostgreSQL are handled with the concept of a role, which is akin to a user. Roles can represent groups of users in the PostgreSQL ecosystem as well.

PostgreSQL establishes the capacity for roles to assign privileges to database objects they own, enabling access and actions to those objects. Roles have the ability to grant membership to another role. Attributes provide customization options, for permitted client authentication.

Attributes for roles through theCREATE ROLEcommand, are available in the[official PostgreSQL documentation.](https://www.postgresql.org/docs/10/static/sql-createrole.html)

Below, are those attributes you will commonly assign when setting up a new role. Most of these are self-explanatory. However, a brief description is provided to clear up any confusion along with example uses.

SUPERUSER- A databaseSUPERUSERdeserves a word of caution. Bottom line, roles with this attribute can create anotherSUPERUSER. Matter of fact, this attribute is required to create anotherSUPERUSERrole. Since roles with this attribute bypass all permission checks, grant this privilege judiciously.

CREATEDB- Allows the role to create databases.

CREATEROLE- With this attribute, a role can issue theCREATE ROLEcommand. Hence, create other roles.

LOGIN- Enables the ability to login. A role name with this attribute can be used in the client connection command. More details on this attribute with forthcoming examples.

Certain attributes have an explicit polar opposite named command and typically is the default when left unspecified.

e.g.  
SUPERUSER\|NOSUPERUSER  
CREATEROLE\|NOCREATEROLE  
LOGIN\|NOLOGIN

Let's look at some of these attributes in action for various configurations you can set up to get going.

#### Creating And Dropping Roles

Creating a role is relatively straightforward. Here's a quick example:

| 123 | `postgres=#CREATEROLE $money_man;ERROR: syntax erroratornear"$"LINE 1:CREATEROLE $money_man;` |
| :--- | :--- |


What went wrong there? Turns out, role names cannot start with anything other than a letter.

"What about wrapping the name in double quotes?" Let's see:

| 12 | `postgres=#CREATEROLE"$money_man";CREATEROLE` |
| :--- | :--- |


That worked, though probably not a good idea. How about a special character in the middle of the name?

| 12 | `postgres=#CREATEROLE money$_man;CREATEROLE` |
| :--- | :--- |


No problem there. Even without double quotes, no error was returned.

I'm just not fond of the name structure of $money\_man for a user. I'm dropping you $money\_man and starting afresh. TheDROP ROLEcommand takes care of removing a role. Here it is in use.

| 123 | `postgres=#DROPROLE $money_man;ERROR: syntax erroratornear"$"LINE 1:DROPROLE $money_man;` |
| :--- | :--- |


And another error with the $money\_man role. Again, resorting to the double quotes it is.

| 12 | `postgres=#DROPROLE"$money_man";DROPROLE` |
| :--- | :--- |


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

