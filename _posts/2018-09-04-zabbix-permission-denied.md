---
title:  "[zabbix] log file 모니터링 시 Permission Denied 오류 수정"
tags: zabbix monitoring
categories: zabbix
toc: true
---
> zabbix를 이용한 log file 모니터링 시 발생 하는 Permission Denied 오류 수정

## 현상
Item(`log`) 등록 후 Permission Denied 오류 발생 
![img01]({{ "/assets/images/2018-09-04-zabbix-permission-denied-03.png" | absolute_url }})

## 원인
`zabbix` 계정이 해당 file에 접근 할 수 없어 Permission Denied 오류 발생.

zabbix agent를 통해 수행 되는 명령은 `zabbix` 계정으로 수행
{: .notice--info}

## 해결 방안
log file 그룹에 `zabbix` 추가.
###  1. zabbix 계정 권한 및 그룹 확인
``` shell
# id zabbix 
uid=129(zabbix) gid=135(zabbix) groups=135(zabbix)
```

###  2. log file 계정 및 그룹 확인
``` shell
# ls -al /var/log/mysql/mysqld.log
-rw-r----- 1 mysql adm 0 Sep  4 00:00 /var/log/mysql/mysqld.log
```

###  3. log file 그룹(adm)에 zabbix 계정 추가
``` shell
# usermod -G adm zabbix
# id zabbix
uid=129(zabbix) gid=135(zabbix) groups=135(zabbix),4(adm)
```
