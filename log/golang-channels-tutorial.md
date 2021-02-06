---
title: "Golang channels tutorial"
slug: 
description: null
date: 2014-12-10T13:12:58+09:00
type: posts
draft: false 
categories:
- til
tags:
- go
series:
-
---

최근에 goroutine과 channel에 대해서 학습하다가 설명이 잘 되어 있는 글을 찾아서 번역(이라고 하기에는 뭐하고 정리?) 를 해봤다. 
제대로 이해를 못하고 잘 못 번역을 한 부분이 있을 수 도 있으니...꼭 원본을 보길 권장한다.;;; 

원본 : http://guzalexander.com/2013/12/06/golang-channels-tutorial.html

---

Go언어는 병렬 프로그램 작성을 위해서 내장 도구를 갖고 있다. go를 함수 호출 앞에 놓으면 동일한 주소에 위치하는 코드를 독립적인 병렬 스레드로 실행을 시작한다. 이런 쓰레드를 Go언어에서는 goroutine이라고 부른다. 여기에서 말하는 concurrently는  parallel을 의미하지는 않는다. (역주: 그렇지만 번역은 “병렬” 이라고 칭하겠다.;;;) Goroutines은 하드웨어에서 병렬(parallel) 실행이 가능할때 병렬(concurrent)아키텍처를 생성하는 것을 의미한다.
Concurrency is not parallelism 에 대한 토크가 있다.  
goroutine 예제를 실행해 보자:

```go
func main() {
     // Start a goroutine and execute println concurrently
     go println("goroutine message")
     println("main function message")
}
```

이 프로그램은 "main function message” 와 아마도 "goroutine message” 를 찍을 것이다. 아마도 라고한건 goroutine 몇 가지 특징이 나타날 수 있기 때문이다 코드를 호출하는 goroutine이 시작될때, main함수는 goroutine이 끝나기를 기다리지 않고, 계속해서  실행이 된다. main함수는 println을 호출하고 나서 실행을 종료한다 그리고 Go언어에서 그건 생성된 모든 goroutine들과 전체 프로그램의 실행 중지를 말한다. 그렇지만 그러한 일이 발생하기전에 우리의 goroutine은 “goroutine message” 문자를 인쇄하고 코드 실행을 완료 할 수 있을거다.  


알다시피 이런 상황을 피할 수 있는 어떤 방법이 있어야만 한다. 그래서 Go언어에는 channel 이란게 있다.  

Channels basics
---
Channel은 동시에 동작하는 함수들의 실행을 동기화하고 특정한 값을 넘겨서 그들이 대화할 수 있는 방법을 제공한다. Channel은 몇 가지의 특징이 있다: channel을 통해서 보낼 수 있는 타입의 요소는, 용량(capacity:버퍼의 사이즈) 그리고 “\<-” 연산자로 지정된 커뮤니케이션의 방향이 있다. make 함수를 사용해서 channel을 할당할 수 있다:

```go
i := make(chan int)       // by default the capacity is 0
s := make(chan string, 3) // non-zero capacity

r := make(<-chan bool)          // can only read from
w := make(chan<- []os.FileInfo) // can only write to
```

Channel은 first-class values(역주: first-class object의 의미로 생각했음) 이고 다른 변수들처럼 어디에서나 사용될 수 있다: 구조체의 요소, 함수의 인자로, 함수에서 반환되는 값으로 그리고 다른 channel을 위한 타입으로도 사용될 수 있다:

```go
// a channel which:
//  - you can only write to
//  - holds another channel as its value
c := make(chan<- chan bool)

// function accepts a channel as a parameter
func readFromChannel(input <-chan string) {}

// function returns a channel
func getChannel() chan bool {
     b := make(chan bool)
     return b
}
```

channel을 쓰고 읽기 위해서 “\<-” 연산자가 있다. channel 변수에 대해서 상대적인 연산자의 위치에 따라서 읽기 또는 쓰기 동작이 정해진다. 다음 예제에서 그 사용법을 보여주고 있지만, 이 코드는 나중에 설명할 몇 가지 원인 때문에 동작하지 않는다.

```go
func main() {
     c := make(chan int)
     c <- 42    // write to a channel
     val := <-c // read from a channel
     println(val)
}
```

이제 channel이 무엇인지, 어떻게 만들고 기본 동작의 수행을 어떻게 하는지 알았으니, 첫번째 예제로 돌아가서 channel이 어떻게 도움을 주는지 알아보자.

```go
func main() {
     // Create a channel to synchronize goroutines
     done := make(chan bool)

     // Execute println in goroutine
     go func() {
          println("goroutine message")

          // Tell the main function everything is done.
          // This channel is visible inside this goroutine because
          // it is executed in the same address space.
          done <- true
     }()

     println("main function message")
     <-done // Wait for the goroutine to finish
}
```

