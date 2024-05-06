# 日常命令笔记

 ## 1.inux

**vim替换**

```bash
:%s/\/opt\/idc\/apps\/eos-8\.2\.6-app\/server/\/home\/primeton\/app\/eos-8\.2-app\/server/g 
```

**文件内容查找-包含文件夹**

```bash
find /home/primeton -name "*" | xargs grep "12800" | grep "12800"
```

**文件内容查找-只查找文件**

```bash
find /home/primeton -type f -name "*" | xargs grep "T_DW_MATER_MIXPROPOR_COUNT" | grep "T_DW_MATER_MIXPROPOR_COUNT"

find /home/primeton/di7.0/server/diserver/project -type f -name "*.*" | xargs grep "T_DWD_PRESPOT_CHECK_RATE*" | grep "T_DWD_PRESPOT_CHECK_RATE"
```

 **日志查看**

> 查看frps服务的今天的日志 

```shell
journalctl -u frps --since "today"
```

 **linux上连接其他mysql**

> centos为例，切没有安装mysql

1、安装mysql 客户端

```shell
yum install  mysql
```

2、连接mysql

```shell
mysql -h 192.168.1.23 -P 13306 -u username -p
```



## 2.docker 

**安装homeassistant:**

```
docker run -d  --name="hass" -v E:\data\appsData\docker\homeassistant\config:/config -p 8123:8123 homeassistant/home-assistant:latest 
```

**安装mysql:**

```shell
docker run -d -p 3306:3306 --privileged=true -v /data/apps/mysql/log:/var/log/mysql -v /data/apps/mysql/data:/var/lib/mysql -v /data/apps/mysql/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=123456 --name mysql mysql:5.7
```

进入mysql容器

```shell
docker exec -it mysql bash
```

**安装redis**

