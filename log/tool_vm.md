---
title: "VM 설정" 
date: 2016-04-21T14:39:36+09:00
---

Labs 머신에서 사용할 vm을 생성할때 특이점

* vm 생성
* Settings -> Network(Attached to: Bridged Adapter)
* Start(Network & Host Name)
    * Host Name: 지정
    * Ethernet(enp0s3): On
    * Configure - IPv4 Settings
        * Method: Manual
        * Addresses: Labs-Network guide 문서의 subnetmask, dns, gateway 그리고 사용할 ip 참조
        * Require IPv4 addressing for this connection to complete 항목 선택
        