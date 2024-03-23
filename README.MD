# data-with-granular-access-readme
INTEGRATOR:


EXPOSEE:
PostgreSQL

This PostgreSQL database was setup with Column and Row Level Security

before proceeding with the database connection, let's have a look at the tables, and the priviliges of the two existing users in the database.
there are three tables in the psql container.

TABLES

products:
```
  title  |     description     | price | id | created_by  
---------+---------------------+-------+----+------------  
 besked1 | Her er en besked    |       |  1 | root  
 bukser  | bukser beeskrivelse |       |  2 | root  
 trøje   | trøje beeskrivelse  |  2000 |  3 | root  
 bil     | bil beeskrivelse    | 50000 |  4 | root  
 ketcher | brugt               |   100 |  6 | user1  
```
secrets:
```
 secret_title |     secret_description     | id |             limited_visibility             | role_privilege  
--------------+----------------------------+----+--------------------------------------------+----------------  
 hemmelighed  | meget hemmelig hemmelighed |  1 | kun admin kan se indholdet af denne celle  |  
 hemmelighed2 | hemmeligste hemmelighed    |  3 | denne secret er synlig for user2           | user2  
 hemmelighed1 | hemmeligere hemmelighed    |  2 | kun admin kan se indholdet af denne secret | user1  
```
messages:
```
 message_title  |  message_description  | id |         created_at         | role_privilege  
----------------+-----------------------+----+----------------------------+----------------  
 sød besked     | meget sød besked      |  1 | 2024-03-14 17:35:15.423276 |  
 hej            | hej med dig           |  5 | 2024-03-14 17:35:15.423276 |  
 hej            | hej med dig           |  4 | 2024-03-14 17:35:15.423276 | user1  
 hej            | hej med dig           |  6 | 2024-03-14 17:35:46.551106 | user2  
 uhøflig besked | føler du dig truffet? |  2 | 2024-03-14 17:35:15.423276 | user1  
 neutral besked | no hard feelings      |  3 | 2024-03-14 17:35:15.423276 | user2  
```
PRIVILEGES

for user1

&nbsp;&nbsp;  products  
&nbsp;&nbsp;  can only:  
&nbsp;&nbsp;  - select and see columns 'title', 'description', and 'price' in products with the value 'user1' in the 'created_by' column. (all products created by  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;user1 are visible to user1)  
&nbsp;&nbsp;  - create, insert, and update the same columns

&nbsp;&nbsp;  messages  
&nbsp;&nbsp;  can only:  
&nbsp;&nbsp;  - select and see columns 'message_title' and 'message_description', in messages with the value 'user1' in the column 'role_privilege'

&nbsp;&nbsp;  secrects  
&nbsp;&nbsp;  can only:  
&nbsp;&nbsp;  - select and see a single cell in the  column "limited_visibility"  
&nbsp;&nbsp;  - can see the columns secret_title and secret_description in rows with 'user1' in the 'role_privilege' column

for user2  

&nbsp;&nbsp;  products  
&nbsp;&nbsp;  can only:  
&nbsp;&nbsp;  - select and see title and description columns of all rows

&nbsp;&nbsp;  messages  
&nbsp;&nbsp;  can only:  
&nbsp;&nbsp;  - select and see columns 'message_title' and 'message_description', in the rows with the value 'user1' in the column 'role_privilege'

&nbsp;&nbsp;  secrets  
&nbsp;&nbsp;  can only:  
&nbsp;&nbsp;  - view the columns 'secret_title' and 'secret_description' of one single row

1a. (windows) download postgresql: https://www.postgresql.org/download/windows/  
1b. (mac) https://www.postgresql.org/download/macosx/  
2. launch installer and install only "Command Line Tools"  
3. (windows) add postgres to path in system environment variables  
&nbsp;&nbsp;3.1. type 'env' from search bar  
&nbsp;&nbsp;3.2. open 'edit the system environment variables'  
&nbsp;&nbsp;3.3. click button 'Environment Variables'  
&nbsp;&nbsp;3.4. mark 'path' under 'user variables for 'yourUsername'  
&nbsp;&nbsp;now you should be able ot use psql in any local cli!  
4. connect to database with user1: psql -h 51.120.12.130 -p 5432 -d testdb -U user1  
5. insert password: 'parsewoard123'  
6. play around with sql commands  
7. now try with user2  

How the granular access to the database was setup (not a full demonstration)

Privileges to specific columns was granted to the specified user with grant. Following statement is just an example to illustrate the use of it:

CRANT SELECT, UPDATE (message_title, message_description) to user1

Row level security ensures granular access to rows in the tables, and was enabled for each table:

'ALTER TABLE messages ENABLE ROW LEVEL SECURITY'

row level security ensures conditional access to specific users.

the following statement gives user1 select privilige to see the limited_visibility column in secrets table, in the row where id=2:

CREATE POLICY user1_see_limited ON secrets FOR select TO user1 USING (limited_visibility = 'kun admin kan se indholdet af denne secret');

Here, the policy is created on the row with the condition where limited_visibility = 'kun admin kan se indholdet af denne secret'.
the user is only granted select privilege on limited_visibility column.
this shows that the user can only see a single cell in the table.
In creating policies, USING is used for SELECT, DELETE and UPDATE, and WITH CHECK is used to apply the INSERT statement, 

Insert policy is created to user1, and user1 is thereforethe only one able to insert rows in products:

CREATE POLICY insert_products_policy ON products FOR INSERT TO user1;

It's important to note that with a combination of column level security (GRANT) and row level security with (CREATE POLICY), you can restrict access for a user, to just one single or more cells in the table. By appliying these features, you'll be able to achieve granular access down to single specific cells in tables.
These are just a few of many statements used for demonstrating how the granular security was setup for the database.
