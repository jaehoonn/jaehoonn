---
title: "KVM 스터디(with go)"
date: 2017-12-02T23:55:52+09:00
---


준비물
---
노트북(우분투 16.04)

KVM이 뭔가요?
---

### 하이퍼바이저?

위키백과에 나와 있는 정의( https://ko.wikipedia.org/wiki/%ED%95%98%EC%9D%B4%ED%8D%BC%EB%B0%94%EC%9D%B4%EC%A0%80 ) 를 보면 이렇게 나와 있다.   

"다수의 운영 체제를 동시에 실행하기 위한 논리적 플랫폼"    

여기에 우리가 잘 알고 있는 VmWare, VirtualBox와 같은것도 있고, Xen, KVM 같이 좀 낯선 것들도 있다.  

내용을 읽다보면, 왠지 중요해 보이는 단어 Type1, Type2, 전가상화, 반가상화 뭐 이런 단어들이 나온다.  
Type1은 호스트에 하이퍼바이저가 존재하고, Type2는 호스트 OS에 설치된 응용 프로그램 형태로 하이퍼바이저가 존재하는거라고 보면 될 거같다.  
그리고 전/반 가상화는 게스트에서 하이퍼바이저에 어떤 방식으로 접근을 하는지에 대한 구분 정도로 보면 어떨까 싶다...;   
전가상화는 게스트OS가 본인이 가상화된 상태라는걸 모르게 모든 하드웨어가 가상화 되어 제공되는 상태이며, 반가상화는 게스트 OS가 가상화되고 있음을 인지하고 특정한 하드웨어를 사용하기 위해서 게스트 OS의 커널의 변경이 필요한 상태이다.   
전가상화와 반가상화에 대해서 가장 이해하기 쉽게 설명된 글을 참고해보면 좋을것 같다. ( https://knowcitrixx.wordpress.com/2014/11/12/hypervisor-full-para-virtualization/ )   

이건 공부하면서 이런거 같다라고 정리한 내용이니 100% 믿지는 말기 바란다.  

이 글에서는 여러가지의 하이퍼바이저들 중에서 KVM(Kernel-based Virtual Machine)을 사용해 보려고 한다.  

### 어떻게 쓰는거지?
#### 설치와 VM 생성

우리는 QEMU(KVM) 이라는 하이퍼바이저를 설치할거다.  
우선 현재 CPU가 가상화를 지원하는지 확인을 해야한다.  
```
$ egrep -c ‘(vmx|svm)’ /proc/cpuinfo
```

결과가 1보다 크면 가상화를 지원하는 CPU 이다.   
그럼 KVM 관련 패키지들을 설치해 보자.  
설치하기전에 os관련 업데이트를 하자.
```
$ sudo apt-get update
$ sudo apt-get upgrade
```
이제 진짜로 설치... qemu-kvm을 설치한다.  
qemu-kvm은 qemu/kvm을 관리할 수 있는 기본적인 프로그램들이 설치된다.

```
$ sudo apt install qemu-kvm
```

뭔가 한참을 설치한다.   

설치된 항목들은 확인 하기 위해서 터미널창에서 qemu까지 입력하고 탭을 몇 번 입력해보자.   
qemu로 시작하는 command가 몇 개가 보여질거다.  
qemu-img(qemu의 디스크 이미지 관리를 위한 명령어),  
qemu-io(qemu용 저장 장치를 위한 진단, 조작 프로그램),  
qemu-make-debian-root(데비안 루트 이미지를 생성),  
qemu-nbd(network block device: 호스트의 블록 디바이스에 연결하여 사용하는 장치, qemu 이미지를 자체적으로 제공하도록 한다.)  
qemu-system-i386, qemu-system-x86_64, qemu-system-x86_64-spice(qemu pc 시스템 에뮬레이터)   
그리고 kv까지 입력하고 탭을 몇 번 입력해 보면 kvm으로 시작하는 command가 몇 개 보인다.  
kvm, kvm-ok, kvm-spice  

마지막으로 vir까지 입력하고 탭을 몇 번 입력해 보면,  
virtfs-proxy-helper  
가 보인다.  

이제 KVM이 설치되었는지 확인을 해보자.  

```
$ lsmod | grep kvm
```

을 하면 ...

```
kvm_intel    192512    0
kvm        598016    1    kvm_intel
irqbypass    16384        1    kvm
```

위의 결과와 비슷한 화면이 보인다. 그럼 잘 설치 된거다.  

그럼 이제 VM을 생성해보자.  

우선 ubuntu 이미지를 하나 받아 놓자.  
( https://help.ubuntu.com/community/Installation/MinimalCD )  

```
$ wget http://archive.ubuntu.com/ubuntu/dists/xenial/main/installer-amd64/current/images/netboot/mini.iso
```

그리고 qemu-img로 디스크를 미리 만들어 놓는다.
```
$ qemu-img create ubuntu.qcow2 10G
```

생성한 디스크에 os 설치를 한다.
```
$ qemu-system-x86_64 -localtime -net  user -net nic -m 256 -cdrom mini.iso -hda ubuntu.qcow2 -boot d
```

![](https://docs.google.com/drawings/d/e/2PACX-1vTy6mMJx7hrjFbQVHYBYohYI4Fh3G4kAYDy4XggeWHbZL65NdhBUNNZy3rYSPXWforAfTXm2I4lOKIl/pub?w=603&h=474)

이렇게 kvm을 이용해서 가상머신상에 ubuntu를 설치해 봤다.  

Go언어는 뭐죠?
---
### Go언어란?  

Go언어에 대한 정의도 위키백과를 참고해 보자.( https://ko.wikipedia.org/wiki/Go_(%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D_%EC%96%B8%EC%96%B4) )
구글에서 유명한 아저씨들(로버트 그리즈머, 롭 파이크, 켄 톰슨)이 만들기 시작했다.  
2009년 11월에 세상에 공개가 되었다.  

문법은 C랑 비슷하고...(라고 하지만 Go 코드에 익숙해지고 난 다음에 C코드 보면 정말 헷갈린다..;)  

여러가지 편리한 도구들도 제공한다.  
go build  
go test  
go fmt  
go get  
go vet  
go run  
godoc  
gorename  
go generate  

### 그래서?  
#### 설치  
설치는 다음과 같이 하면 된다.  

1. 기존에 설치된 버전이 있다면 삭제한다.   
   ```
   $ rm -r /usr/local/go
   ```
2. http://golang.org/doc/install 에서 다운로드
(또는$ wget –no-check-certificate https://storage.googleapis.com/golang/go1.7.1.linux-amd64.tar.gz 와 같이 원하는 OS 및 버전에 맞는 Go 바이너리를 wget으로 받을 수도 있다.)
3. /usr/local/go에 압축을 푼다.  
   ```
   $ sudo tar -C /usr/local -xzf gox.x.x.linux-xxx.tar.gz
   ```
4. $HOME/.profile 에 다음을 추가한다.   
   ```
   PATH=$PATH:/usr/local/go/bin  
   ```  
   ```
   GOPATH=$HOME/work
   ```
5. .profile을 적용한다.  
   ```
   $ source ./.profile
   ```
6. 설치가 되었으면 제대로 설치되었는지 확인 해보자.  
   ```
   $ go version
   ```
7. 결과가 다음이랑 비슷하게 나오면 성공한거다.  
   ```
   go version go1.8.1 linux/amd64
   ```

#### Hello,World
이제 Hello, World 한번 찍어보자.  

1. 에디터(utf-8 편집 가능한걸로)로 다음과 같이,   
   ```go
    package main

    import “fmt”

    func main() {
        fmt.Println(“Hello, World”)
    }
   ```
   main.go 파일을 하나 만들어서 작성해보자. 
2. 이제 저장하고 빌드해보자.  
   ```
   $ go run main.go
   $ go build
   $ go install
   ```
   빌드하는 방법은 위와 같이 3가지가 있는데, run은 결과물을 굳이 만들지 않고 결과를 바로 확인 할 수 있는거, build는 현재 작업하고 있는 경로에 결과물을 만들어 놓는거다. 그리고 install은 만들어진 결과물을 $GOPATH의 bin 디렉토리 아래에 옮겨주기까지 한다.  
3. 결과를 확인한다.

libvirt는 또 뭐에요?
---

### 뭐하는거?  
KVM같은 하이퍼바이저를 관리(?)를 하기 위한 라이브러리 정도(?)  

### 이건 또 어떻게 쓰나요?  
우선 설치를 해보자.

```
$ sudo apt install libvirt-bin ubuntu-vm-builder bridge-utils
```

여기에서 libvirt-bin 은 libvirt 관련한 것들(virsh, virt-admin, virt-host-validate, virt-login-shell, virt-pki-validate, virt-xml-validate, virtlockd, virtlogd)이 설치된다. 그리고 /var/lib/libvirt라는 디렉토리가 생긴다.  
ubuntu-vm-builder는 깨끗한 테스트환경, 가상 시스템 설치 프로세스를 자동화하거나 하는 VM을 빠르게 만들어 주는게 설치된다고 하는데 뭐가 설치되는지는 아직 잘 모르겠다.  
그리고 bridge-utils는 브릿지 네트워크를 설정하기 위한 용도의 무언가가 설치된다고 하는데, 설치되는게 없는거 같다.  

KVM에 대한 사용 권한은 루트 사용자와 현재 사용자만이 갖게 된다.  
다른 계정을 추가하기 위해서는 다음과 같이 추가한다.  

```
$ adduser user1 libvirtd
```

이제 virsh 이라는 libvirt를 다루기 위한 쉘 프로그램이 추가 되어 있는걸 확인 할 수 있다.  
이게 나중에  유용하게 사용이 될 거다.  

그리고 이제는 virt-manager라는 GUI기반의 VM 관리 도구를 설치한다.  
```
$ sudo apt install virt-manager
```

virt-manager를 설치하게 되면 virt-install, virt-viewer, virt-clone, virt-xml, virt-convert 같은걸 추가로 사용할 수 있게된다.  

virt-manager를 설치하지 않고 virtinst 를 설치해도 가능할거다…;  

이제 virt-manager를 사용해서 vm을 생성하고 시작하고 등등의 관리를 GUI 상에서 할 수 있는데, 동일한 작업을 virsh에서 해보기도 하고...virsh로 해본걸 다시 코드로 작성해 보기도 하자.  

libvirt-go 패키지를 설치한다.  
우선 libvirt-dev 라이브러리의 설치가 필요하다.  

```
$ sudo apt-get install libvirt-dev
```

그리고  
```
$go get github.com/libvirt/libvirt-go
```

이제 libvirt-go 패키지까지 설치를 마쳤다.  

그럼 이제 뭘 하면 되죠?
---
### TODO(libvirt-go를 사용한 예제들)
