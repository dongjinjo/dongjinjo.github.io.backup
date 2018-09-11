---
title:  "[zabbix] MySQL Key 생성 및 Item 설정"
tags: zabbix mysql monitoring
categories: zabbix
toc: true
---
> Zabbix의 MySQL용 Key 생성 및 Item 설정 방법

## MySQL 인증 설정
``` shell
# /var/lib/zabbix/.my.cnf
[mysql]
user=''
password=''
host='ip addr'

[mysqladmin]
user=''
password=''
host='ip addr'
```

## UserParameter.conf 추가
userParameter_mysql.conf
``` shell
UserParameter=mysql.status[*],echo "show global status where Variable_name='$1';" | HOME=/var/lib/zabbix mysql -N | awk '{print $$2}'

UserParameter=mysql.size[*],bash -c 'echo "select sum($(case "$3" in both|"") echo "data_length+index_length";; data|index) echo "$3_length";; free) echo "data_free";; esac)) from information_schema.tables$([[ "$1" = "all" || ! "$1" ]] || echo " where table_schema=\"$1\"")$([[ "$2" = "all" || ! "$2" ]] || echo "and table_name=\"$2\"");" | HOME=/var/lib/zabbix mysql -N'

UserParameter=mysql.version,mysql -V
UserParameter=mysql.ping,HOME=/var/lib/zabbix mysqladmin ping | grep -c alive
```

## Item Key 설정 값

| name                           | key                          |
|--------------------------------|------------------------------|
| status                         | mysql.ping                   |
| bytes received per second      | mysql.status[bytes_received] |
| bytes sent per second          | mysql.status[bytes_sent]     |
| begin operations per second    | mysql.status[com_begin]      |
| commit operations per second   | mysql.status[com_commit]     |
| delete operations per second   | mysql.status[com_delete]     |
| insert operations per second   | mysql.status[com_insert]     |
| rollback operations per second | mysql.status[com_rollback]   |
| select operations per second   | mysql.status[com_select]     |
| update operations per second   | mysql.status[com_update]     |
| queries per second             | mysql.status[questions]      |
| slow queries                   | mysql.status[slow_queries]   |
| uptime                         | mysql.status[uptime]         |
| version                        | mysql.version                |
| disk used(%)                   | vfs.fs.size[/var/lib/mysql   |
