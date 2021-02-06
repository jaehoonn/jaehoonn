---
title: "GCE에서 kops를 이용한 K8s 구성"
slug: k8s-with-kops-on-gce
description: null
date: 2020-03-05T21:36:13+09:00
type: posts
draft: false 
categories:
- til 
tags:
- kubernetes
series:
-
---

# 클러스터 배포용 VM 인스턴스 생성
* Kubernetes를 구성하기 위한 VM 인스턴스를 생성한다.  
* "ID 및 API 액세스" 항목에서 액세스 범위를 "모든 Cloud API에 대한 전체 액세스 허용" 으로 선택한다. 또는 아래와 같이 서비스 계정에 권한을 설정하고 서비스 계정을 선택해도 된다.
  * GCP 관리 콘솔의 IAM&Admin에서 서비스 계정을 하나 만든다. 서비스 계정이 갖고 있어야 하는 최소한의 권한은 다음과 같다.
  ```shell
  compute admin
  servce account admin
  service account key admin
  service account token creator
  service acount user
  storage admin
  DNS Administrator
  DNS Reader
  ```
  * SSH로 연결 & 업데이트
  ```shell
  jaehoonn@jaehoonn:~$sudo apt-get update
  jaehoonn@jaehoonn:~$sudo apt-get dist-upgrade
  ```

# kubectl 설치
```shell
jaehoonn@jaehoonn:~$curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
jaehoonn@jaehoonn:~$chmod +x ./kubectl
jaehoonn@jaehoonn:~$sudo mv ./kubectl /usr/local/bin/kubectl
```

# kops 설치
```shell
jaehoonn@jaehoonn:~$wget -O kops https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
jaehoonn@jaehoonn:~$chmod +x ./kops
jaehoonn@jaehoonn:~$sudo mv ./kops /usr/local/bin/
```

# GCP Tool 설치
* https://cloud.google.com/sdk/install
```shell
jaehoonn@jaehoonn:~$export CLOUD_SDK_REPO="cloud-sdk-$(lsb_release -c -s)"
jaehoonn@jaehoonn:~$echo "deb http://packages.cloud.google.com/apt $CLOUD_SDK_REPO main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
jaehoonn@jaehoonn:~$sudo apt-get install apt-transport-https ca-certificates
jaehoonn@jaehoonn:~$curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
jaehoonn@jaehoonn:~$sudo apt-get update && sudo apt-get install google-cloud-sdk
```

* 추가 구성요소는 선택적으로 설치 한다. ( sudo apt-get install )
```shell
google-cloud-sdk-app-engine-python
google-cloud-sdk-app-engine-python-extras
google-cloud-sdk-app-engine-java
google-cloud-sdk-app-engine-go
google-cloud-sdk-datalab
google-cloud-sdk-datastore-emulator
google-cloud-sdk-pubsub-emulator
google-cloud-sdk-cbt
google-cloud-sdk-cloud-build-local
google-cloud-sdk-bigtable-emulator
kubectl
```

