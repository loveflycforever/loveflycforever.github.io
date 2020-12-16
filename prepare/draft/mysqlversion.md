5.0.x    5.0.96    Mar 2, 2012
5.1.x    5.1.73    Nov 8, 2013

收购后
5.5.x    5.5.62    Aug 29, 2018
5.6.x    5.6.49    Jun 2, 2020
5.7.x    5.7.31    Jun 2, 2020
8.0.x    8.0.21    Jun 16, 2020


5.7  新特性 
增加JSON支持
Generated Column是MySQL 5.7引入的新特性，所谓Cenerated Column，就是数据库中这一列由其他列计算而得
例如，知道直角三角形的两条直角边，要求斜边的长度。很明显，斜边的长度可以通过两条直角边计算而得，那么，这时候就可以在数据库中只存放直角边，斜边使用Generated Column
在MySQL 5.7中，支持两种Generated Column，即Virtual Generated Column和Stored Generated Column，前者只将Generated Column保存在数据字典中（表的元数据），并不会将这一列数据持久化到磁盘上；后者会将Generated Column持久化到磁盘上，而不是每次读取的时候计算所得。很明显，后者存放了可以通过已有数据计算而得的数据，需要更多的磁盘空间，与Virtual Column相比并没有优势，因此，MySQL 5.7中，不指定Generated Column的类型，默认是Virtual Column。
一般情况下，都使用Virtual Generated Column，这也是MySQL默认的方式，如果使用Stored Generated Column，前面的建表语句将会是下面这样，即多了一个stored关键字：

 

Create Table: CREATE TABLE `triangle` (

`sidea` double DEFAULT NULL,

`sideb` double DEFAULT NULL,

`sidec` double GENERATED ALWAYS AS (SQRT(sidea * sidea + sideb * sideb)) STORED)


可以在generated column上建立索引，建立索引以后，能够加快查找速度


聚集索引不能包含virtual generated column 

不能在Virtual Generated Column上创建全文索引和空间索引

Virtual Generated Column不能作为外键

创建generated column(包括virtual generated column 和stored generated column)时不能使用非确定性的（不可重复的）函数，比如，curtime()

先新建一个Generated Column，然后再在这个Generated Column上建索引
