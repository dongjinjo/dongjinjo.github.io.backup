---
title:  "[zabbix] MongoDB Key 생성 및 Item 설정"
tags: zabbix mongo monitoring
categories: zabbix
toc: true
---
> Zabbix의 MongoDB 용 Key 생성 및 Item 설정 방법


## Mongo 인증 설정
``` shell
# /var/lib/zabbix/.mongo.rc
function authRequired() {
  try {
    if (db.serverCmdLineOpts().code == 13) {
      return true;
    }
    return false;
  }
  catch (err) {
    return false;
  }
}

if (authRequired()) {
  try {
    rs.slaveOk()
    var prev_db = db
    db = db.getSiblingDB('')
    db.auth('', '')
    db = db.getSiblingDB(prev_db)
  }
  catch (err) {
    return;
  }
}
```


## UserParameter.conf 추가
userParameter_mongo.conf
``` shell
UserParameter=mongo.service,ps -ef | grep mongo | grep -v grep | wc -l
UserParameter=mongo.network[*],echo "db.serverStatus().network" | HOME=/var/lib/zabbix mongo | grep $1 | cut -d ":" -f 2 | cut -d "," -f1 | cut -d " " -f 2
UserParameter=mongo.opcounters[*],echo "db.serverStatus().opcounters" | HOME=/var/lib/zabbix mongo | grep $1 | cut -d ":" -f 2 | cut -d "," -f 1 | cut -d " " -f 2
UserParameter=mongo.mem_resident,echo "db.serverStatus().mem" | HOME=/var/lib/zabbix mongo | grep resident | cut -d ":" -f 2 | cut -d "," -f 1 | cut -d " " -f 2
UserParameter=mongo.mem_virtual,echo "db.serverStatus().mem" | HOME=/var/lib/zabbix mongo | grep virtual | cut -d ":" -f 2 | cut -d "," -f 1| cut -d " " -f 2
UserParameter=mongo.mem_mapped,echo "db.serverStatus().mem" | HOME=/var/lib/zabbix mongo | grep '\bmapped\b' | cut -d ":" -f 2 | cut -d "," -f 1 | cut -d " " -f 2
UserParameter=mongo.connection_current,echo "db.serverStatus().connections.current"| HOME=/var/lib/zabbix mongo | sed -n 3p
UserParameter=mongo.connection_available,echo "db.serverStatus().connections.available"| HOME=/var/lib/zabbix mongo | sed -n 3p
UserParameter=mongo.queue_write,echo "db.serverStatus().globalLock.currentQueue.writers" | HOME=/var/lib/zabbix mongo |sed -n 3p
UserParameter=mongo.queue_reader,echo "db.serverStatus().globalLock.currentQueue.readers" | HOME=/var/lib/zabbix mongo |sed -n 3p
UserParameter=mongo.cursors_totalOpen,echo "db.serverStatus().cursors.totalOpen" | HOME=/var/lib/zabbix mongo | sed -n 3p
UserParameter=mongo.cursors_timedOut,echo "db.serverStatus().cursors.timedOut" | HOME=/var/lib/zabbix mongo | sed -n 3p
UserParameter=mongo.backgroundFlush,echo "db.serverStatus().backgroundFlushing.last_ms" | HOME=/var/lib/zabbix mongo | sed -n 3p
UserParameter=mongo.rpstatus,echo "rs.status()"| HOME=/var/lib/zabbix mongo | grep myState| cut -d ":" -f 2 | cut -d "," -f 1 | cut -d " " -f 2
UserParameter=mongo.pagefaults,echo "db.serverStatus().extra_info.page_faults" | HOME=/var/lib/zabbix mongo | sed -n 3p
UserParameter=mongo.oplog_storetime,echo "db.printReplicationInfo()" | HOME=/var/lib/zabbix mongo | sed -n 4p | cut -d "(" -f 2 | cut -d "h" -f 1
```


## Item Key 설정 값

| name                 | key                                |
|----------------------|------------------------------------|
| connection available | mongo.connection_available         |
| connection current   | mongo.connection_current           |
| page faults          | mongo.pagefaults                   |
| queue reader         | mongo.queue_reader                 |
| queue write          | mongo.queue_write                  |
| service              | mongo.service                      |
| disk used(%)         | vfs.fs.size[/var/lib/mongo, pused] |


## Reference 
* [https://github.com/WuXianglong/Zabbix-Templates-Scripts/blob/master/mongo/mongo.conf](https://github.com/WuXianglong/Zabbix-Templates-Scripts/blob/master/mongo/mongo.conf)
* [https://share.zabbix.com/databases/mongodb/mongodb-for-zabbix-3-2](https://share.zabbix.com/databases/mongodb/mongodb-for-zabbix-3-2)
