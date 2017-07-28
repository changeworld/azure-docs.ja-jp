---
title: "Azure Container Service チュートリアル - アプリの準備 | Microsoft Docs"
description: "Azure Container Service チュートリアル - アプリの準備"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 49d727de69c700af0ae6402ad54b5880010ebb3b
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017

---

# <a name="create-container-images-to-be-used-with-azure-container-service"></a>Azure Container Service で使用するコンテナー イメージを作成する

このチュートリアルでは、Kubernetes 用にアプリケーションを準備します。 手順は次のとおりです。  

> [!div class="checklist"]
> * GitHub からアプリケーション ソースを複製する  
> * アプリケーション ソースからコンテナー イメージを作成する
> * ローカル Docker 環境でイメージをテストする

後のチュートリアルでは、これらのコンテナー イメージを Azure Container Registry にアップロードした後、Azure でホストされている Kubernetes クラスターで実行します。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルの前提として、コンテナー、コンテナー イメージ、基本 Docker コマンドなど、Docker のコア概念を基本的に理解している必要があります。 必要な場合は、[Docker の入門]( https://docs.docker.com/get-started/)に関するドキュメントでコンテナーの基礎を参照してください。 

このチュートリアルを完了するには、Docker 開発環境が必要です。 Docker では、[Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、または [Linux](https://docs.docker.com/engine/installation/#supported-platforms) システムで Docker を簡単に構成できるパッケージが提供されています。

## <a name="get-application-code"></a>アプリケーションのコードを入手する

このチュートリアルで使うサンプル アプリケーションは、基本的な投票アプリです。 アプリケーションは、フロントエンド Web コンポーネントとバックエンド データベースで構成されています。 

アプリケーションのコピーを開発環境にダウンロードするには、git を使います。

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

アプリケーション ディレクトリには、作成済みの Docker ファイルと Kubernetes マニフェスト ファイルがあります。 これらのファイルを使って、チュートリアル セット全体のアセットを作成します。 

## <a name="create-container-images"></a>コンテナー イメージを作成する

アプリケーション フロントエンドのコンテナー イメージを作成するには、[docker build](https://docs.docker.com/engine/reference/commandline/build/) コマンドを使います。

```bash
docker build ./azure-voting-app/azure-vote -t azure-vote-front
```

同じコマンドをもう一度実行して、今度はバックエンドのコンテナー イメージを作成します。

```bash
docker build ./azure-voting-app/azure-vote-mysql -t azure-vote-back
```

完了したら、`docker images` コマンドを使って作成されたイメージを確認します。 

```bash
docker images
```

出力:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              c13c4f50ede1        39 seconds ago       716 MB
azure-vote-back              latest              33fe5afc1885        About a minute ago   407 MB
mysql                        latest              e799c7f9ae9c        4 weeks ago          407 MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        8 months ago         694 MB
```

## <a name="test-application"></a>アプリケーションをテストする

2 つのコンテナー イメージを作成したので、ローカル開発環境でこれらのイメージをテストします。 

最初に、Docker ネットワークを作成します。 このネットワークは、コンテナー間の通信に使われます。  

```bash
docker network create azure-vote
```

`docker run` コマンドを使って、バックエンド コンテナー イメージのインスタンスを実行します。

この例では、mysql データベース ファイルがコンテナー内に格納されます。 このアプリケーションを Kubernete クラスターに移動した後、外部データ ボリュームを使ってデータベース ファイルを格納します。 また、MySQL の資格情報を設定するには環境変数が使われています。

```bash
docker run -p 3306:3306 --name azure-vote-back -d --network azure-vote -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote azure-vote-back 
```

フロントエンド コンテナー イメージのインスタンスを実行します。

環境変数を使って、データベースの接続情報が構成されます。

```bash
docker run -d -p 8080:80 --name azure-vote-front --network=azure-vote -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote -e MYSQL_HOST=azure-vote-back azure-vote-front
```

完了したら、`docker ps` を実行して実行中のコンテナーを確認します。  

```bash
docker ps
```

出力:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                           NAMES
3aa02e8ae965        azure-vote-front     "/usr/bin/supervisord"   59 seconds ago      Up 57 seconds       443/tcp, 0.0.0.0:8080->80/tcp   flaskmysqlvote_azure-vote-front_1
5ae60b3ba181        azure-vote-backend   "docker-entrypoint..."   59 seconds ago      Up 58 seconds       0.0.0.0:3306->3306/tcp          azure-vote-back
```

実行中のアプリケーションを探すには、`http://localhost:8080` を参照します。 アプリケーションの初期化には数秒かかります。 エラーが発生した場合は、もう一度やり直してください。

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-tutorials/vote-app.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

アプリケーションの機能を検証したので、実行中のコンテナーを停止して削除できます。 コンテナー イメージは削除しないでください。 これらのイメージは、次のチュートリアルで Azure Container Registry インスタンスにアップロードされます。

[docker rm](https://docs.docker.com/engine/reference/commandline/rm/) コマンドを使って、フロントエンド コンテナーを停止して削除します。 

```bash
docker rm -f azure-vote-front
```

[docker rm](https://docs.docker.com/engine/reference/commandline/rm/) コマンドを使って、バックエンド コンテナーを停止して削除します。 

```bash
docker rm -f azure-vote-back
```

[docker network rm](https://docs.docker.com/engine/reference/commandline/network_rm/) コマンドを使って、ネットワークを削除します。

```bash
docker network rm azure-vote
```

完了すると、Azure Vote アプリケーションを構成する 2 つのコンテナー イメージが完成します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、アプリケーションをテストし、アプリケーション用のコンテナー イメージを作成しました。 次の手順を完了しました。

> [!div class="checklist"]
> * GitHub からアプリケーション ソースを複製する  
> * アプリケーション ソースからコンテナー イメージを作成する
> * ローカル Docker 環境でイメージをテストする

次のチュートリアルでは、Azure Container Registry にコンテナー イメージを格納する方法について学習します。

> [!div class="nextstepaction"]
> [Azure Container Registry にイメージをプッシュする](./container-service-tutorial-kubernetes-prepare-acr.md)
