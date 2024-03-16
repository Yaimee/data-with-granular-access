# data-with-granular-access-readme

PostgreSQL

This PostgreSQL database was setup with user privileges and Row Level Security

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

``` secret_title |     secret_description     | id |             limited_visibility             | role_privilege  
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

  products
  can only:
  - select and see columns 'title', 'description', and 'price' in products with the value 'user1' in the 'created_by' column. (all products created by user1 are visible to 
    user1
  - create, insert, and update the same columns

  messages
  can only:
  - select and see columns 'message_title' and 'message_description', in messages with the value 'user1' in the column 'role_privilege'

  secrects
  can only:
  - select and see a single cell in the  column "limited_visibility"
  - can see the columns secret_title and secret_description in rows with 'user1' in the 'role_privilege' column

for user2

  products
  can only: 
  - select and see title and description columns of all rows

  messages
  can only:
  - select and see columns 'message_title' and 'message_description', in the rows with the value 'user1' in the column 'role_privilege'

  secrets
  can only:
  - view the columns 'secret_title' and 'secret_description' of one single row

1a. (windows) download postgresql: https://www.postgresql.org/download/windows/
1b. (mac) https://www.postgresql.org/download/macosx/
2. launch installer and install only "Command Line Tools"
3. (windows) add postgres to path in system environment variables
  3.1. type 'env' from search bar
  3.2. open 'edit the system environment variables'
  3.3. click button 'Environment Variables'
  3.4. mark 'path' under 'user variables for <your username>'
  now you should be able ot use psql in any local cli!
4. connect to database with user1: psql -h 51.120.12.130 -p 5432 -d testdb -U user1
5. insert password: 'parsewoard123'
6. play around with sql commands
7. now try with user2
