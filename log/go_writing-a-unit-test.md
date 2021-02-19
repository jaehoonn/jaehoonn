---
title: "Go언어 프로젝트에서 테스트 코드 작성 경험"
date: 2018-02-21T23:02:29+09:00
---

테스트 코드는 왜?
---

현재 프로젝트를 진행 중 코딩을 할 때면 계속해서 의심과 두려움이 들었다.  

"내가 제대로 작성하고 있는 게 맞나?"  
"이렇게 작성하면 다른 데에서 문제가 생기는 건 아닌가?"  

그리고 이런 걱정들은 다음의 2가지 원인 때문이지 않을까 라고 생각하게 되었다.  
첫 번째, 코드 작성 후 실행해서 결과를 확인하기 위해서 개인 개발 환경을 MessageQueue와 내가 보낸 요청에 대해 기대하는 응답을 전달해줄 MessageQueue 반대편의 모듈을 구성하기가 쉽지 않다는 것이었다.  
두 번째, 기존에 코딩하면서 세워 놓았던 원칙을 기억하지 못하고 그 원칙에 어긋나는 코드를 작성하게 되었을 때, 전체 시스템에서 동작 중 알 수 없는 순간에 오동작하는 경우 때문이었다.  

"테스트 주도 개발 TDD 실천법과 도구" 라는 책과 함께 몇 가지의 블로그 포스팅 그리고 몇 권의 책을 더 읽어 보면서 테스트 코드를 작성해 보면 문제의 원인을 해결할 수 있지 않을까 싶었다.   
그래서 진행중인 프로젝트들에 테스트 코드를 작성해 보기로 했다.

테스트 코드는 뭘?
---

테스트 코드를 작성할 때에 주의해야 할 점은 다음과 같다.  

1. "질문 -> 응답 -> 정제 -> 반복" 의 과정을 거쳐서, 테스트를 작성하고 실패하고 정리하고  이걸 반복한다. 
2. 테스트 작성의 최소 단위는 함수이다. 하나의 함수를 기준으로 테스트 코드를 작성할 수 있다.
3. 설계할 때 동작을 먼저 정의하고 그 동작에 필요한 속성을 고려한다.

테스트 코드는 어떻게?
---

Go 언어에서 테스트 코드의 작성은 표준으로 제공하는 “testing” 패키지만으로 가능하다.  

테스트 코드 작성과 확인을 좀 더 편리하게 도와주는 다음과 같은 도구들의 도움을 받을 수도 있다.  

* testify ( go get -u github.com/stretchr/testify )  
* goconvey ( go get -u github.com/smartystreets/goconvey )  

testify는 assert, http, mock, require, suite 와 같은 패키지를 제공해서 좀 더 편리한 검증을 할 수 있도록 도와준다. 그리고 goconvey는 터미널(go test)에서 테스트 결과를 확인하던걸 브라우저에서 편리하게 확인할 수 있게 해주는 도구이다.  

goconvey의 사용은 테스트를 진행할 디렉토리에서 goconvey를 실행해 주면 웹브라우저가 실행되면서 다음과 같은 화면으로 현재 프로젝트의 테스트 상황을 한 눈에 볼 수 있게 해준다.  
![](/static/writing-a-unit-test/scr-1.png)

또한, 현재 coverage 상태를 다음과 같이 볼 수도 있다.  
![](/static/writing-a-unit-test/scr-2.png)

테스트 코드 작성
---

테스트 함수 작성 요령  

* 테스트 대상 함수와 이름을 1:1로 일치시킨다.  

  ```
  대상: Getbalance() {...}  
  테스트: TestGetbalance(...) {...}
  ```

* 테스트 대상 메소드의 이름을 메소드 단위로 작성을 하면서 뒤에 추가적인 케이스에 대한 내용을 추가한다.  

  ```
  func Test_Withdraw_마이너스통장인출(...) {...}
  ```

* 특정한 메소드가 아닌 테스트 시나리오를 대상으로 하는 테스트 메소드를 작성하는 경우도 있다.  

  ```
  func Test_VIP고객이_인출할때_수수료계산(...) {...}
  ```