* GCP를 시작하기 위한 초기화
```shell
jaehoonn@jaehoonn:~$gcloud init
Welcome! This command will take you through the configuration of gcloud.
Your current configuration has been set to: [default]
You can skip diagnostics next time by using the following flag:
  gcloud init --skip-diagnostics
Network diagnostic detects and fixes local network connection issues.
Checking network connection...done.                                                                                
Reachability Check passed.
Network diagnostic passed (1/1 checks passed).
Choose the account you would like to use to perform operations for 
this configuration:
 [1] xxxxxxxxxxxx-compute@developer.gserviceaccount.com
 [2] Log in with a new account
Please enter your numeric choice: 1

You are logged in as: [xxxxxxxxxxxx-compute@developer.gserviceaccount.com].
Pick cloud project to use: 
 [1] jaehoonn-000000
 [2] Create a new project
Please enter numeric choice or text value (must exactly match list 
item): 1

Your current project has been set to: [jaehoonn-000000].

Do you want to configure a default Compute Region and Zone? (Y/n)? y

Which Google Compute Engine zone would you like to use as project 
default?
If you do not specify a zone via a command line flag while working 
with Compute Engine resources, the default is assumed.
 [1] us-east1-b
 [2] us-east1-c
 [3] us-east1-d
 [4] us-east4-c
 [5] us-east4-b
 [6] us-east4-a
 [7] us-central1-c
 [8] us-central1-a
 [9] us-central1-f
 [10] us-central1-b
 [11] us-west1-b
 [12] us-west1-c
 [13] us-west1-a
 [14] europe-west4-a
 [15] europe-west4-b
 [16] europe-west4-c
 [17] europe-west1-b
 [18] europe-west1-d
 [19] europe-west1-c
 [20] europe-west3-c
 [21] europe-west3-a
 [22] europe-west3-b
 [23] europe-west2-c
 [24] europe-west2-b
 [25] europe-west2-a
 [26] asia-east1-b
 [27] asia-east1-a
 [28] asia-east1-c
 [29] asia-southeast1-b
 [30] asia-southeast1-a
 [31] asia-southeast1-c
 [32] asia-northeast1-b
 [33] asia-northeast1-c
 [34] asia-northeast1-a
 [35] asia-south1-c
 [36] asia-south1-b
 [37] asia-south1-af
 [38] australia-southeast1-b
 [39] australia-southeast1-c
 [40] australia-southeast1-a
 [41] southamerica-east1-b
 [42] southamerica-east1-c
 [43] southamerica-east1-a
 [44] asia-east2-a
 [45] asia-east2-b
 [46] asia-east2-c
 [47] asia-northeast2-a
 [48] asia-northeast2-b
 [49] asia-northeast2-c
 [50] europe-north1-a
Did not print [12] options.
Too many options [62]. Enter "list" at prompt to print choices fully.
Please enter numeric choice or text value (must exactly match list 
item): 8

Your project default Compute Engine zone has been set to [us-central1-a].
You can change it by running [gcloud config set compute/zone NAME].
Your project default Compute Engine region has been set to [us-central1].
You can change it by running [gcloud config set compute/region NAME].
Created a default .boto configuration file at [/home/jaehoonn/.boto]. See this file and
[https://cloud.google.com/storage/docs/gsutil/commands/config] for more
information about configuring Google Cloud Storage.
Your Google Cloud SDK is configured and ready to use!
* Commands that require authentication will use xxxxxxxxxxxx-compute@developer.gserviceaccount.com by default
* Commands will reference project `jaehoonn-000000` by default
* Compute Engine commands will use region `us-central1` by default
* Compute Engine commands will use zone `us-central1-a` by default
Run `gcloud help config` to learn how to change individual settings
This gcloud configuration is called [default]. You can create additional configurations if you work with multiple accounts and/or projects.
Run `gcloud topic configurations` to learn more.
Some things to try next:
* Run `gcloud --help` to see the Cloud Platform services you can interact with. And run `gcloud help COMMAND` to get help on any gcloud command.
* Run `gcloud topic --help` to learn about advanced features of the SDK like arg files and output formatting
jaehoonn@jaehoonn:~$
```

* 선택 사항
  * "Please enter your numeric choice: 1"
    * 연결되어 있는 서비스 계정인 "[1] xxxxxxxxxxxx-compute@developer.gserviceaccount.com" 를 선택
  * "Please enter numeric choice or text value (must exactly match list 
item): 1"
    * 연결하고자 하는 프로젝트인 "[1] jaehoonn-000000" 를 선택
  * "Do you want to configure a default Compute Region and Zone? (Y/n)? y"
    * 기본으로 제공되고 있는 region과 zone을 사용
  * "Please enter numeric choice or text value (must exactly match list 
item): 8"

# 스토리지(버킷) 생성
```shell
jaehoonn@jaehoonn:~$ gsutil mb gs://jaehoonn-cluster-state-store
Creating gs://jaehoonn-cluster-state-store/...
jaehoonn@jaehoonn:~$ export KOPS_STATE_STORE=gs://jaehoonn-cluster-state-store/
```

