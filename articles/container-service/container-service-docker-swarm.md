<properties
   pageTitle="Docker Swarm を使用した Azure コンテナー サービスのコンテナー管理| Microsoft Azure"
   description="Azure コンテナー サービスで Docker Swarm にコンテナーをデプロイする"
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker、コンテナー、マイクロ サービス、Mesos、Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/13/2016"
   ms.author="nepeters"/>

# Docker Swarm でのコンテナーの管理

Docker Swarm は、コンテナーにまとめたワークロードをプールされた Docker ホストのセット全体にデプロイする環境を提供します。Docker Swarm は、ネイティブ Docker API を使用します。Docker Swarm 上のコンテナーを管理するワークフローは、1 つのコンテナー ホスト上の場合とほぼ同じです。このドキュメントでは、Docker Swarm の Azure コンテナー サービス インスタンスで、コンテナーにまとめたワークロードをデプロイする簡単な例について説明します。Docker Swarm の詳細なドキュメントについては、[Docker.com の Docker Swarm](https://docs.docker.com/swarm/) を参照してください。

このドキュメントで行う演習の前提条件:

[Azure コンテナー サービスに Swarm クラスターを作成する](./container-service-deployment.md)

[Azure コンテナー サービスの Swarm クラスターと接続する](./container-service-connect.md)

## 新しいコンテナーをデプロイする

Docker Swarm で新しいコンテナーを作成するには、`docker run` コマンドを使用します。この例では、`yeasy/simple-web` イメージからコンテナーを作成します。


```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

コンテナーを作成したら、`docker ps` を使用してコンテナーに関する情報を返します。ここで、コンテナーをホストする Swarm エージェントが一覧に表示されます。


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

このコンテナーで実行されているアプリケーションには、Swarm エージェント ロード バランサーのパブリック DNS エージェントを使用してアクセスできます。この情報は、Azure ポータルで確認できます。


![](media/real-visit.jpg)

## 複数のコンテナーをデプロイする

Docker Swarm クラスターで複数のコンテナーを開始した場合、`docker ps` コマンドを使用して、コンテナーが実行されているホストを確認できます。この例では、3 つのコンテナーは 3 つの Swarm エージェントが均等に分散されます。


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## Docker Compose を使用してコンテナーをデプロイする

Docker Compose を使用して、複数のコンテナーのデプロイと構成を自動化することができます。自動化するには、SSH トンネルが作成され、DOCKER\_HOST 変数が設定されている必要があります。

ローカル システムに docker-compose.yml を作成します。サンプルについては、[こちら](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml)を参照してください。

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

`docker-compose up -d` を実行してコンテナーのデプロイを開始します。


```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

最後に、実行中のコンテナーの一覧を返すことができます。この一覧には、Docker Compose でデプロイされたコンテナーが反映されます。


```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

## 次のステップ:

[Docker Swarm の詳細](https://docs.docker.com/swarm/)。

<!---HONumber=AcomDC_0420_2016-->