fixed bug #82 createTableIfNotExists and indexes http://sourceforge.net/p/ormlite/bugs/82/


when constructing the CREATE TABLE statement
original solution:
after adding the PRIMARY KEY statement:
`CREATE TABLE IF NOT EXIST table_name
...columns...
PRIMARY KEY (id)`

it concats:
`,CREATE INDEX index_name ON column name;`

which essentially makes it two separate SQL queries. First it creates the table, then it adds the index.
It works as expected - creates the table and adds the index - unless a table already exists with the appropriate.
In such case, the first statement would do nothing, while the second statement still tries to create an index, which would subsequently fail.

my solution:
instead of adding another query after CREATE TABLE, I moved ADD INDEX inside of the first statement. Therefore,

`CREATE TABLE IF NOT EXIST table_name
...columns...
PRIMARY KEY (id)`

then

`,KEY index_name (column_name);`

Therefore, if table exists, the entire statement would be skipped, thus, no table nor index will be created.

THINGS TO NOTE:
1. Currently implementation only checks for index=true data field annotation for adding indexes.
2. If table pre-exists without index, the entire statement would still be skipped, thus no index would be added.
3. Only tested with MySQL.