## PostgreSQL

> - 基础配置
>
>   ```shell
>   # 默认工具位置
>   /usr/pgsql-16/bin
>   # 默认数据库及文件位置
>   /var/lib/pgsql/16
>   # 开启远程访问
>   vim postgresql.conf
>    -- listen_addresses = '*'
>   vim pg_hba.conf
>   -- host all all 0.0.0.0/0 scram-sha-256
>   # 连接数据库
>   su postgres
>   psql -h <ip> -p <port> -U <user> -d <database>
>   # 切换数据库
>   \c initdb
>   # 切换schema(表空间)
>   set search_path to 'tablespace_name';
>   \d # 查看当前数据库中表，视图，序列
>   \db # 查看表空间
>   \du # 查看用户
>   \dS # 查看表分区情况
>   \timing # 开启查询时间
>   \l # 查看数据库
>   
>   ```
>
> - 数据库操作
>
>   ```postgresql
>   -- Tablespace： 文件系统定义那些存储数据库对象的文件存放的位置
>   create tablespace space_name location '/path';
>   
>   -- 查看当前数据库表空间
>   select * from pg_tablespace;
>   
>   -- 移动表空间下对象(移动期间涉及的对象将被锁定)
>   alter table all in tablespace old_space set tablespace new_space;
>   
>   -- 创建分区表  
>   create table table_name () partition by {list | range }(field_name) tablespace table_space;
>   
>   -- 添加字段注释
>   comment on column table_name.column is 'text';
>   
>   -- 非空约束 NOT NULL。 唯一约束 UNIQUE， 主键约束 PARIMAEY KEY，默认约束 DEFAULT
>   -- check 约束，约束字段范围
>   check(column_1 > 'value' | column_2)
>   
>   -- 创建自增序列
>   create sequence seq_name start with 1 increment by 1 no minvalue no maxvalue cache 1;
>   -- 查看seq
>   select nextval('seq_name');
>   
>     -- 创建分区表（crud操作会自动路由到相应的分区表，主表字段操作会同步到分表，索引操作不会同步到分表）
>     create table table_name_partition partition of table_name for values from ('start_value') to ('end_value');
>   
>     create table table_name_partition partition of table_name for in ('values')  
>   
>     -- 查询数据所在分区
>     select v.tableoid::regclass,v.* from table_main;
>   
>     -- 解除分区表限制
>     alter table t_main detach partition on  t_partiion;
>   
>     -- 继承表 （只继承字段,不继承主键，唯一约束和数据）
>     create table t_name(column) inherits(parent_t_name);
>   
>     -- 单查询父表( only限制只操作父表，不使用only则会查询所有子表所有数据union) 
>     select * from only parent_t_name ;
>   
>     --解除继承关系
>     alter table sub_table no inherit parent_table;
>   ```
>
> - 数据类型及运算符
>
>     ```postgresql
>     -- 整数
>     smallint/integer/bigint
>     
>     -- 浮点数
>     numeric/decimal 
>     --字符类型
>     char(n)/varchar(n)/text
>     -- 支持的运算符
>     ||：字符拼接， like  similar to  ～ ：模糊查询
>     
>     -- 时间类型 支持基本的算术运算符
>       timestamp/date/time/interval
>     
>     -- 布尔类型  true/false/unkonw
>       boolean
>     
>     -- 枚举
>       create type week as enum('value');
>     
>     -- 几何类型
>     point/line/lseg/box/path/polygon/circle
>     -- line/lesg '{A,B,C}' line '(x,y)(x,y)'
>     
>     -- JSON数据类型
>     json/jsonb(支持索引)
>     select '{"key":"value"}'::jsonb;
>     -- 支持的运算符
>     json->'key | index ' -- 获取json下标为index 或key的元素
>     json ->> ''          -- 输出为text 或 int
>     json #> '{}'         -- 嵌套读取json 
>     json #>> '{}'				 -- 嵌套读取json,输出为文本
>     
>     jsonb ? 'key'        -- key是否存在最外层json中
>     jsonb ?| array['key']  -- key是否存在最外层json中
>     jsonb || jsonb        -- 合并两个json
>     jsonb - ''            -- 删除指定的key
>     
>     --范围数据类型
>     int8range/numberange/tsrange/daterange
>     --范围值输入 '[start,end]' / '(start,end)'
>     --支持的运算符 
>     --  @> 包含 <@ 被包含
>     --  -|- 是否相邻
>     --  && 是否重叠
>     --  + * - 并交差
>     
>     -- 数组类型,支持切片访问
>     array['value'] / '{"str","str2"}'
>     --支持的运算符
>     -- || 拼接数组
>     ```
>
> - 常用函数
>
>   ```postgresql
>   -- 格式化字符串输出
>   -- type: %S:字符串占位， %I:双引号占位 ，%L:SQL占位
>   format('%[position][flags][with]type')
>   
>   -- json函数，将任意类型数据转为jsonb
>    to_jsonb(anyelement)
>   
>   -- 数组转json
>    array_to_json()/row_to_json()
>   
>   -- 返回当前数据库名称
>    select current_catalog; / select current_database();
>   
>   --返回当前schema（表空间）
>    select current_schema();
>   select current_user();
>   
>   --返回当前session信息
>   -- inet_client_addr()   //返回本机ip
>   -- inet_client_port()
>   -- inet_server_addr()  inet_server_port()
>   
>   -- 返回当前session连接pid
>   pg_backend_pid()
>   
>   -- 返回数据库配置
>   select current_setting('key')
>   ```
>
> - PostgreSQL特殊语法
>
>   ```postgresql
>    --任意表达式去重（类似于group by，返回每组第一行）
>   select distinct on (field_name,field_name_2);
>   
>   -- like 别名（操作符）
>   ~~ 等效 like
>   -- ilike 不区分大小写
>   ~~* 等效 ilike
>   
>   -- with : 临时表，用于复杂查询和递归查询
>     with recursive T(field_name) as (query_sql)
>   select * from T1 where T.field_name = T1.field_name
>   
>   -- 归并数据 replace 语法
>     on conflict do nothing  -- 不采取任何操作，忽略该行
>   on conflict do update   -- 采取更新操作
>   insert into (...value) on conflict(field_name) do update set field_name = 'value'
>   
>   -- 关联update语句 (在update语句中构造更改数据的临时表)
>     update T set column = update_column from (values ('update_value'),('update_value')) as T2(colum) where T.colum = T2.colum
>   
>   -- 批量删除(同上)
>     delete from T using (values)
>   
>   -- 视图
>     create view view_name as query_sql;
>   -- 临时视图
>   create temp view view_name as query_sql;
>   --物化视图（会缓存结果集，与原表数据无关，允许添加索引）
>   create materialized view view_name as query_sql;
>   -- 刷新物化视图
>   refresh materialized view view_name;
>   
>   -- 服务器上执行
>     -- copy 标准文件于表数据传输指令
>   -- 将查询结果或表输出到文件，使用""间隔
>   copy [(query_sql) | table]  to '/path/file' (delimiter ',');
>   -- 输出到csv
>   copy [(query_sql) | table]  to '/path/file' with csv;
>   -- 输出到控制台
>   copy table to stdout (delimiter,',')
>   
>   -- 导入命令 t1为临时表
>   copy t1(...column) from '/tmp/file' with csv header encoding 'GBK';
>   
>   -- 查看事务隔离级别
>   show transaction_isolation;
>   -- 更改事务级别
>   alter system set default_transaction_isolation to 'repeatable_read';
>   ```
>
> - **索引结构**
>
>   1. B-tree：用于排序，范围或等值查询
>
>   2. Hash：用于等值查询
>
>   3. GiST：用于几何字段（point/line/lseg/box/path/polygon/circle）查询
>
>   4. SP-GiST：对比GIST优化索引算法 [四叉树](https://blog.csdn.net/hyman_c/article/details/119315210)
>
>   5. GIN：倒排索引，处理复合字段（jsonb，array，text）
>
>   6. BRIN：同B-tree，作用于大数量的表
>
> - **索引类型**
>
>   1. 唯一索引
>   1. 多列索引
>   1. 主键
>   1. 函数索引（将函数计算结果构造索引）
>   1. 条件索引（约束字段指定范围构成索引）
>
> - SQL执行计划
>
>   explain [option] statement
>
>   option：
>
>   1. analyze：真实执行statement
>   2. verbose：查询计划额外信息
>   3. costs：估算执行成本
>   4. buffers：使用缓存情况
>   5. timing：实际花费时间
>   6. format：输出格式，text，xml，json，默认text
>
>   执行计划
>
>   ![image-20231203130443602](./assets/image-20231203130443602.png) 
>
>   cost：预计启动开销/总开销，actual time ：实际启动时间和运行启动时间
>
>   shared read：磁盘命中，shared hit：缓存命中

#### **数据库运维**

> - 服务端命令
>
>   1. clusterdb：基于堆表的物理文件重新排序，以使数据存储在物理上相邻的位置，节约磁盘访问次数，加快查询
> 2. reindexdb：对数据库或表重新生成索引
>   3. vacuumed：清除数据库中无用的物理文件
>
> - 数据库配置
>
>   1. pg_hba.conf：数据库实例防火墙配置
>
>      ![image-20240324154300918](./assets/image-20240324154300918.png) 
>
>   2. postgresql.conf：数据库配置文件
>
>       [postgresql.conf](./assets/postgresql.conf) 
>   
> - 备份与恢复
>
>   pg_dump/pg_restore：将数据库所有对象输入输出为sql或归档文件
>
>   pg_basebackup：将数据库实例进行备份
>
>   WAL归档：记录操作的SQL日志
>
>   1. achive_mode=on：开启日子文件归档备份
>   2. wal_level=replica：
>   3. achive_command='shell'：设置归档操作命令
>
> - 服务器配置参数
>
>   内存相关参数
>
>   1. shared_buffers：进程共享缓存容量（default：128mb，suggest：内存25%）
>   2. huge_pages：启用巨型内存页（try ｜ on ｜ off）
>   3. work_men：hash连接查询所需内存（default：4mb）
>
>   WAL日志参数
>
>   1. fsync：wal是否先写入磁盘（default：true，on｜off｜remote_apply | remote_write | local)
>
>   2. wal_sync_method：脏页写回磁盘的方式
>
>      open_datasync/open_sync
>
>      fdatasync/fsync：提交时调用系统函数写会磁盘
>
>      fsync_writethrough：提交时同时写入磁盘与缓存
>
>   查看服务器统计信息
>
>   ```postgresql
>   -- 查看pg活跃session
>   select * from pg_stat_activity;
>   -- 统计pg数据库信息/ all_tables
>   select * from pg_stat_database;
>   ```
>
> 

