# 日常命令笔记

## 1、linux

**vim替换**
:%s/\/opt\/idc\/apps\/eos-8\.2\.6-app\/server/\/home\/primeton\/app\/eos-8\.2-app\/server/g

**文件内容查找-包含文件夹**
find /home/primeton -name "*" | xargs grep "12800" | grep "12800"
**文件内容查找-只查找文件**


find /home/primeton -type f -name "*" | xargs grep "T_DW_MATER_MIXPROPOR_COUNT" | grep "T_DW_MATER_MIXPROPOR_COUNT"

find /home/primeton/di7.0/server/diserver/project -type f -name "*.*" | xargs grep "T_DWD_PRESPOT_CHECK_RATE*" | grep 

## 2、docker 

**安装mysql:**
docker run -d -p 3306:3306 --privileged=true -v /data/apps/mysql/log:/var/log/mysql -v /data/apps/mysql/data:/var/lib/mysql -v /data/apps/mysql/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=123456 --name mysql mysql:5.7
进入mysql容器
docker exec -it mysql bash

**安装 frpc**

docker run --restart=always --network host -d -v /data/apps/frp:/etc/frp/frpc.ini --name frpc snowdreamtech/frpc

**容器管理Portainer：**
#启动Portainer
docker run -dit -p 9000:9000 -v /data/apps/portainer:/data -v /var/run/docker.sock:/var/run/docker.sock --name portainer portainer/portainer:latest

**SqlServer安装**

docker run -d --restart=always --name=serverstatus -v ~/serverstatus-config.json:/ServerStatus/server/config.json -v ~/serverstatus-monthtraffic:/usr/share/nginx/html/json -p 8881:80 -p 35601:35601 cppla/serverstatus:latest    

**安装 nginx**

docker run --name nginx-test -p 8080:80 -v E:\data\appsData\docker\nginx\conf:/etc/nginx/conf -v E:\data\appsData\docker\nginx\conf.d:/etc/nginx/conf.d -v E:\data\appsData\docker\nginx\logs:/var/log/nginx -v E:\data\appsData\docker\nginx\html:/usr/share/nginx/html -d nginx

**安装直播推流 kplayer**
docker run  -td --name=kplayer2 -v /data/videos:/video -v  /data/apps/kplayer2/config.json:/kplayer/config.json -v/data/apps/kplayer2/cache:/kplayer/cache  --restart=always  bytelang/kplayer:latest  

## 3、数据库：

**mysql修改密码**

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456'; --5.6

update mysql.user set authentication_string=password('mysql1991Pswd') where user='root';  --5.7

docker run -d --restart=always --name=serverstatus -v ~/serverstatus-config.json:/ServerStatus/server/config.json -v ~/serverstatus-monthtraffic:/usr/share/nginx/html/json -p 8881:80 -p 35601:35601 cppla/serverstatus:latest     
