---
title: Kubernetes on Azure のチュートリアル - アプリの準備
description: AKS チュートリアル - アプリの準備
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 3e500ec0c7acbf8d8e10756c944516cd7e589610
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101112"
---
# <a name="tutorial-prepare-application-for-azure-kubernetes-service-aks"></a>チュートリアル: Azure Kubernetes Service (AKS) 用のアプリケーションの準備

このチュートリアルは、7 つあるうちの 1 番目で、複数コンテナー アプリケーションを Kubernetes で使用する準備をします。 手順は次のとおりです。

> [!div class="checklist"]
> * GitHub からアプリケーション ソースを複製する
> * アプリケーション ソースからコンテナー イメージを作成する
> * ローカル Docker 環境でアプリケーションをテストする

完了後、次のアプリケーションにローカル開発環境からアクセスできます。

![Azure 上の Kubernetes クラスターの図](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

この後のチュートリアルでは、このコンテナ イメージが Azure Container Registry にアップロードされ、AKS クラスターで実行されます。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルの前提として、コンテナー、コンテナー イメージ、基本 Docker コマンドなど、Docker のコア概念を基本的に理解している必要があります。 必要な場合は、[Docker の入門][docker-get-started]に関するドキュメントでコンテナーの基礎を参照してください。

このチュートリアルを完了するには、Docker 開発環境が必要です。 Docker では、[Mac][docker-for-mac]、[Windows][docker-for-windows]、または [Linux][docker-for-linux] システムで Docker を簡単に構成できるパッケージが提供されています。

Azure Cloud Shell には、このチュートリアルの各ステップを完了するのに必要な Docker コンポーネントがすべて含まれているわけではありません。 そのため、完全な Docker 開発環境の使用をお勧めします。

## <a name="get-application-code"></a>アプリケーションのコードを入手する

このチュートリアルで使うサンプル アプリケーションは、基本的な投票アプリです。 アプリケーションは、フロントエンド Web コンポーネントとバックエンド Redis インスタンスで構成されています。 Web コンポーネントは、カスタム コンテナー イメージにパッケージ化されています。 Redis インスタンスでは、Docker Hub から変更されていないイメージを使用します。

アプリケーションのコピーを開発環境にダウンロードするには、git を使います。

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

ディレクトリを変更して、複製されたディレクトリから作業するようにします。

```console
cd azure-voting-app-redis
```

ディレクトリ内には、アプリケーションのソース コード、事前作成された Docker Compose ファイル、および Kubernetes マニフェスト ファイルがあります。 これらのファイルは、チュートリアル セット全体で使用されます。

## <a name="create-container-images"></a>コンテナー イメージを作成する

[Docker Compose][docker-compose] は、コンテナー イメージと複数のコンテナー アプリケーションのデプロイからのビルドを自動化するために使用できます。

コンテナー イメージの作成、Redis イメージのダウンロード、およびアプリケーションの起動を行うために、`docker-compose.yaml` ファイルを実行します。

```console
docker-compose up -d
```

完了したら、[docker images][docker-images] コマンドを使って、作成されたイメージを確認します。

```console
docker images
```

3 つのイメージがダウンロードまたは作成されたことに注目してください。 `azure-vote-front` イメージはアプリケーションを含み、`nginx-flask` イメージをベースとして使用します。 `redis` イメージは、Redis インスタンスを起動するために使用されます。

```
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

[docker ps][docker-ps] コマンドを実行して、実行中のコンテナーを確認します。

```console
docker ps
```

出力:

```
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>ローカルでアプリケーションをテストする

実行中のアプリケーションを探すには、http://localhost:8080 を参照します。

![Azure 上の Kubernetes クラスターの図](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

アプリケーションの機能を検証したので、実行中のコンテナーを停止して削除できます。 コンテナー イメージは削除しないでください。 `azure-vote-front` イメージは、次のチュートリアルで Azure Container Registry インスタンスにアップロードされます。

実行中のコンテナーを停止するには、次を実行します。

```console
docker-compose stop
```

次のコマンドで、停止しているコンテナーとリソースを削除します。

```console
docker-compose down
```

完了すると、Azure Vote アプリケーションを含むコンテナー イメージが完成します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、アプリケーションをテストし、アプリケーション用のコンテナー イメージを作成しました。 次の手順を完了しました。

> [!div class="checklist"]
> * GitHub からアプリケーション ソースを複製する
> * アプリケーション ソースからコンテナー イメージを作成する
> * ローカル Docker 環境でアプリケーションアプリケーションをテストする

次のチュートリアルでは、Azure Container Registry にコンテナー イメージを格納する方法について学習します。

> [!div class="nextstepaction"]
> [Azure Container Registry にイメージをプッシュする][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md