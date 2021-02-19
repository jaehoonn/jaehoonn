---
title: "RabbitMQ 스터디(with go)"
date: 2017-04-19T23:43:52+09:00
---



Go언어로  RabbitMQ를 어떻게 이용할 수 있는지 스터디하는 내용들을 대충 대충  정리하는 중...

RabbitMQ라는게 뭐지?
---
AMQP를 구현한 메세지 브로커  
여기 저기에 있는 클라이언트들(Producer/Consumer)이 메세지를 서로 주고 받을 수 있도록 해주는 그런거?

설치
---
* RabbitMQ 설치  
```
sudo apt-get install rabbitmq-server
```

* 관리 플러그인 설치  
```
sudo rabbitmq-plugins enable rabbitmq_management   
sudo service rabbitmq-server restart
```

* 실행   
```
service rabbitmq-server start
```

* 계정 등록(id:jaehoonn, pw:1234)  
```
rabbimqctl add_user jaehoonn 1234
```

* 등록한 계정을 관리자 계정으로 변경  
```
rabbitmqctl set_user_tags jaehoonn administrator
```

* 웹브라우저로 관리 플러그인에 접속
```
localhost:15672  
```

용어
---
대충 요런것들에 대해서 찾아 보면 될 것 같은데...  

* 브로커(broker): 미들웨어, 메세지를 받고 전달  
* 가상호스트(virtual host): 가상 영역   
* 연결(connection): 물리적인 네트워크 연결(?)  
* 채널(channel): 논리적인 네트워크 연결(?)  
* 익스체인지(exchange): 생산된 새로운 메세지를 큐에 전달  
* 큐(queue): RabbitMQ 에 존재하는 우편함으로 메세지를 저장  
* 결합(binding): 익스체인지와 큐를 연결  
* 큐(queue)의 속성  
 * durable: 브로커가 재시작되어도 큐를 선언한 상태로 유지 여부  
 * autoDelete: 큐에서 소비할 대상이 없을때 큐의 유지 여부  
 * exclusive: 다른 연결에서 큐를 사용 가능 여부  
 * arguments: 사용자 정의 큐를 설정  

Go언어에서 RabbitMQ 사용
---
* 패키지는 amqp를 사용 (https://github.com/streadway/amqp)
* tutorial(http://www.rabbitmq.com/getstarted.html) 을 보면 6개의 챕터로 나누어져 있으며, 각 챕터마다 Go언어를 사용한 내용이 존재한다.
* tutorial의 예제 코드는 github(https://github.com/rabbitmq/rabbitmq-tutorials/tree/master/go) 에서 확인 할 수 있다.

amqp 패키지
---
* Type은 다음과 같은 것들이 있다.
  * Acknowledger
  * Authentication
  * Blocking
  * Channel
  * Config
  * Configuration
  * Connection
  * Decimal
  * Delivery
  * Error
  * PlainAuth
  * Publishing
  * Queue
  * Return
  * Table
  * URI

* 여기에서 Acknowledger와 Authentication은 인터페이스 이므로 패스,   
패키지 내부에서 사용하는 타입(?)들인 Blocking, Configuration, Decimal, Error, Publishing, Queue, Table도 패스   
Config, URI, Delivery는 DialXXX, Open 함수, Connection, Channel 타입에서 사용하는 용도이므로 대충 이런거구나 하고 패스   
Connection, Channel 타입에 대해서만 좀 알아 보면 좋을것 같다.

* Connection의 메소드들에 대해서 알아보자
  * Channel()
  * Close()
  * ConnectionState()
  * LocalAddr()
  * Notify(Blocked, Close)

* Channel의 메소드들에 대해서 알아보자
  * Acknowledger 인터페이스의 구현 메소드  
    Ack(), Nack(), Reject()
  * Channel 기능에 대한 메소드 나열   
   Cancel(), Close(), Confirm(), Consume(), Flow(), Get(), Publish(), Qos(), Recover()
  * Exchange, Notify, Queue, Tx 관리 메소드   
   Exchange(Bind, Declare, DeclarePassive, Delete, Unbind)   
   Notify(Cancel, Close, Confirm, Flow, Publish, Return)   
   Queue(Bind, Declare, DeclarePassive, Delete, Inspect, Purge, Unbind)   
   Tx(, Commit, Rollback)   


tutorial
---
1. "Hello World" (http://www.rabbitmq.com/tutorials/tutorial-one-go.html)
2. Work Queues (http://www.rabbitmq.com/tutorials/tutorial-two-go.html)
3. Publish/Subscribe (http://www.rabbitmq.com/tutorials/tutorial-three-go.html)
4. Routing(http://www.rabbitmq.com/tutorials/tutorial-four-go.html)
5. Topics(http://www.rabbitmq.com/tutorials/tutorial-five-go.html)
6. Remote Procedure Call(http://www.rabbitmq.com/tutorials/tutorial-six-go.html)

"Hello World"
---

* 응용프로그램에서 RabbitMQ로 전달되는 메세지는 큐에 저장된다.
* 여러개의 Porducer에서 메세지를 큐에 전달을 하며, 여러개의 Consumer에서 데이터를 수신하려고 시도할 수 있다.
* RaabbitMQ 클라이언트 라이브러리를 설치  
```
go get github.com/streadway/amqp
```
* mq 서버에 연결  
```go
conn, err := amqp.Dial("amqp://jaehoonn:1234@localhost:5672")  
```  
연결 포트는 5672번을 사용 (관리 플러그인은 15672번 포트를 사용)
* Connection   
Dial(...) 에서 Connection 타입을 만든다.  
```go
ch, err := conn.Channel()
```  
Connection은 소켓 연결 관리, 프로토콜 버전 협상(?), 인증을 처리  
Connection을 통해서 Channel을 생성한다.  

* Channel   
채널을 통해서 큐를 생성
```go
q, err := ch.QueueDeclare(...)
```  
큐에 대한 설정을 할 수 있을것 같다.  
name: 큐의 이름  
durable: 브로커 재시작 후에도 큐의 상태를 유지(true)  
autoDelete: 소비자가 없으면 유지 되지 않는다(true)  
exclusive: 다른 연결에서 큐를 사용하지 못함(true)  
noWait: 대기열이 서버에서 선언(true)  
args: 사용자 정의 큐를 구성할때 사용  
참고) 결합된 큐가 없을때, 목적지 큐를 찾지 못하면 그냥 사라진다.
* 보내기/받기
  * 보내기(Publish)  
   exchange: exchange 이름   
   key: routing key의 이름(단일 큐일 경우에는 큐의 이름)   
   mandatory: 전달할 수 없는 메세지일 경우에 처리   
   immediate: 전달할 수 없는 메세지일 경우에 처리  
   msg:  
  * 받기(Consume)   
   queue:   
   consumer:     
   autoAck:      
   exclusive: true이면 서버가 메세지의 소비자, false이면 다수의 소비자가 존재  
   noLocal: 동일한 연결에 대해서 한번 정송한 메세지를 다시 전송하지 않는다  
   noWait: 요청이 들어오면 바로 전송한다.  
   args: 
* Work Queues