* kops는 클러스터 구성을 유지하기 위한 상태 저장소로서 "Storage"의 "Bucket"을 이용한다.
  * mb: Make buckets
* Bucket의 이름은 필요한 형태로 만들면 된다. (ex, jaehoonn-cluster-state-store)

# 클러스터 생성
* Google Cloud에서 사용할 정보
```shell
jaehoonn@jaehoonn:~$ PROJECT=`gcloud config get-value project`
jaehoonn@jaehoonn:~$ export KOPS_FEATURE_FLAGS=AlphaAllowGCE # to unlock the GCE features
```
* Kubernetes 클러스터 생성(상태 저장소)
```shell
jaehoonn@jaehoonn:~$ kops create cluster --name=jaehoonn-cluster.k8s.local --zones=us-central1-a --state=${KOPS_STATE_STORE} --project=${PROJECT}

ManagedFile/jaehoonn-cluster.k8s.local-addons-dns-controller.addons.k8s.io-k8s-1.6
        Location                addons/dns-controller.addons.k8s.io/k8s-1.6.yaml

  ManagedFile/jaehoonn-cluster.k8s.local-addons-dns-controller.addons.k8s.io-pre-k8s-1.6
        Location                addons/dns-controller.addons.k8s.io/pre-k8s-1.6.yaml

  ManagedFile/jaehoonn-cluster.k8s.local-addons-kube-dns.addons.k8s.io-k8s-1.12
        Location                addons/kube-dns.addons.k8s.io/k8s-1.12.yaml

  ManagedFile/jaehoonn-cluster.k8s.local-addons-kube-dns.addons.k8s.io-k8s-1.6
        Location                addons/kube-dns.addons.k8s.io/k8s-1.6.yaml

  ManagedFile/jaehoonn-cluster.k8s.local-addons-kube-dns.addons.k8s.io-pre-k8s-1.6
        Location                addons/kube-dns.addons.k8s.io/pre-k8s-1.6.yaml

  ManagedFile/jaehoonn-cluster.k8s.local-addons-kubelet-api.rbac.addons.k8s.io-k8s-1.9
        Location                addons/kubelet-api.rbac.addons.k8s.io/k8s-1.9.yaml

  ManagedFile/jaehoonn-cluster.k8s.local-addons-limit-range.addons.k8s.io
        Location                addons/limit-range.addons.k8s.io/v1.5.0.yaml

  ManagedFile/jaehoonn-cluster.k8s.local-addons-rbac.addons.k8s.io-k8s-1.8
        Location                addons/rbac.addons.k8s.io/k8s-1.8.yaml

  ManagedFile/jaehoonn-cluster.k8s.local-addons-storage-gce.addons.k8s.io-v1.6.0
        Location                addons/storage-gce.addons.k8s.io/v1.6.0.yaml

  ManagedFile/jaehoonn-cluster.k8s.local-addons-storage-gce.addons.k8s.io-v1.7.0
        Location                addons/storage-gce.addons.k8s.io/v1.7.0.yaml

  ManagedFile/etcd-cluster-spec-events
        Location                backups/etcd/events/control/etcd-cluster-spec

  ManagedFile/etcd-cluster-spec-main
        Location                backups/etcd/main/control/etcd-cluster-spec

  ManagedFile/manifests-etcdmanager-events
        Location                manifests/etcd/events.yaml

  ManagedFile/manifests-etcdmanager-main
        Location                manifests/etcd/main.yaml

  Secret/admin

  Secret/kube

  Secret/kube-proxy

  Secret/kubelet

  Secret/system:controller_manager

  Secret/system:dns

  Secret/system:logging

  Secret/system:monitoring

  Secret/system:scheduler

  StorageBucketAcl/serviceaccount-backup-readwrite-jaehoonn-cluster-state-store
        Bucket                  jaehoonn-cluster-state-store
        Entity                  xxxx-000000000000-compute@developer.gserviceaccount.com
        Role                    WRITER

  TargetPool/api-jaehoonn-cluster-k8s-local

Must specify --yes to apply changes

Cluster configuration has been created.

Suggestions:
 * list clusters with: kops get cluster
 * edit this cluster with: kops edit cluster jaehoonn-cluster.k8s.local
 * edit your node instance group: kops edit ig --name=jaehoonn-cluster.k8s.local nodes
 * edit your master instance group: kops edit ig --name=jaehoonn-cluster.k8s.local master-us-central1-a

Finally configure your cluster with: kops update cluster --name jaehoonn-cluster.k8s.local --yes

jaehoonn@jaehoonn:~$
```
* Kubernetes 클러스터 생성(VM 인스턴스에 직접 생성)
```shell
jaehoonn@jaehoonn:~$ kops update cluster --name jaehoonn-cluster.k8s.local --yes
I1106 05:42:52.576477   26628 apply_cluster.go:559] Gossip DNS: skipping DNS validation
W1106 05:42:52.641383   26628 external_access.go:36] TODO: Harmonize gcemodel ExternalAccessModelBuilder with awsmodel
W1106 05:42:52.641597   26628 firewall.go:35] TODO: Harmonize gcemodel with awsmodel for firewall - GCE model is way too open
W1106 05:42:52.641685   26628 firewall.go:63] Adding overlay network for X -> node rule - HACK
W1106 05:42:52.641750   26628 firewall.go:64] We should probably use subnets?
W1106 05:42:52.641824   26628 firewall.go:118] Adding overlay network for X -> master rule - HACK
W1106 05:42:52.770317   26628 storageacl.go:71] we need to split master / node roles
W1106 05:42:52.770353   26628 storageacl.go:86] adding bucket level write ACL to gs://jaehoonn-cluster-state-store to support etcd backup
W1106 05:42:52.770450   26628 autoscalinggroup.go:108] enabling storage-rw for etcd backups
I1106 05:42:54.889249   26628 executor.go:103] Tasks: 0 done / 66 total; 39 can run
I1106 05:42:55.253887   26628 address.go:123] GCE creating address: "api-jaehoonn-cluster-k8s-local"
I1106 05:42:55.387139   26628 vfs_castore.go:729] Issuing new certificate: "etcd-peers-ca-events"
I1106 05:42:55.672462   26628 vfs_castore.go:729] Issuing new certificate: "apiserver-aggregator-ca"
I1106 05:42:55.765508   26628 vfs_castore.go:729] Issuing new certificate: "etcd-manager-ca-main"
I1106 05:42:55.985020   26628 vfs_castore.go:729] Issuing new certificate: "etcd-peers-ca-main"
I1106 05:42:56.327905   26628 vfs_castore.go:729] Issuing new certificate: "etcd-clients-ca"
I1106 05:42:56.593223   26628 vfs_castore.go:729] Issuing new certificate: "ca"
I1106 05:42:56.803652   26628 vfs_castore.go:729] Issuing new certificate: "etcd-manager-ca-events"
I1106 05:43:08.063339   26628 executor.go:103] Tasks: 39 done / 66 total; 25 can run
I1106 05:43:08.238400   26628 instancetemplate.go:214] We should be using NVME for GCE
I1106 05:43:08.240753   26628 instancetemplate.go:214] We should be using NVME for GCE
I1106 05:43:08.248537   26628 instancetemplate.go:214] We should be using NVME for GCE
I1106 05:43:08.249786   26628 instancetemplate.go:214] We should be using NVME for GCE
I1106 05:43:10.777593   26628 vfs_castore.go:729] Issuing new certificate: "kube-controller-manager"
I1106 05:43:11.412793   26628 vfs_castore.go:729] Issuing new certificate: "kube-scheduler"
I1106 05:43:11.665669   26628 vfs_castore.go:729] Issuing new certificate: "apiserver-proxy-client"
I1106 05:43:11.698639   26628 vfs_castore.go:729] Issuing new certificate: "apiserver-aggregator"
I1106 05:43:12.071948   26628 vfs_castore.go:729] Issuing new certificate: "kubelet-api"
I1106 05:43:12.189613   26628 vfs_castore.go:729] Issuing new certificate: "kubelet"
I1106 05:43:12.465094   26628 vfs_castore.go:729] Issuing new certificate: "kops"
I1106 05:43:12.539693   26628 vfs_castore.go:729] Issuing new certificate: "master"
I1106 05:43:12.633503   26628 vfs_castore.go:729] Issuing new certificate: "kube-proxy"
I1106 05:43:12.812512   26628 vfs_castore.go:729] Issuing new certificate: "kubecfg"
I1106 05:43:21.263578   26628 executor.go:103] Tasks: 64 done / 66 total; 2 can run
I1106 05:43:32.802331   26628 executor.go:103] Tasks: 66 done / 66 total; 0 can run
I1106 05:43:33.070437   26628 update_cluster.go:294] Exporting kubecfg for cluster
kops has set your kubectl context to jaehoonn-cluster.k8s.local

Cluster is starting.  It should be ready in a few minutes.

Suggestions:
 * validate cluster: kops validate cluster
 * list nodes: kubectl get nodes --show-labels
 * ssh to the master: ssh -i ~/.ssh/id_rsa admin@api.jaehoonn-cluster.k8s.local
 * the admin user is specific to Debian. If not using Debian please use the appropriate user based on your OS.
 * read about installing addons at: https://github.com/kubernetes/kops/blob/master/docs/addons.md.

jaehoonn@jaehoonn:~$
```
* 클러스터 상태 확인(클러스터 생성 완료되고 5~10분뒤에 확인)
```shell
jaehoonn@jaehoonn:~$ kops validate cluster
Using cluster from kubectl context: jaehoonn-cluster.k8s.local

Validating cluster jaehoonn-cluster.k8s.local

I1106 05:50:12.213436   26733 gce_cloud.go:273] Scanning zones: [us-central1-c us-central1-a us-central1-f us-central1-b]
INSTANCE GROUPS
NAME                    ROLE    MACHINETYPE     MIN     MAX     SUBNETS
master-us-central1-a    Master  n1-standard-1   1       1       us-central1
nodes                   Node    n1-standard-2   2       2       us-central1

NODE STATUS
NAME                            ROLE    READY
master-us-central1-a-dg9q       master  True
nodes-2f58                      node    True
nodes-9t48                      node    True

Your cluster jaehoonn-cluster.k8s.local is ready
jaehoonn@jaehoonn:~$
```
* kubectl 동작 확인
```shell
jaehoonn@jaehoonn:~$ kubectl get pods --all-namespaces
NAMESPACE     NAME                                                READY   STATUS    RESTARTS   AGE
kube-system   dns-controller-7f9457558d-fmnpk                     1/1     Running   0          4m53s
kube-system   etcd-manager-events-master-us-central1-a-dg9q       1/1     Running   0          3m26s
kube-system   etcd-manager-main-master-us-central1-a-dg9q         1/1     Running   0          4m4s
kube-system   kube-apiserver-master-us-central1-a-dg9q            1/1     Running   1          4m27s
kube-system   kube-controller-manager-master-us-central1-a-dg9q   1/1     Running   0          4m29s
kube-system   kube-dns-66d58c65d5-6nllr                           3/3     Running   0          4m53s
kube-system   kube-dns-66d58c65d5-6qk7c                           3/3     Running   0          3m55s
kube-system   kube-dns-autoscaler-6567f59ccb-xzp98                1/1     Running   0          4m51s
kube-system   kube-proxy-master-us-central1-a-dg9q                1/1     Running   0          4m6s
kube-system   kube-proxy-nodes-2f58                               1/1     Running   0          3m41s
kube-system   kube-proxy-nodes-9t48                               1/1     Running   0          3m25s
kube-system   kube-scheduler-master-us-central1-a-dg9q            1/1     Running   0          4m33s
jaehoonn@jaehoonn:~$
```
