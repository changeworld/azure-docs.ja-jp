---
title: "Azure Container Instances チュートリアル - アプリの準備"
description: "Azure Container Instances チュートリアル第 1 部 (全 3 部) - Azure Container Instances にデプロイするアプリを準備する"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 5012412ec642a04102836274caea253635376efb
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>デプロイするコンテナーを Azure Container Instances に作成する

Azure Container Instances では、仮想マシンをプロビジョニングしたり、上位レベルのサービスを採用したりせずに、Azure のインフラストラクチャ上に Docker コンテナーをデプロイすることができます。 このチュートリアルでは、Node.js で小さな Web アプリケーションを構築し、Azure Container Instances を使用して実行できるコンテナーにそのアプリケーションをパッケージ化します。

シリーズの第 1 部となるこの記事では、次の内容を学習します。

> [!div class="checklist"]
> * GitHub からアプリケーション ソース コードを複製する
> * アプリケーション ソースからコンテナー イメージを作成する
> * ローカル Docker 環境でイメージをテストする

後続のチュートリアルでは、Azure Container Registry にイメージをアップロードした後、Azure Container Instances にデプロイします。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルでは、Azure CLI バージョン 2.0.23 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール][azure-cli-install]」をご覧ください。

このチュートリアルの前提として、コンテナー、コンテナー イメージ、基本 `docker` コマンドなど、Docker のコア概念を基本的に理解している必要があります。 必要な場合は、[Docker の入門][docker-get-started]に関するドキュメントでコンテナーの基礎を参照してください。

このチュートリアルを完了するには、ローカルにインストールされた Docker 開発環境が必要です。 Docker では、[Mac][docker-mac]、[Windows][docker-windows]、または [Linux][docker-linux] システムで Docker を簡単に構成できるパッケージが提供されています。

Azure Cloud Shell には、このチュートリアルの各ステップを完了するのに必要な Docker コンポーネントがすべて含まれているわけではありません。 このチュートリアルを完了するには、ローカル コンピューターに Azure CLI と Docker 開発環境をインストールする必要があります。

## <a name="get-application-code"></a>アプリケーションのコードを入手する

このチュートリアルのサンプルには、[Node.js][nodejs] で構築した単純な Web アプリケーションが含まれます。 このアプリは、次のような静的な HTML ページとして機能します。

![ブラウザーに表示されたチュートリアル アプリ][aci-tutorial-app]

GitHub を使用してサンプルをダウンロードします。

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>コンテナー イメージを構築する

サンプル リポジトリで提供される Dockerfile は、コンテナーの構築方法を示しています。 その方法は、[Alpine Linux][alpine-linux] に基づく[公式 Node.js イメージ][docker-hub-nodeimage]から始まります。これは、コンテナーで使用するのに適した小規模なディストリビューションです。 次に、アプリケーション ファイルをコンテナーにコピーし、ノード パッケージ マネージャーを使用して依存関係をインストールして、最後にアプリケーションを起動します。

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/ /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

[docker build][docker-build] コマンドを使用してコンテナー イメージを作成し、これを *aci-tutorial-app* としてタグ付けします。

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

[docker build][docker-build] コマンドの出力は次のようになります (わかりやすくするために切り詰められています)。

```bash
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.9.3-alpine
8.9.3-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.9.3-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

[docker images][docker-images] コマンドを使用して、構築されたイメージを確認します。

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

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Container Instances にデプロイできるコンテナー イメージを作成しました。 次の手順を完了しました。

> [!div class="checklist"]
> * GitHub からのアプリケーション ソースの複製
> * アプリケーション ソースからのコンテナー イメージの作成
> * コンテナーのローカルでのテスト

次のチュートリアルでは、Azure Container Registry にコンテナー イメージを格納する方法について学習します。

> [!div class="nextstepaction"]
> [Azure Container Registry にイメージをプッシュする](./container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