이 프로그램은 두 메세지를 인쇄 할 것이다. 왜? “done” channel은 버퍼 없기 때문이다. unbuffered channel에서 모든 동작은 수신자와 송신자 모두가 통신 준비가 되기 전까지 실행이 멈춰있게 된다. 그게 unbuffered channel을 동기식이라고 부르는 이유이다. “<-done” 으로 읽는 작업을 하는 main 함수는 goroutine에서 channel에 data를 쓰기 전까지 동작이 블락될 것이다. 그래서 읽는 작업이 성공한 후에만 프로그램이 끝난다.  

다음의 경우 버퍼가 완전히 차있지 않으면 쓰는 동작을 할 수 있고, 버퍼가 비어 있지 않으면 블럭없이 성공적으로 모두 읽을 수 있는 버퍼를 갖고 있다. 이러한 channel을 비동기식이라고 부른다. 동기와 비동기의 차이를 보여주는 예는 다음과 같다:

```go
func main() {
     message := make(chan string) // no buffer
     count := 3

     go func() {
          for i := 1; i <= count; i++ {
               fmt.Println("send message")
               message<- fmt.Sprintf("message %d", i)
          }
     }()

     time.Sleep(time.Second * 3)

     for i := 1; i <= count; i++ {
          fmt.Println(<-message)
     }
}
```
이 예에서 “message” 는 동기식 channel이고 그 결과이다:

```
send message
// wait for 3 seconds
message 1
send message
send message
message 2
message 3
```

goroutine에서 channel에 첫번째 쓰는 동작을 하고 난 후에 channel에 모든 쓰기 동작은 첫번째 읽기 동작이 수행되기전 (대략 3초 후)까지 블락되었다.  

이제 “message” channel에서  버퍼를 제공해보자. 즉 새로 작성한 라인은 “message := make(chan string, 2)” 처럼 보일것이다. 이번에는 결과과 다음과 같이 보일거다:

```
send message
send message
send message
// wait for 3 seconds
message 1
message 2
message 3
```

이제 3개의 message를 저장할 수 있도록 한 channel의 버퍼에서 첫 번째 읽기를 위한 대기 없이 모든 쓰기 동작을 할 수 있는 걸 볼 수 있다. channel의 capacity를 변경해서 시스템의 처리량을 제한하고 정보량을 제어할 수 있게 되었다.  

Deadlock
---

이제 실행할 수 없었던 읽고/쓰기 동작 예제로 돌아가 보자.

```go
func main() {
     c := make(chan int)
     c <- 42    // write to a channel
     val := <-c // read from a channel
     println(val)
}
```
실행해 보면 이런 에러들을 볼 수 있다.(상세한건 다를 수 있다.)

```
fatal error: all goroutines are asleep - deadlock!

goroutine 1 [chan send]:
main.main()
     /fullpathtofile/channelsio.go:5 +0x54
exit status 2
```

이 에러를 deadlock이라고 부른다. 이 상황은 두 goroutine이 서로를 기다리면서 둘 다 실행을 할 수 없는 것이다. Go언어는 런타임에서 deadlock을 찾을 수 있기때문에 이런 에러를 볼 수 있다. 커뮤니케이션 동작이 블락되었기 때문에 에러가 발생한 것이다.  

이 코드는 싱글쓰레드에서 한줄 씩, 차례대로 실행된다. 프로그램 실행이 channel에 쓰기 동작( c <- 42 ) 에서 블락되어 있는건, 우리가 알고 있는 것 처럼, 동기 channel에서 쓰기 동작은 그걸 받는 쪽에서 데이터를 받을 준비가 되어야만 성공할 수 있기 때문이다. 그래서 다음 라인에 리시버를 만들어 주기만 하면 된다.  

이 코드를 동작하게 하기 위해 다음과 같이 작성하면 된다:

```go
func main() {
     c := make(chan int)
     
     // Make the writing operation be performed in
     // another goroutine.
     go func() { 
        c <- 42 
     }()
     val := <-c
     println(val)
}
```

Range channels and closing
---

앞의 예제들중에 하나에서 channel에 여러 메세지를 보내고 그걸 읽었다. 받는 부분의 코드이다:

```go
for i := 1; i <= count; i++ {
     fmt.Println(<-message)
}
```

읽는 동작에서 deadlock이 없게 하기 위해서, 보낸 메세지의 수를 정확하게 알고 있어야 한다. 더 많이 보내게 되면 읽을 수가 없다.이건 아주 불편하다. 이걸 좀 더 일반적인 코드로 작성하면 좋을것 같다.  

Go언어에는 array, string, slice, map 그리고 channel의 반복자를 사용할 수 있는 range expression 이 있다. channel을 close 하기전까지 반복처리를 한다. 다음 예제를 보자(지금 동작하지 않는다.):  

```go
func main() {
     message := make(chan string)
     count := 3

     go func() {
          for i := 1; i <= count; i++ {
               message <- fmt.Sprintf("message %d", i)
          }
     }()

     for msg := range message {
          fmt.Println(msg)
     }
}
```

이 코드는 동작하지 않는다. 위에서 말한대로 range는 channel이 close되기 전까지 작업을 계속하게 된다. close 함수로 channel을 닫아줘야만 한다. goroutine 부분은 이처럼 보여질거다:

```go
go func() {
     for i := 1; i <= count; i++ {
          message <- fmt.Sprintf("message %d", i)
     }
     close(message)
}()
```

channel을 close 하게 되면 한가지 더 유용한 기능이 있다. - 닫힌 channel 에 읽기 동작을 하면 블락이 되지 않고 channel 타입의 기본값을 반환한다.  

```go
done := make(chan bool)
close(done)

// Will not block and will print false twice 
// because it’s the default value for bool type
println(<-done)
println(<-done)
```

이 방법은 goroutine의 동기화에 사용할 수도 있다. 예제중에 하나를 동기화 해보자 :

```go
func main() {
     done := make(chan bool)

     go func() {
          println("goroutine message")

          // We are only interested in the fact of sending itself, 
          // but not in data being sent.
          done <- true
     }()

     println("main function message")
     <-done 
}
```

여기에서 “done” channel은 오직 실행을 동기화 하는데만 쓰이고 데이터를 전송하지는 못한다. 이럴때 사용하는 패턴이 있다:

```go
func main() {
     // Data is irrelevant
     done := make(chan struct{})

     go func() {
          println("goroutine message")

          // Just send a signal "I'm done"
          close(done)
     }()

     println("main function message")
     <-done
}
```

goroutine에서 close한 channel에 읽기 동작을 하면 블럭되지도 않고 main 함수도 계속 실행이 된다.  

Multiple channels and select
---

실제 프로그램에서는 더 많은 goroutine과 channel이 필요할 거다. 독립적인 부분이 많아 지면 더 효과적인 동기화가 필요할거다. 복잡한 예제를 보자:

```go
func getMessagesChannel(msg string, delay time.Duration) <-chan string {
     c := make(chan string)
     go func() {
          for i := 1; i <= 3; i++ {
               c <- fmt.Sprintf("%s %d", msg, i)
               // Wait before sending next message
               time.Sleep(time.Millisecond * delay)
          }
     }()
     return c
}

func main() {
     c1 := getMessagesChannel("first", 300)
     c2 := getMessagesChannel("second", 150)
     c3 := getMessagesChannel("third", 10)

     for i := 1; i <= 3; i++ {
          println(<-c1)
          println(<-c2)
          println(<-c3)
     }
}
```

우리는 channel을 만들고 일정한 간격으로 3개의 메세지로 channel을 채우는 goroutine을 생성하는 함수를 갖고 있다. 3번째 channel c3는 제일 짧은 간격이라서 다른것들보다 먼저 메세지가 보여지게 될 것 같다. (역주: except 가 아니라 expect 인것 같다.) 그렇지만 결과는 다음과 같다:

```
first 1
second 1
third 1
first 2
second 2
third 2
first 3
second 3
third 3
```

분명히 연속해서 출력되었다.300밀리초단위로 루프 반복과 다른 작업을  하는 첫번쨰 channel에 대한 읽기 동작은 기다려야하기 때문이다. 우리는 실제로 모든 channel에서 어떤것이든 바로 메세지를 읽기를 원한다.  

여러 channel에서 커뮤니케이션 작업을 위해서 Go언어에는 “select” 가 있다. “switch”와 비슷하지만, 모든 case에는 커뮤니케이션 동작(read, write)만 있다. “case”의 동작이 수행되면 그에 맞는 코드 블록이 실행된다. 그래서 우리가 원하는걸 하도록 작성하면된다:  

```go
for i := 1; i <= 9; i++ {
     select {
     case msg := <-c1:
          println(msg)
     case msg := <-c2:
          println(msg)
     case msg := <-c3:
          println(msg)
     }
}
```

숫자 9는 channel에서 3번 쓰는 동작을 한다. 그래서 9번 루프를 돌면서 select를 했다. 프로그램에서 무한 루프로 “select”를 실행하는건 보통 데몬으로서 실행되는걸 의미한다. 그렇지만 여기에서는 한번 실행이 되고나면 deadlock에 걸리게 될 것이다.  

이제 예상한 결과를 얻었다. 그리고 읽기 작업중에 다른 작업을 차단하지 않는다. 결과이다:

```
first 1
second 1
third 1 // this channel does not wait for others
third 2
third 3
second 2
first 2
second 3
first 3
```

Conclusion
---

Channel은 Go언어에서 아주 강력하고 흥미로운 구조이다. 그렇지만 그걸 효과적으로 사용하기 위해서는 어떻게 동작하는지 꼭 이해를 해야한다. 여기에서는 꼭 필요한 기본들에 대해서만 설명을 했다. 더 학습하기 위해서 다음들을 보면 좋을 것 같다.  

* [Concurrency is not parallelism](https://blog.golang.org/concurrency-is-not-parallelism) - early mentioned talk from Rob Pike
* [Go Concurrency Patterns](https://www.youtube.com/watch?v=f6kdp27TYZs)
* [Advanced Go Concurrency Patterns](https://www.youtube.com/watch?v=QDDwwePbDtw)