> 先下载redis.conf到准备挂载的路径
>
> 下载地址：
>
> [https://github.com/redis/redis/blob/7.0/redis.conf]: 
>
> 注意下载对应的docker redis版本,此处我用redis:7.0.0.0-alpine3.16,下载的redis.conf对应版本为7.0，否则可能无法启动

```bash
docker run -p 6379:6379 --name redis -v D:/data/appsDatas/code/docker/redis/redis4Data/conf/redis.conf:/etc/redis/redis.conf -v D:/data/appsDatas/code/docker/redis/redis4Data/data:/data -d redis:7.0.0.0-alpine3.16 redis-server /etc/redis/redis.conf --requirepass 123456 --appendonly yes
```



**安装 frpc**

```bash
docker run --restart=always --network host -d -v /data/apps/frp:/etc/frp/frpc.ini --name frpc snowdreamtech/frpc
```

**容器管理Portainer：

#启动Portainer

```bash
docker run -dit -p 9000:9000 -v /data/apps/portainer:/data -v /var/run/docker.sock:/var/run/docker.sock --name portainer portainer/portainer:latest
```

**SqlServer安装**

```bash
docker run -d --restart=always --name=serverstatus -v ~/serverstatus-config.json:/ServerStatus/server/config.json -v ~/serverstatus-monthtraffic:/usr/share/nginx/html/json -p 8881:80 -p 35601:35601 cppla/serverstatus:latest
```

​    

**安装 nginx**

```bash
docker run --name nginx-test -p 8080:80 -v E:\data\appsData\docker\nginx\conf:/etc/nginx/conf -v E:\data\appsData\docker\nginx\conf.d:/etc/nginx/conf.d -v E:\data\appsData\docker\nginx\logs:/var/log/nginx -v E:\data\appsData\docker\nginx\html:/usr/share/nginx/html -d nginx
```

**安装直播推流 kplayer**

```bash
docker run  -td --name=kplayer2 -v /data/videos:/video -v  /data/apps/kplayer2/config.json:/kplayer/config.json -v/data/apps/kplayer2/cache:/kplayer/cache  --restart=always  bytelang/kplayer:latest
```

  

## 3.数据库：

**mysql修改密码**

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456'; --5.6

update mysql.user set authentication_string=password('mysql1991Pswd') where user='root';  --5.7
```

**mysql表空间管理**

> 表空间占比情况查询

```sql
SELECT a.tablespace_name "FNC_BAK_SPACE ",a.bytes / 1024 / 1024 /1023 "表空间大小(GB)",(a.bytes - b.bytes) / 1024 / 1024 / 1024  "已使用空间(GB)",
b.bytes / 1024 / 1024 "空闲空间(M)",round(((a.bytes - b.bytes) / a.bytes) * 100, 2) "使用比"
FROM (SELECT tablespace_name, sum(bytes) bytes
FROM dba_data_files GROUP BY tablespace_name) a,
(  SELECT tablespace_name, sum(bytes) bytes, max(bytes) largest
   FROM dba_free_space
   GROUP BY tablespace_name
) b
WHERE a.tablespace_name = b.tablespace_name
ORDER BY ((a.bytes - b.bytes) / a.bytes) DESC;
```

> 表空间file文件情况查询

```SQL
select tablespace_name, file_id,file_name, round(bytes/(1024*1024),0) total_space from dba_data_files t WHERE t.TABLESPACE_NAME  = 'SYSTEM' order by tablespace_name;
```

> 创建临时表空间

```sql
CREATE temporary  tablespace lowcode_bps_temp  tempfile '/home/oracle/oradata/appdb/lowcode/bps/lowcode_bps_temp01.dbf'  SIZE 32M AUTOEXTEND ON NEXT 32M MAXSIZE 4096M;
--创建表空间
CREATE   tablespace lowcode_bps  datafile '/home/oracle/oradata/appdb/lowcode/bps/lowcode_bps01.dbf'  SIZE 32M AUTOEXTEND ON NEXT 32M MAXSIZE 4096M;
```

> 表空间增加file文件

```sql
alter tablespace SIP_DATA_TABLESPACE add datafile  '/home/oracle/oradata/appdb/sip/data16.dbf'  SIZE 32M AUTOEXTEND ON NEXT 32M MAXSIZE 4096M;

--临时表空间查询

select c.tablespace_name,

to_char(c.bytes/1024/1024/1024,'99,999.999') total_gb,

to_char( (c.bytes-d.bytes_used)/1024/1024/1024,'99,999.999') free_gb,

to_char(d.bytes_used/1024/1024/1024,'99,999.999') use_gb,

to_char(d.bytes_used*100/c.bytes,'99.99') || '%'use

from  (select tablespace_name,sum(bytes) bytes

from dba_temp_files GROUP by tablespace_name) c,

(select tablespace_name,sum(bytes_cached) bytes_used

from v$temp_extent_pool GROUP by tablespace_name) d

where c.tablespace_name = d.tablespace_name;
```

> 表空间使用者查询

```sql
select a.username,

   a.sql_id,

   a.SEGTYPE,

   b.BYTES_USED/1024/1024/1024||'G',

   b.BYTES_FREE/1024/1024/1024  from   V$TEMPSEG_USAGE  a  join  V$TEMP_SPACE_HEADER b on   a.TABLESPACE=b.tablespace_name; 
```

> LAG() 与 LEAD（）函数，常用与计算同比环比:其实就是排序号后上下错位，用哪个函数取决排序

```sql
   select 
     test.id,
     test.month, --202311
     test.value,
     lag(test.value) over (order by test.id,test.month) as PRE_MONTH_VALUE, --上个月的值
     test.value - ag(test.value) over (order by test.id,test.month) as diff_to_last_month  --得到与上个月的差额，
     lead(test.value) over (order by test.id,test.month) as forward_MONTH_VALUE --lead刚好相反，能拿到下个月的值，计算差额同理
   from test
   order by 
    test.id,
    test.month desc, --202312到202301每个月一条数据
    test.value

```
