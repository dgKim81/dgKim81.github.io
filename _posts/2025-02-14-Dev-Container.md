---
title: Visual Code Dev Container 소개
description: >-
  개발 환경에 대한 편의 도구
author: dgkim
date: 2025-02-14 12:56:00 +0900
categories: [개발 일상]
tags: [Visual Code, 팁]
pin: false
media_subpath: '/assets/posts/20250214'
comments: true
---

# Visual Studio Code Dev Container
## Visual Studio Code Dev Containers 확장 기능 설치

visual studio code에서 dev container 설치하기.
![Desktop View](setup-01.png)

Extensions에서 dev container라고 검색을 하면 찾을 수 있다.

개발 환경을 셋팅한채로 도커화해서 구성원들이 일관된 환경에서 작업할 수 있도록 지원하는 기능이다.
도커를 기반으로 동작하므로 도커를 설치 해야한다. 도커의 설치는 공식 홈페이지에서 확인할 수 있다.
Windows는 Docker Desktop을 설치했던 것 같다. WSL2로 설치해서 사용해 봤다. 
파일 시스템 문제로 수정된 파일을 인식하지 못하는 문제가 있다.

[도커 공식 홈페이지에서 설치 안내](https://docs.docker.com/engine/install/ubuntu/)

## 처음으로 dev-container 만나기
일단 공식 설명서에 있는 방법을 따라가기로 했다.

``` bash
$ git clone  https://github.com/Microsoft/vscode-remote-try-node
Cloning into 'vscode-remote-try-node'...
remote: Enumerating objects: 384, done.
remote: Counting objects: 100% (176/176), done.
remote: Compressing objects: 100% (29/29), done.
remote: Total 384 (delta 161), reused 147 (delta 147), pack-reused 208 (from 2)
Receiving objects: 100% (384/384), 111.23 KiB | 599.00 KiB/s, done.
Resolving deltas: 100% (193/193), done.

$ cd vscode-remote-try-node/

code .
``` 

## 살펴 보기

![프로젝트 열기](open-dev-container-01.png)
일단 간단한 Nodejs 프로젝트 이다.

![프로젝트 열기](open-dev-container-04.png)
처음에 접근했을 때는 우측 아래에 dev container로 열겠느냐라는 물음이 왔었다.
열겠다고 하니 뭔가 바뀌었다.


### dev-container.json 파일 확인

가장 먼저 눈에 띈다. 아마도 설정 파일인 듯 하다.

- name : 이름인 것 같고..
- image : docker 이미지를 의미하는 것 같다. [Microsoft Dev Container를 위한 Docker Image](https://mcr.microsoft.com/) 이쪽에서 가져오는 것 같은데 어떻게 찾는지는 조금 더 찬찬히 볼때 살펴보기로 하고,
- customizations: 뭔가 개발 툴에 대한 설정 같다.
- portsAttributes: 컨테이너 실행 시에 컨테이너와 로컬의 포트 맵핑인 것 같다.
- postCreateCommand : 컨테이너 실행 시에 바로 실행할 명령이다. container에서 개발 환경을 설정하는 것 같다.

``` json
// For format details, see https://aka.ms/devcontainer.json. For config options, see the
// README at: https://github.com/devcontainers/templates/tree/main/src/javascript-node
{
	"name": "Node.js",
	// Or use a Dockerfile or Docker Compose file. More info: https://containers.dev/guide/dockerfile
	"image": "mcr.microsoft.com/devcontainers/javascript-node:1-18-bullseye",

	// Features to add to the dev container. More info: https://containers.dev/features.
	// "features": {},

	// Configure tool-specific properties.
	"customizations": {
		// Configure properties specific to VS Code.
		"vscode": {
			"settings": {},
			"extensions": [
				"streetsidesoftware.code-spell-checker"
			]
		}
	},

	// Use 'forwardPorts' to make a list of ports inside the container available locally.
	// "forwardPorts": [3000],

	// Use 'portsAttributes' to set default properties for specific forwarded ports. 
	// More info: https://containers.dev/implementors/json_reference/#port-attributes
	"portsAttributes": {
		"3000": {
			"label": "Hello Remote World",
			"onAutoForward": "notify"
		}
	},

	// Use 'postCreateCommand' to run commands after the container is created.
	"postCreateCommand": "yarn install"

	// Uncomment to connect as root instead. More info: https://aka.ms/dev-containers-non-root.
	// "remoteUser": "root"
}
```

### docker 확인

도커에서 받은 이미지가 확인 된다.
``` bash
$ docker images
REPOSITORY                                                                                        TAG       IMAGE ID       CREATED          SIZE
vsc-vscode-remote-try-node-e1c0e39276fcb34b20614b3cb7df806edf450d4021d4f5b0df88ce2537fc84f5-uid   latest    42cdc0ddfb51   30 minutes ago   1.32GB
```

실행 중인 컨테이너
``` bash
$ docker ps
CONTAINER ID   IMAGE                                                                                             COMMAND                  CREATED          STATUS          PORTS     NAMES
3d99e6944d69   vsc-vscode-remote-try-node-e1c0e39276fcb34b20614b3cb7df806edf450d4021d4f5b0df88ce2537fc84f5-uid   "/bin/sh -c 'echo Co…"   10 minutes ago   Up 10 minutes             admiring_dhawan
```

컨테이너 정보 확인
``` bash
$ docker container inspect 3d99e6944d69

...
            "Mounts": [
                {
                    "Type": "bind",
                    "Source": ".../devContainer/vscode-remote-try-node",
                    "Target": "/workspaces/vscode-remote-try-node"
                },
                {
                    "Type": "volume",
                    "Source": "vscode",
                    "Target": "/vscode"
                }
            ],

...

```
예상대로 개발 폴더를 바운드 마운트로 잡고 있다.
그런데 포트 맵핑은 확인을 못했다. 그래서 직접 올려 보았다

``` bash
docker run --rm -p 3001:3000 --name test-dev-container -it -v "${pwd}/:/workspaces/vscode-remote-try-node" -v "vscode:/vscode" 42cdc0ddfb51

$ docker ps
CONTAINER ID   IMAGE                                                                                             COMMAND                  CREATED          STATUS          PORTS                                         NAMES
01a9f85491b4   42cdc0ddfb51                                                                                      "docker-entrypoint.s…"   2 minutes ago    Up 2 minutes    0.0.0.0:3001->3000/tcp, [::]:3001->3000/tcp   test-dev-container
3d99e6944d69   vsc-vscode-remote-try-node-e1c0e39276fcb34b20614b3cb7df806edf450d4021d4f5b0df88ce2537fc84f5-uid   "/bin/sh -c 'echo Co…"   24 minutes ago   Up 24 minutes                                                 admiring_dhawan
```
직접 올릴 때는 포트가 정보에 나타났는데 이 부분은 조금 이상하다. 

### 실행해보기

소스는 Node js Express 서버 구현이다. 바로 실행 버튼을 "F5"를 입력하니 실행이 된다.
![실행 결과](run-nodejs-01.png)

### vs code remote explorer 확인

![Remote Explorer 확인](dev-container-info-01.png)
명령으로 이것 저것 확인하고 있었는데, 옆에 쉽게 볼 수 있는 메뉴가 있다.

## 결론

개발 환경을 구성하고 공유하기에 매우 좋은 것 같다. 일단 이미지를 만들거나 아니면 어떤 이미지를 베이스로 해서
바로 개발 환경을 셋팅할 수 있을 것 같다.

java spring 이나, 혹은 react, node 같은 경우는 소스를 받아도 셋팅이 까다로운 경우가 좀 있다.
특히 이미 어느 정도 진행중인 것을 디자이너와 협업하는 경우 원격지에서는 셋팅해주기가 곤란한 경우도 있었다.
조금 디테일 활용방법을 익혀서 프로젝트 만들때 구성해두면 좋을 것 같다는 생각이 든다.

vs code에서 container에 디버거를 바로 붙이면 편리할 텐데 생각해서 찾아보니 매우 좋은 기능이 있었다.
