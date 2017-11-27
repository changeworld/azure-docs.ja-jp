---
title: "Azure Container Instances チュートリアル - アプリの準備"
description: "Azure Container Instances にデプロイするアプリを準備します"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 3ae8eb53843e31ec66af52be9b04fbb626093f5b
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2017
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>デプロイするコンテナーを Azure Container Instances に作成する

Azure Container Instances では、仮想マシンをプロビジョニングしたり、上位レベルのサービスを採用したりせずに、Azure のインフラストラクチャ上に Docker コンテナーをデプロイすることができます。 このチュートリアルでは、Node.js で小さな Web アプリケーションを構築し、Azure Container Instances を使用して実行できるコンテナーにそのアプリケーションをパッケージ化します。 説明内容は次のとおりです。

> [!div class="checklist"]
> * GitHub からアプリケーション ソースを複製する
> * アプリケーション ソースからコンテナー イメージを作成する
> * ローカル Docker 環境でイメージをテストする

後続のチュートリアルでは、Azure Container Registry にイメージをアップロードした後、Azure Container Instances にデプロイします。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルでは、Azure CLI バージョン 2.0.21 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。

このチュートリアルの前提として、コンテナー、コンテナー イメージ、基本 `docker` コマンドなど、Docker のコア概念を基本的に理解している必要があります。 必要な場合は、[Docker の入門]( https://docs.docker.com/get-started/)に関するドキュメントでコンテナーの基礎を参照してください。

このチュートリアルを完了するには、Docker 開発環境が必要です。 Docker では、[Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、または [Linux](https://docs.docker.com/engine/installation/#supported-platforms) システムで Docker を簡単に構成できるパッケージが提供されています。

Azure Cloud Shell には、このチュートリアルの各ステップを完了するのに必要な Docker コンポーネントがすべて含まれているわけではありません。 そのため、Azure CLI および Docker 開発環境のローカル インストールをお勧めします。

## <a name="get-application-code"></a>アプリケーションのコードを入手する

このチュートリアルのサンプルには、[Node.js](http://nodejs.org) で構築した単純な Web アプリケーションが含まれます。 このアプリは、次のような静的な HTML ページとして機能します。

![ブラウザーに表示されたチュートリアル アプリ][aci-tutorial-app]

GitHub を使用してサンプルをダウンロードします。

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>コンテナー イメージを構築する

サンプル リポジトリで提供される Dockerfile は、コンテナーの構築方法を示しています。 その方法は、[Alpine Linux](https://alpinelinux.org/) に基づく[公式 Node.js イメージ][dockerhub-nodeimage]から始まります。これは、コンテナーで使用するのに適した小規模なディストリビューションです。 次に、アプリケーション ファイルをコンテナーにコピーし、ノード パッケージ マネージャーを使用して依存関係をインストールして、最後にアプリケーションを起動します。

```Dockerfile
FROM node:8.2.0-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

`docker build` コマンドを使用してコンテナー イメージを作成し、これを *aci-tutorial-app* としてタグ付けします。

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

`docker build` コマンドの出力は次のようになります (わかりやすくするために切り詰められています)。

```bash
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.2.0-alpine
8.2.0-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.2.0-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

`docker images` を使用して、構築されたイメージを参照します。

```bash
docker images
```

出力:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

## <a name="run-the-container-locally"></a>コンテナーをローカルで実行する

Azure Container Instances にコンテナーをデプロイする前に、ローカルでコンテナーを実行して、コンテナーが動作することを確認します。 `-d` スイッチを使用すると、コンテナーをバックグラウンドで実行できます。一方、`-p` スイッチでは、コンピューター上の任意のポートをコンテナーのポート 80 にマップすることができます。

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

ブラウザーで http://localhost:8080 を開き、コンテナーが実行されていることを確認します。

![ブラウザーでのアプリのローカル実行][aci-tutorial-app-local]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Container Instances にデプロイできるコンテナー イメージを作成しました。 次の手順を完了しました。

> [!div class="checklist"]
> * GitHub からのアプリケーション ソースの複製
> * アプリケーション ソースからのコンテナー イメージの作成
> * コンテナーのローカルでのテスト

次のチュートリアルでは、Azure Container Registry にコンテナー イメージを格納する方法について学習します。

> [!div class="nextstepaction"]
> [Azure Container Registry にイメージをプッシュする](./container-instances-tutorial-prepare-acr.md)

<!-- LINKS -->
[dockerhub-nodeimage]: https://store.docker.com/images/node

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png