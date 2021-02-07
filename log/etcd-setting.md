---
title: "etcd 설정"
slug: 
description: null
date: 2021-02-08T02:20:00+09:00
type: posts
draft: false 
categories:
- til
tags:
- etcd 
series:
-
---

linux에서 etcd 클러스터 설정하기

How To Setup a etcd Cluster On Linux – Beginners Guide( https://devopscube.com/setup-etcd-cluster-linux/ ) 를 읽고해보는 요약

소개  
etcd는, 
- key-value 저장소 중 하나이다. 
- raft 프로토콜을 사용한다.
- rest/grpc를 이용한다.

준비물
- 3개의 linux 서버
- 각 서버의 호스트 이름
- 방화벽 개방(2380, 2379)

설정  
etc-1: 10.128.0.2  
etc-2: 10.128.0.4  
etc-3: 10.128.0.3  

으로 호스트 이름과 ip를 사용한다. (ip는 각 상황에 맞춰서 사용한다.)

각 서버 별로 1~6의 과정을 진행한다.  

1. src 폴더로 이동한다.
```
$ cd /usr/local/src
```

2. 최신 버전의 etcd를 다운로드 한다.
```
$ sudo wget "https://github.com/coreos/etcd/releases/download/v3.3.9/etcd-v3.3.9-linux-amd64.tar.gz"
```

3. tar를 푼다.
```
$ sudo tar -xvf etcd-v3.3.9-linux-amd64.tar.gz
```

4. 풀린 etcd 실행 파일들(etcd, etcctl)을 local/bin으로 이동한다.
```
$ sudo mv etcd-v3.3.9-linux-amd64/etcd* /usr/local/bin/
```

5. etcd 폴더와 사용자 그리고 그룹을 만든다. etcd 사용자로 etcd 서비스를 실행한다.
```
$ sudo mkdir -p /etc/etcd /var/lib/etcd
$ groupadd -f -g 1501 etcd
$ useradd -c "etcd user" -d /var/lib/etcd -s /bin/false -g etcd -u 1501 etcd
$ chown -R etcd:etcd /var/lib/etcd
```

6. root 사용자로 다음의 작업을 한다.   
  시스템 IP와 호스트 이름을 설정할 수 있는 두 개의 환경 변수를 설정한다.
```
$ ETCD_HOST_IP=$(ip addr show eth0 | grep "inet\b" | awk '{print $2}' | cut -d/ -f1)
$ ETCD_NAME=$(hostname -s)
```

etcd용 systemd service 파일을 만든다.  
--listen-client-urls

note)
–name, –initial-advertise-peer-urls, –listen-peer-urls, –listen-client-urls는 각 서버마다 다르다. 
ETCD_NAME 및 ETCD_HOST_IP 변수가 자동으로 대체된다. 

```
cat << EOF > /lib/systemd/system/etcd.service
[Unit]
Description=etcd service
Documentation=https://github.com/coreos/etcd
 
[Service]
User=etcd
Type=notify
ExecStart=/usr/local/bin/etcd \\
 --name ${ETCD_NAME} \\
 --data-dir /var/lib/etcd \\
 --initial-advertise-peer-urls http://${ETCD_HOST_IP}:2380 \\
 --listen-peer-urls http://${ETCD_HOST_IP}:2380 \\
 --listen-client-urls http://${ETCD_HOST_IP}:2379,http://127.0.0.1:2379 \\
 --advertise-client-urls http://${ETCD_HOST_IP}:2379 \\
 --initial-cluster-token etcd-cluster-1 \\
 --initial-cluster etcd-1=http://10.142.0.2:2380,etcd-2=http://10.142.0.4:2380,etcd-3=http://10.142.0.3:2380 \\
 --initial-cluster-state new \\
 --heartbeat-interval 1000 \\
 --election-timeout 5000
Restart=on-failure
RestartSec=5
 
[Install]
WantedBy=multi-user.target
EOF
```

etcd 클러스터 구성하기

모든 서버에서 구성이 되고 난 후 etcd 서비스를 시작하고 활성화 한다. 
첫번째 서버는 부트스트랩 노드로 동작하고 3개의 노드가 모두 시작되면 한 노드가 리더로 선정된다.
```
$ systemctl daemon-reload
$ systemctl enable etcd
$ systemctl start etcd.service
$ systemctl status -l etcd.service
```

etcd 클러스터 상태 확인

etcdctl은 etcd 클러스터와 상호 작용하는 유틸리티이다. 이 유틸리티는 모든 노드의 "/usr/local/bin" 폴더에서 찾을 수 있다.
클러스터 노드중 하나에서 다음과 같이 확인 할 수 있다.

다음과 같이 클러스터의 상태를 확인 할 수 있다.
```
$ etcdctl cluster-health
```

다음과 같이 하면 클러스터 멤버들의 상태와 리더의 상태를 확인 할 수 있다.
```
$ etcdctl  member list
```

etcdctl은 기본으로 v2를 사용한다. 그래서 v3을 사용하기 위해서는 환경 변수로 "ETCDCTL_API=3"을 명시하면 v3의 기능을 사용할 수 있다.
아니면 다음과 같이 사용할 수 도 있다.
```
$ ETCDCTL_API=3 etcdctl put name1 batman
$ ETCDCTL_API=3 etcdctl put name2 ironman
$ ETCDCTL_API=3 etcdctl put name3 superman
$ ETCDCTL_API=3 etcdctl put name4 spiderman
```

name3의 값을 얻기 위해서는 다음과 같이 한다.
```
$ ETCDCTL_API=3 etcdctl get name3
```

key들을 나열하거나 prefix를 사용해서 나열 할 수 있다.
```
$ ETCDCTL_API=3 etcdctl get name1 name4 # lists range name1 to name 4
$ ETCDCTL_API=3 etcdctl get --prefix name # lists all keys with name prefix
```