* 테스트 케이스 시나리오는 정상적인 흐름에 대한 결과를 확인하거나, 예외나 에러 상황에 대한 결과를 확인하는 방법이 있을 수 있다. 그리고 a+b의 동작을 하는 메소드에 a와 b를 넣고 실제로 a+b를 한 결과와 일치하는지 확인해 보는 식의 경우가 있다.  

Go 언어로 만들어진 프로젝트에서 테스트 코드를 작성하기 위해서는 몇 가지 규칙이 있다.  

* 파일명이 "xxx_test.go" 와 같이 파일명 뒤에 \_test라고 지정한다.
* 테스트를 진행할 함수의 이름은 "Test" 로 시작되어야 한다.  

  ```
  func Test_Minus_작은_수에서_큰_수_빼기(t *testing.T) { }
  ```

* 위의 ex에서 보이는 것 처럼 testing.T 를 인자로 받는다. testing.T 타입은 Error, Fail, Fatal, Log 등과 같은 테스트에 필요한 요소들을 갖고 있다.
(자세한 내용은 https://golang.org/pkg/testing/ 을 참고) 

테스트 코드 작성 예(1)
---

간단한 더하기 계산을 하는 패키지를 만들면서 필요한  테스트 코드를 작성하는 예를 한번 보자.  
테스트 코드는 다음과 같다.  

```go
// add_test.go
package add

import (
	"testing"
)

func TestAdd(t *testing.T) {
	//arrange
	var x, y, res int
	x = 2
	y = 3

	//act
	res = Add(x, y)

	//assert
	if res != 5 {
		t.Fatal("Add의 결과가 옳바르지 않습니다")
	}
}
```

add 패키지의 구현은 다음과 같다.  

```go
// add.go
package add

func Add(x, y int) int {
	return x + y
}
```

이제 테스트를 진행해서 결과를 보기 위해서 다음과 같이 실행한다.  

```
$ go test -v
=== RUN   TestAdd
--- PASS: TestAdd (0.00s)
```

테스트 코드 작성 예(2)
---

이번에는 testify/assert 패키지를 이용한 테스트 코드 작성 예를 한번 보도록 하겠다. 그리고 현재 프로젝트 디렉토리에서 goconvey를 실행해서 브라우저를 통해서 테스트 함수들의 성공 또는 실패를 계속해서 확인해보자.  

```
sample_1$ goconvey

2018/02/20 17:02:39 goconvey.go:61: Initial configuration: [host: 127.0.0.1] [port: 8080] [poll: 250ms] [cover: true]
2018/02/20 17:02:42 tester.go:19: Now configured to test 10 packages concurrently.
2018/02/20 17:02:42 goconvey.go:192: Serving HTTP at: http://127.0.0.1:8080
2018/02/20 17:02:42 integration.go:122: File system state modified, publishing current folders... 0 3038222316
2018/02/20 17:02:42 goconvey.go:118: Received request from watcher to execute tests...
2018/02/20 17:02:42 executor.go:69: Executor status: 'executing'
2018/02/20 17:02:42 coordinator.go:46: Executing concurrent tests: sample_1
2018/02/20 17:02:42 goconvey.go:105: Launching browser on 127.0.0.1:8080
2018/02/20 17:02:46 goconvey.go:113: ATTENTION: default value of option force_s3tc_enable overridden by environment.
기존 브라우저 세션에 새 창을 생성했습니다.
```

```go
// oprcfg.go

type CfgItem struct {
  Name        string
  Value       string
  Owner       string
}

type Config struct {
  Cfg map[string][]CfgItem
}
 
func NewConfig() (*Config, error) {
  oc := &Config{}
  oc.Cfg = make(map[string][]CfgItem)
  return oc, nil
}
 
func (oc *Config) GetOprCfg(owner string) []OprCfg {
  if owner == "" {
    return nil
  }
  
  globalCategory := "global"
  srchCategory := owner
 
  globalConfigItem := oc.Cfg[globalCategory]
  srchConfigItem := oc.Cfg[srchCategory]
 
  var resOpcf []OprCfg
 
  for _, cf := range globalConfigItem {
    opcf := OprCfg{Name: cf.Name, Value: cf.Value, Owner: cf.Owner}
    resOpcf = append(resOpcf, opcf)
  }
 
  for _, cf := range srchConfigItem {
    opcf := OprCfg{Name: cf.Name, Value: cf.Value, Owner: cf.Owner}
    resOpcf = append(resOpcf, opcf)
  }
 
  return resOpcf
}
```

Config 객체를 생성하는 NewConfig() 함수가 있고, Config에서 owner에 해당하는 OprCfg를 반환하는 GetOprCfg() 함수가 있다.  
이 두 함수에 대한 테스트 코드를 다음과 같이 작성했다.  

```go
// oprcfg_test.go

func Test_Config_New(t *testing.T) {
  // arrange
 
  // act
  oc, _ := NewConfig()
 
  // assert
  assert.NotNil(t, oc)
}
 
func Test_GetOprCfg(t *testing.T) {
  // arrange
  oc, _ := NewConfig()
 
  oc.SetCfgItem("key1", "value1", "key1-value1", "owner1")
  oc.SetCfgItem("key2", "value2", "key2-value2", "owner2")
  oc.SetCfgItem("key3", "value3", "key3-value3", "owner1")
  oc.SetCfgItem("key4", "value4", "key4-value4", "owner1")
  oc.SetCfgItem("key5", "value5", "key5-value5", "owner2")
  oc.SetCfgItem("key6", "value6", "key6-value6", "owner3")
  oc.SetCfgItem("key7", "value7", "key7-value7", "owner1")
  oc.SetCfgItem("key8", "value8", "key8-value8", "global")
 
  // act
  opcfs := oc.GetOprCfg("owner1")
  t.Log(opcfs)
 
  // assert
  var res []OprCfg
  res = make([]OprCfg, 5)
 
  // res에는 global이 먼저 쌓이고, 타겟 owner가 쌓인다
  res[0] = OprCfg{Name: "key8", Value: "value8", Owner: "global"}
  res[1] = OprCfg{Name: "key1", Value: "value1", Owner: "owner1"}
  res[2] = OprCfg{Name: "key3", Value: "value3", Owner: "owener1"}
  res[3] = OprCfg{Name: "key4", Value: "value4", Owner: "owner1"}
  res[4] = OprCfg{Name: "key7", Value: "value7", Owner: "owner1"}
  assert.True(t, assert.ObjectsAreEqual(&res, &opcfs))
}
```

테스트 코드를 작성하기 위해서 “Test” 로 시작하는 함수명을 갖도록 했다.  
func Test_Config_New(t \*testing.T) 함수에서 보면, 다음과 같은 주석을 볼 수 있다.  

```
// arrange
...
// act
...
// assert
...
```

이는 테스트 코드를 작성하기 위한 일종의 템플릿 같은 것이다.  
arrange: 테스트를 진행하기 전에 필요한 준비 작업 정도의 코드들이 위치할 수 있다. 
act: 실제로 테스트 되어서 결과를 확인하고자 하는 동작을 구현한다.  
assert: act에서 진행한 동작에 대한 결과를 확인하는 용도이다.  

첫 번째 테스트 코드인 Test_Config_New는 Config 객체의 생성이 잘 되는지 확인하기 위한 테스트이다.  

arrange 절은 준비할 작업이 별도로 없기 때문에 생략되었다.  
act 절에서 확인하고자 하는 NewConfig() 함수를 사용해서 Config 객체를 생성한다. 
assert 절에서 생성된 객체가 생성이 잘 되었는지 확인하는 과정으로 객체가 nil인지를 확인한다. 여기에서 testify/assert 패키지를 사용한다.  

assert 패키지에는 다양한 검증을 위한 기능을 제공하고 있다. 간단하게 assert.NotNil은 주어진 객체가 nil이 아닌지 확인하는 함수이다. assert의 다양한 기능에 대해서는 godoc( https://godoc.org/github.com/stretchr/testify/assert ) 에서 확인해볼 수 있다. 

두 번째 테스트 코드인 Test_GetOprCfg는 Config에  다양한 owner의 CfgItem들이 섞여 있는 중에서, 특정 owner와 onwer가 global로 지정된 CfgItem을 찾아서 OprCfg 만들어서 반환해주는걸  확인하기 위한 용도의 테스트이다.  

arrange 절에서 GetOprCfg 함수를 테스트하기 위해서 Config 객체를 생성해서 임의의 설정(CfgItem) 값들을 owner를 달리해서 입력(SetCfgItem)한다.  
act 절에서 GetOprCfg 함수에 owner로 "owner1” 를 전달해서  OprCfg 슬라이스를 반환값으로 받는다.  
assert 절에서는 결과 값으로 받은 OperCfg 슬라이스가 global과 owner1이 owner인 값들만을 전달받았는지를 확인한다.  

assert 패키지의 ObjectsAreEqual은 두 Object가 일치하는지를 확인한다.  
assert 패키지의 True는 전달받은 값이 true인지를 확인한다.  

이렇게 기본적인 테스트 코드를 작성할 수 있다. 그렇지만 모든 상황에 대해서 테스트 코드를 작성할 수 있는 것은 아니며, 다음과 같은 경우에 대해서는 테스트 코드를 작성하는 데 한계가 있다.  

* 동시성 문제
* 접근제한자
* GUI
* 의존성 모듈 테스트 

위의 경우는 고민하지 말고 테스트 코드 만들기를 포기하자...; (왜? 난 테스트 코드 작성 초보이니까!)

테스트 코드 작성 예(3)
---

의존성의 경우는 테스트더블을 이용해서 어느 정도는 해결(?) 할 수 있다. 예를 들어 다음과 같은 경우이다.

```
패스워드 저장의 구현을 위해서 암호화 모듈을 제공 받아야 한다. 
암호화를 위한 인터페이스와 암호화 방식이 미리 정해져 있다면, 암호화 모듈을 Mock으로 제작할 수 있다. 
미리 약속된 인터페이스 기능들을 Cipher라는 인터페이스로 정의하고 
그 인터페이스를 구현한 MockMD5Cipher를 만들어서 encrypt와 decrypt 내부에서 
“potato”와 potato의 md5 hash 값을 무조건 반환해준다.
```

이러한 경우를 우리 프로젝트에서 생각해 보면 MessageQueue에 의존적인 환경에서 개발하고 있는걸 생각해 볼 수 있다. MessageQueue의 동작을 테스트더블 객체로 만들어서 테스트 코드를 작성해 볼 수 있다. 

"테스트더블" 이라는 용어는 대역, 스턴트맨을 의미하는 스턴트 더블이라는 단어에서 유래되었다.  
데스트더블의 분류에는 "Dummy", "Stub", "Fake", "Spy", "Mock" 이 있다. 이들의 정의 또는 쓰임새는 다음과 같다.  

* Dummy: 객체의 겉모양만 만들어서 테스트를 진행할 수 있도록 하는 목적의 객체이다. (객체가 생성만 되면 되는 경우에 사용한다)
* Stub: 특정 메소드가 호출되어야 정상적인 동작이 가능한 경우에 사용하는 객체이다.(객체가 특정 상태나 모습을 갖추면 되기 때문에 필요한 메소드에 하드 코딩으로 결과 값 등을 작성한다.)
* Fake: 여러 상황을 대표하기 위해 Stub보다 좀 더 복잡한 구현이 들어간 객체이다.
* Spy: 특정한 메소드가 호출되었는지 확인하기 위한 용도로 호출된 내역을 내부에서 기록하는 로직이 들어간다.
* Mock: 위의 테스트더블 객체들은 상태에 대한 확인이 주된 용도였다면 Mock은 행위를 검증하는 용도로 많이 사용된다.

이제 MessageQueue에 의존적인 부분을 테스트더블 객체로 만들어서 테스트 코드를 작성해 보려고 한다.  
변경된 이벤트 정보를 확인하는 GetChangeEventInfo 함수에 대한 테스트 코드를 작성해 보자.  
테스트 코드를 작성하고자 하는 전체 로직은 다음과 같다.  

1. GetChangeEventInfo 함수가 호출되는 경우는 외부에서 이벤트가 변경되었음을 알리는 요청이 들어 온다.
2. 전달받은 eventID 값을 이용해서 GetChangeEventInfo 함수가 MessageQueue에 연결되어 있는 Agent에게 변경된 이벤트 정보를 전달해 달라고 요청한다.
3. Agent는 Database에서 조회해서 MessageQueue에 결과를 전달할 것이다.
4. sample_1은 MessageQueue를 통해서 전달된 응답을 수집할 것이다.
5. 결과로 받은 값을 확인한다.  

테스트 코드를 작성하기 이전의 코드 상태는 다음과 같았다. 

```go
func (d *Agent) GetChangeEventInfo(eventID string) (*EventInfo, error) {
  *msg := MakeChangeMsg(“blah-blah-chg-key”, “agent”)*
 
  eventUUID := UUID_Data { UUID: eventID }
  any, _ := Any.Encoding(&eventUUID)
  *msg.Payload(any)
 
  /* message queue 관련 기타 등등의 구현 들
      ...
  */
 
  SendMessage(msg)
  val := RecvMessage()

  data := val.GetPayload()
  var ei EventInfo
  Any.Decoding(data[0], &ei)
 
  return &ei, nil
}
```

위의 코드 중에서 굵게 표시된 부분은 sample_1 이 아닌 외부 요소(MessageQueue)들에 대한 동작을 구현한 부분이다.  
그리고 이 부분은 MessageQueue에 무언가를 요청하는 과정에서 매번 반복되기도 한다. 그래서 이 부분을 별도의 함수로 구현하기로 했다.  

```go
func (d *Agent) GetChangeEventInfo(eventID string) (*EventInfo, error) { 
  sendInfo := &MqSendInfo{
    key:     “blah-blah-chg-key”,
    recv: “agent”,
  }
 
  eventUUID := UUID_Data{ UUID: eventID }
  any, _ := Any.Encoding(&eventUUID)
 
  anyData, err := MqSend(sendInfo, any)
  if err != nil { return nil, err }
 
  var ei EventInfo
  Any.Decoding(anyData[0], &ei)

  return &ei, nil
}
```

위와 같이 코드를 별도의 함수(MqSend)로 빼놓고, MqSendInfo라는 struct를 인자로 받도록 변경을 했다. 함수와 구조체는 다음과 같이 작성되었다.  

```go
type MqSendInfo struct {
  key     string
  recv string
}
 
func MqSend(info *MqSendInfo, any *Any) ([]*Any, error) {
  msg := MakeChangeMsg(info.key, info.recv)
  
  msg.Payload(any)
 
  /* message queue 관련 기타 등등의 구현 들 
     ...
  */
 
  SendMessage(msg) 
  val := RecvMessage()
 
  return val.Msgs, nil
}
```

이렇게 해서 중복된 코드를 많이 줄일 수는 있었다. 그렇지만 MessageQueue의 의존성은 여전히 남아있다.  

테스트 코드를 작성하는 입장에서는 우리가 전달한 eventID의 값을 이용해서 MqSend 함수가 EventInfo 값을 돌려주면된다. MqSend가 호출되었는지와 그 함수의 결과 값을 이용해서 GetChangeEventInfo가 우리가 원하는 결과 값을 만들어서 돌려주면 성공적으로 동작한 거로 간주할 수가 있게 된다.  

그래서 MqSend 함수를 가짜로 구현하고 있는 테스트더블 객체를 만들려고 한다. +
우선 위의 MqSendInfo 구조체와 MqSend 함수의 이름을 MqMsg 구조체와 구조체의 메소드 Send로 변경하였다.  

```go
type Messenger interface {
  Send(any *Any) ([]*Any, error)
}
 
type MqMsg struct {
  key     string
  recv string
}

func (m *MqMsg) Send(any *Any) ([]*Any, error) {
  msg := MakeChangeMsg(m.key, m.recv)
 
  msg.Payload(any)
 
  /* message queue 관련 기타 등등의 구현 들 
     ...
  */
 
  SendMessage(msg)
  val := RecvMessage()

  return val.Msgs, nil
}
```

이제 테스트 코드를 다음과 같이 작성해 볼 수 있다.  

```go
type MockAgentMsgr struct {
  mock.Mock
}

func (m *MockAgentMsgr) Send(any *Any) ([]*Any, error) {
  args := m.Called(any, wait)
  return args.Get(0).([]*Any), args.Error(1)
}

func Test_GetChangeEventInfo(t *testing.T) {
  // arrange
 
  // 입력 값
  eventID := "event--uuid-1"
 
  UUID := UUID_Data { UUID: eventID }
  par1, _ := Any.Encoding(&UUID)
 
  // 출력 값
  var resAnytypes []*Any
  res1 := EventInfo { UUID: eventID, Name: "event1", Type: ET_START }
 
  resAnytype1, _ := Any.Encoding(&res1)
  resAnytypes = append(resAnytypes, resAnytype1)
 
  m := &MockAgentMsgr{}
  m.On("Send", par1).Return(resAnytypes, nil)
 
  agentMg := NewAgent(m)
 
  // act
  event, err := agentMg.GetChangeEventInfo(eventID)
 
  // assert
  assert.Equal(t, nil, err)
  assert.True(t, assert.ObjectsAreEqual(&res1, event))
  assert.True(t, m.AssertCalled(t, "Send", par1, true))
}
```

MqMsg의 테스트더블을 구현하기 위해서 Messenger 인터페이스에 Send 메소드를 정의했다.  
MockAgentMsgr 구조체는 Messenger 인터페이스를 구현한 테스트더블이다.  
MockAgentMsgr의 Send 메소드의 구현에서는 입력된 값과 반환되어야 할 값에 대해서 지정을 하고 있다.  
이제 Test_GetChangeEventInfo 함수에서 입력값을 만들고 Send 메소드가 호출되었을 때 반환해야 할 출력값도 지정한다. MessageQueue에서 어떤 동작을 하든지 상관없이 입력된 값에 의해서 정해진 결과 값을 받는다는 가정이다. 그래서 작성된 코드 중에 다음과 같은 내용이 있다.  

```go
m := &MockAgentMsgr{}
m.On("Send", par1).Return(resAnytypes, nil)
```

Send 메소드가 호출될 때 입력값에 대해서 출력값을 정의해 놓은 것이다.  

그런데 현재의 실행 코드상에서는 정해진 형태(MqMsg의 Send 메소드)만을 호출하는 구조이다. 이를 변경해야 할 필요가 있다. 그래서 우리는 Agent 객체를 생성하는 시점에 MessageQueue에 대한 정보를 지정해서 받는 형식으로 변경했다.  

```go
type Agent struct {
  Msgr Messenger
}
 
func NewAgent(msgr Messenger) *Agent {
  return &Agent{Msgr: msgr}
}

func (d *Agent) GetChangeEventInfo(eventID string) (*EventInfo, error) {
  eventUUID := UUID_Data{ UUID: evnetID }
  any, _ := Any.Encoding(&eventUUID)
 
  anyData, err := d.Msgr.Send(any)
  if err != nil { return nil, err }
 
  var ei EventInfo
  Any.Decoding(anyData[0], &ei)
 
  return &ei, nil
}
```

이제 테스트 코드에서처럼 NewAgent로 Agent 객체를 생성하기 전에 Messenger를 생성해서 전달받을 수 있게 되었다. (단, 실제로 Agent를 사용하는 시점에서는 매번 중복 코드가 발생하게 되었다. 이 부분은 다음에 다시 개선하기로 한다.)  

이제 GetChangeEventInfo 함수를 구현하기 위해서 MessageQueue에 연결해서 디버깅하기 위한 과정 대신에 테스트 코드가 sample_1 에서 구현되어야 할 내용에 대해서 보장을 해줄 수 있게 되었다.  

결론
---

이제 입력되는 값과 MessageQueue 건너편의 대상으로부터 받기를 원하는 값에 대한 정의만 정확하다면, MessageQueue에 직접 연결되지 않아도 우리는 sample_1 의 기능을 구현할 수 있게 되었다.  
그리고 코딩 중에 세워졌던 원칙에 어긋나는 경우에 대해서 별도의 문서나 히스토리를 확인하지 않고 작성된 테스트 코드로 확인할 수 있게 되었다. 
