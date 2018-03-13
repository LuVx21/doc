key:同时具有constraint和index的作用,即约束本列的数据和优化查询
index:仅具有优化查询的作用


mysqldump -u用戶名 -p密码 -d 数据库名 表名 > 脚本名;

导出整个数据库结构和数据
mysqldump -h localhost -uroot -p1121 tale > dump.sql

导出单个数据表结构和数据
mysqldump -h localhost -uroot -p1121  tale table > dump.sql

导出整个数据库结构（不包含数据）
mysqldump -h localhost -uroot -p1121  -d tale > dump.sql

数据(不含表结构)
mysqldump -h localhost -uroot -p1121  -t tale > dump.sql

导出单个数据表结构（不包含数据）
mysqldump -h localhost -uroot -p1121  -d tale table > dump.sql

1.导出指定表的数据

mysqldump -t database -u username -ppassword --tables table_name1 table_name2 table_name3 >D:\db_script.sql

2.导出指定表的结构

mysqldump -d database -u username -ppassword --tables table_name1 table_name2 table_name3>D:\db_script.sql

3.导出表的数据及结构

mysqldump  database -u username -ppassword --tables table_name1 table_name2 table_name3>D:\db_script.sql

4.若 数据中 ，某些表除外，其余表都需导出

mysqldump -h IP -u username -ppassword --default-character-set=utf8 --database database_name --ignore-table=database_name.table_name1

--ignore-table=database_name.table_name2 --ignore-table=database_name.table_name3 >D:\db_script.sql




mysql -u root -p --tee=/mysql/mysql_op.log

tee mysql_op.log

notee
