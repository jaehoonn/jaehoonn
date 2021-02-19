---
title: "delve를 사용한 Go언어 디버깅"
date: 2017-06-18T23:13:14+09:00  
---

소개
---
delve는 Go언어를 위한 debugger 이다.

delve설치
---
설치를 하기위해서는 Go 1.5 이상의 버전이 필요하다.  
지금 시점에 Go 1.5를 사용하지 않을테니...  

다음의 명령으로 설치한다.

```
$ go get github.com/derekparker/delve/cmd/dlv
```

이제 설치 끝! (Windows랑 MAC은 모르겠다.)

delve사용
---

프로세스 실행 제어, 변수, 스레드/goroutine 상태, CPU 레지스터 상태 등을 확인 할 수 있다.

* Commands
  * attach: 실행중인 프로세스에 연결해서 디버깅을 한다.  
    ```
    $ dlv attach pid [executable]
    ```  
    실행중인 프로세스에 디버그 세션을 연결해서 프로세스를 제어한다.  
    디버그 세션을 종료할때 프로세스도 종료할 수 있다.  
  * connect: headless debug server(?)에 연결  
    ```
    $ dlv connect addr  
    ```  
    실행중인 헤드리스 디버그 서버에 연결 한다고 하는데 무슨 소리인지 모르겠다.  
  * core: core dump를 확인?  
    ```
    $ dlv core `<executable>` `<core>`  
    ```  
    코어 파일과 실행 파일을 열어 코어 덤프가 발생되었을때의 상태를 검사한다.  
  * debug: 현재 디렉토리의 main 패키지를 컴파일하고 디버깅을 시작한다. 패키지명을 입력하면 된다고 하지만 안되는거 같은데..  
    ```
    $ dlv debug [package]  
    ```  
    최적화 되지 않게 컴파일을 해서 시작하고 연결을 한다.
  * exec: 미리 컴파일 해 놓은 바이너리를 실행하고 디버그 세션을 시작  
    ```
    $ dlv exec [./path/to/binary]
    ```  
    미리 컴파일한 바이너리를 delve가 실행하고 바로 디버그 세션을 시작하기 위해서 연결(? attach) 한다.  
    최적화되지 않은 디버깅을 위한 바이너리로 실행을 해야한다. (-gcflags="-N -I")
  * replay: rr trace를 재실행?  
    ```
    $ dlv replay [trace directory]
    ```  
    mozilla rr이 반드시 설치되어 있어야 한다.  
    rr로 생성된 trace를 열어서 확인한다.(?)
  * test: test 바이너리를 컴파일하고 디버깅 프로그램을 시작한다.  
    ```
    $ dlv test [package]
    ```
    새로운 디버그 세션에서 단위 테스트를 실행  
    현재 디렉토리의 테스트를 디버그한다.  
    패키지 이름을 지정하면 해당 패키지의 테스트를 디버깅한다.  
  * trace: 컴파일하고 trace하는 프로그램을 시작한다?  
    ```
    $ dlv trace [package] regexp
    ```  
    -p(--pid) : 해당 Pid에 연결한다.   
    -s(--stack) : stack trace를 보여준다.   
    trace의 sub-command로 입력된 정규표현식과 일치하는 함수들에 trace-point를 설정하고 해당 함수에서 정보를 출력한다.  
    디버그 세션을 시작하지 않고 어떤 기능을 하는지 확인하고자 할 때 유용하다.
  * version: 버전 정보를 출력한다.  
    ```
    $ dlv version
    ```

* CLI Commands
  * args: 함수의 인자들을 출력한다
  * break(b): 브레이크포인트를 지정  
    ```  
    $ break [name] <linespec>  
    ```  
    ex) $ break main.go:10  
  * breakpoints: 활성화되어 있는 프레이크포인트 정보를 출력
  * check: 현재 위치에 체크포인트를 설정한다
  * checkpoints: 체크포인트들을 출력한다.
  * clear: 브레이크포인트를 제거한다.
  * clear-checkpoint: 체크포인트를 제거한다
  * clearall:  다중의 브레이크포인트를 제거한다
  * condition: 브레이크포인트이 조건을 지정한다.
  * continue: 브레이크포인트전까지 실행하거나 프로그램을 종료한다.
  * disassemble: Disassembler(?)
  * exit(q): 디버거를 종료한다.
  * frame
  * funcs
  * goroutine
  * goroutines
  * help
  * list
  * locals
  * next(n)
    다음 라인으로 넘어간다.
  * on: 브레이크포인트에 도달했을때 실행할 명령을 지정(print, stack, goroutine)  
    ```
    $ on <breakpoint name or id> <command>
    ```
  * print(p):  
    ```
    $ [goroutine <n>] [frame <m>] print <expression>  
    ```
  * regs
  * restart(r): 체크포인트나 이벤트에서 프로세스를 다시 시작한다.  
    ```
    $ restart [event number or checkpoint id]
    ```
  * rewind(rw): 브레이크포인트나 프로그램 종료되는 지점까지 뒤로 돌아간다.
  * set: 변수의 값을 변경한다.  
    ```
    $ [goroutine <n>] [frame <m>] set <variable> = <value>
    ```
  * source
  * stack(bt): stack trace를 출력한다.  
    ```
    $ [goroutine <n>] [frame <m>] stack [<depth>] [-full]  
    ```
  * step: 함수에 진입한다.
  * step-instruction
  * stepout: 현재 함수에서 나간다.
  * thread
  * threads
  * trace
  * types
  * vars
