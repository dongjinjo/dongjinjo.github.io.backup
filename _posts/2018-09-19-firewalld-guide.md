---
title:  "방화벽(firewalld) 설정"
tags: firewalld firewall-cmd
categories: firewall
toc: true
---

> 방화벽(firewalld) 설정 가이드

## 서비스 제어

| 명령어                  | 설명         |
|-------------------------|--------------|
| service firewalld start | service 시작 |
| service firewalld stop  | service 중지 |
| firewall-cmd \--reload   | 재시작       |

## 목록 조회

| 명령어                             | 설명                   |
|------------------------------------|------------------------|
| firewall-cmd \--list-all            | default zone 설정 정보 |
| firewall-cmd \--list-all-zones      | 전체 zone 설정 정보    |
| firewall-cmd \--list-all --zone=dmz | 특정 zone 설정 정보    |
| firewall-cmd \--get-zones           | zone 목록              |
| firewall-cmd \--get-default-zone    | default zone 이름      |
| firewall-cmd \--get-services        | 사전 정의 service 목록 |

## 추가 & 삭제

**\--zone=\<zone>**
\--zone=\<zone> 옵션이 없을 경우 default zone에 추가, 삭제 됩니다.
{: .notice--info}

{% capture notice-2 %}
**\--permanent**
firewall-cmd 로 방화벽 정책을 변경 시  즉시 적용되지만 reload 하거나 재부팅 할 경우 정책이 초기화됩니다.
방화벽 정책을 영구적으로 적용하기 위해서는 \--permanent 옵션을 사용해야 합니다. 옵션을 사용할 경우 즉시 적용 되지 않아 reload를 수행해야 합니다. 

|               | --permanent 옵션 사용 | --permanent 옵션 미사용 |
|:-------------:|:---------------------:|:-----------------------:|
|   즉시 적용   |           X           |            O            |
| 정책 영구적용 |           O           |            X            |
{% endcapture %}

<div class="notice--info">{{ notice-2 | markdownify }}</div>


| 명령어                                       | 설명              |
|----------------------------------------------|-------------------|
| firewall-cmd \--add-service=ssh              | ssh service 추가  |
| firewall-cmd \--remove-service=ssh           | ssh service 삭제  |
| firewall-cmd \--add-port=21/tcp              | tcp(21) port 추가 |
| firewall-cmd \--remove-port=21/tcp           | tcp(21) port 삭제 |
| firewall-cmd \--add-source=192.168.0.0/24    | source ip 추가    |
| firewall-cmd \--remove-source=192.168.0.0/24 | source ip 삭제    |

## rich rule

### rich rule 규칙

``` shell
rule
  [family="ipv4|ipv6"]
  [source [not] address="address[/mask]"|mac="mac-address"|ipset="ipset"]
  [destination [not] address="address[/mask]]
  service|port|protocol|icmp-block|icmp-type|masquerade|forward-port|source-port
  [log]
  [audit]
  [accept|reject|drop|mark]
```

| 명령어                                    | 설명           |
|-------------------------------------------|----------------|
| firewall-cmd \--add-rich-rule='rule ~'    | rich rule 추가 |
| firewall-cmd \--remove-rich-rule='rule ~' | rich rule 삭제 |

### rich rule 예제

#### 특정 대역에 대한 모든 연결 허용

``` shell
firewall-cmd --add-rich-rule='rule family="ipv4" source address="192.168.0.0/24" accept'
```

#### 특정 IP에 대한 모든 연결 거부
``` shell
firewall-cmd --add-rich-rule='rule family="ipv4" source address="192.168.0.10/32" reject'  
```

#### 특정 대역에 대한 22 port 연결 허용  
``` shell
firewall-cmd --add-rich-rule='rule family="ipv4" source address="192.168.0.0/24" port port="22" protocol="tcp" accept'
```
