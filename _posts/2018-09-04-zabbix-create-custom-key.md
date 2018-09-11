---
title:  "[zabbix] Custom Key 생성 방법(UserParameter)"
tags: zabbix mysql monitoring
categories: zabbix
toc: true
---
> 신규 Item Key 생성 및 테스트 진행

## Command 작성
예제는 `mysql -V` Command를 통해 신규 Item Key 등록 합니다. 
``` shell
# mysql -V
mysql  Ver 15.1 Distrib 5.5.56-MariaDB, for Linux (x86_64) using readline 5.1
```
**작성 유의사항**
Command 결과는 실 데이터만 존재 해야 합니다. 
예) CPU 사용율 : 50% => X, 50 => O
{: .notice--warning}

## UserParameter.conf 설정
새로운 UserParameter.conf 파일에 Command를 입력합니다. 

형식 : UserParameter=key,command
{: .notice--info}

``` shell
vi /etc/zabbix/zabbix_agentd/UserParameter_MySQL.conf

UserParameter=mysql.version,mysql -V
```

## Agent 재시작
``` shell
service zabbix-agent restart
```

## 테스트
``` shell
# zabbix_agentd -t mysql.version
mysql.version [t|mysql  Ver 15.1 Distrib 5.5.56-MariaDB, for Linux (x86_64) using readline 5.1]
```

신규 Item Key는 설정한 Agent에서만 동작하므로 필요한 모든 Agent에 설정이 필요합니다.
{: .notice--danger}

## Reference
* [https://www.zabbix.com/documentation/3.2/manual/config/items/userparameters](https://www.zabbix.com/documentation/3.2/manual/config/items/userparameters)
