# postgres-study
study postgresql
## install
system: Centos8
arch: x86

### 1、安装
官网的安装方法如下：

```bash
# 可以去 https://www.postgresql.org/download/linux/redhat/ 先参考一下官方的安装方法
# 可以先去 https://download.postgresql.org/pub/repos/yum/reporpms 下去找适合的安装包选择性的安装
# Install the repository RPM:
dnf install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm

# Disable the built-in PostgreSQL module:
dnf -qy module disable postgresql

dnf install -y postgresql13-server

# 然后初始化数据库
/usr/pgsql-13/bin/postgresql-13-setup initdb

systemctl enable postgresql-13

systemctl start postgresql-13

```
目前从理论上来说我们已经安装成功了，账号密码都还没有设置好的
在本机访问5432端口成功，但在其它机器还访问不了

### 防火墙开放端口
```bash
firewall-cmd --zone=public --add-port=5432/tcp --permanent
firewall-cmd --reload
```
注意，如果是云服务器还可能会要在云服务器上开放对应端口


### 配置允许远程访问

```bash
# 此处的配置文件位置需要自己工具具体版本找，此版本就是这个位置
vim /var/lib/pgsql/13/data/postgresql.conf
# 找到 listen_addresses 关键字先然后在其下一行加入一行 listen_addresses = '*' 
# 再
# 找到 listen_addresses 关键字先然后在其下一行加入一行 listen_addresses = '*' 
# 再重启 服务
systemctl restart postgresql-13
# 此时就可以连通 5432 端口了，但是还不能访问数据库服务
```
### 配置账号密码
先修改配置文件
```bash
vim  /var/lib/pgsql/13/data/pg_hba.conf
# 加一行到 IPv4 local connections:
host    all             all             0.0.0.0/0               scram-sha-256

```
### 修改密码
```bash
# 如下命令进入 psql 内
sudo -u postgres psql
# 进入到 psql 内之后
alter user postgres with password 'pg@pwsd123456';
# 注意最后的分号
\q
# 此命令是退出 psql 的
# 再重启
systemctl restart postgresql-13

```

此时已经可以远程访问到此数据库服务了
