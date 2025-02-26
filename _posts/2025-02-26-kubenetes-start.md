---
title: Minikube 소개
description: >-
  Kubernetes를 익히기 위한 도구
author: dgkim
date: 2025-02-26 08:32:00 +0900
categories: [개발 일상]
tags: [팁]
pin: false
media_subpath: '/assets/posts/20250226'
comments: true
---
# Minikube 
 
 Minikube는 로컬 컴퓨터 전용 Kubernetes이다. 개인의 연습과 개발을 위해 사용된다.
Kubernetes를 셋업하는 방법 중에 하나이다.

## 설치

[Minikube](https://minikube.sigs.k8s.io/docs/start/?arch=%2Flinux%2Fx86-64%2Fstable%2Fbinary+download) 사이트에 접근해서 OS에 맞는 방법으로 설치하면 된다. 

Minikube는 설치시에 가상환경을 요구한다. Docker + Minikube 조합이 좋다고 생각한다. 컨테이너를 다루기 위해서는 Docker이 필요하고 그것을 Minikube의 driver로 제공할 수 있다.

``` bash
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```

현재 Linux를 사용하고 있으므로, 위의 명령으로 쉽게 설치할 수 있다.
다른 OS의 경우는 Minikube 공식 페이지를 읽고 설치를 하면 될 것 같다.

[kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/): Kubernetes의 명령 라인 도구 이다. 설치 할수 있다. 별도의 설정 과정은 없었던 것 같지만 [kubectl의 설정방법](https://minikube.sigs.k8s.io/docs/handbook/kubectl/)을 확인해보면 좋을 것 같다. 

* 정확히 얘기하면 마스터 노드에 명령을 보내는 도구이다. 마스터 노드는 다음에 기회가 되면 요약해야 겠다.

## 활용

쿠버네티스는 아래의 객체와 동작한다.
- pods: 컨테이너에게 환경을 제공하고 실행하는 객체
- deployments: 최종 배포 상태를 정의하는 객체. kubernets가 최종적으로 맞추려고 하는 상태
- service: 쿠버네스트 내부의 network. pod가 외부에 공개되려면 service가 필요하다.
- volume: 쿠버네티스 내부의 저장소
- 등등...

Docker을 이용해서 DockerHub에 이미지를 업로드 한다. Docker Hub에 가입이 되어 있어야 하고 가입이 되었다면 이미지 repository를 사용할 수 있다. 

> image repository는 계정별로 private 설정되는 것은 하나뿐이다. 그냥 public으로 쓰는 것이 좋을 것 같다.

minikube 실행 `minikube start` 

``` bash
$ minikube start
😄  minikube v1.35.0 on Ubuntu 24.04
✨  Using the docker driver based on existing profile
👍  Starting "minikube" primary control-plane node in "minikube" cluster
🚜  Pulling base image v0.0.46 ...
🔄  Restarting existing docker container for "minikube" ...
🐳  Preparing Kubernetes v1.32.0 on Docker 27.4.1 ...
🔎  Verifying Kubernetes components...
    ▪ Using image docker.io/kubernetesui/dashboard:v2.7.0
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
    ▪ Using image docker.io/kubernetesui/metrics-scraper:v1.0.8
❗  Enabling 'storage-provisioner' returned an error: running callbacks: [sudo KUBECONFIG=/var/lib/minikube/kubeconfig /var/lib/minikube/binaries/v1.32.0/kubectl apply --force -f /etc/kubernetes/addons/storage-provisioner.yaml: Process exited with status 1
stdout:

stderr:
error: error validating "/etc/kubernetes/addons/storage-provisioner.yaml": error validating data: failed to download openapi: Get "https://localhost:8443/openapi/v2?timeout=32s": dial tcp [::1]:8443: connect: connection refused; if you choose to ignore these errors, turn validation off with --validate=false
]
💡  Some dashboard features require the metrics-server addon. To enable all features please run:

	minikube addons enable metrics-server

🌟  Enabled addons: default-storageclass, dashboard
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default

```
간혹 가상환경을 못찾는 문제가 있었는데 그럴때는 `minikube start --driver=docker`로 명령을 내리면 되었다.

물론 Docker는 깔려 있어야 한다.

뭔가 오류가 보이는데.. 이상없으면 그냥 진행했다. `minikube status`는 현재 상태를 알아보는 명령이다.

``` bash
$ minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured

```

minikube는 대시보드를 제공한다. 현재 상태에 대해서 직관적으로 알 수 있어서 띄워두고 상태를 확인 하는 편이다.

``` bash
$ minikube dashboard > $HOME/project/docker/dashboard.log 2>&1 &
[1] 58920
```

리다이렉팅을 하는 이유는 현재 창을 그대로 쓰고 싶은데 리다이렉팅을 쓰고 `&` 이렇게 했을 때
Detach가 원활히 되지 않았다. 이렇게 써두면 shell이 꺼질 때 같이 꺼진다. 아예 backgroup process로 
올리고 싶으면 `disown`을 쓰면 된다.


``` bash
$ minikube dashboard > $HOME/project/docker/dashboard.log 2>&1 & disown
```

![Minikube dashboard](minikube-dashboard.png)

