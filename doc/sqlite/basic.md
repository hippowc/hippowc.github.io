1 在命令行输入 sqlite3 即可进入sqlite命令界面，也可以使用sqlite3 xxx.db直接创建数据库，后面的文件名就是数据库名称（一个普通文件）
sqlite命令以 分号 结尾，sqlite有很多 .命令，一些基本的如下：
.show，查看sqlite的配置
.header on, .mode column：格式化输出，表格样式
.schema 表名称，可以查看这个表的基本信息

2 数据库操作
todo

3 创建表
create table xxx (
  column1 datatype PRIMARY KEY AUTOINCREMENT,
  column2 text not null,
  ...
);
这时id信息是自动增长的，在插入的时候id表示为null即可。
