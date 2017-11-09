---
title: "Azure Container Registry チュートリアル - geo レプリケーション Azure Container Registry の準備"
description: "Azure Container Registry を作成し、geo レプリケーションを構成して、Docker イメージを準備し、それをレジストリにデプロイします。 3 部構成のシリーズのパート 1。"
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: mmacy
tags: acr, azure-container-registry, geo-replication
keywords: "Docker、コンテナー、レジストリ、Azure"
ms.service: container-registry
ms.devlang: 
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 88feffc13690a3a33f757a43972c5ef1fe967b7f
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2017
---
# <a name="prepare-a-geo-replicated-azure-container-registry"></a>geo レプリケーション Azure Container Registry の準備

Azure Container Registry は、デプロイの近くにネットワークを確保できる Azure にデプロイされたプライベート Docker レジストリです。 この一連の 3 つのチュートリアルの記事では、geo レプリケーションを使用して、Linux コンテナーで実行する ASP.NET Core Web アプリケーションを、2 つの [Web Apps for Containers](../app-service/containers/index.yml) インスタンスにデプロイする方法について説明します。 Azure が最も近い geo レプリケーション レポジトリからの各 Web アプリ インスタンスにイメージをデプロイする方法を確認します。

このチュートリアルの 3 部構成のシリーズのパート 1 では、次の作業を行います。

> [!div class="checklist"]
> * geo レプリケーション Azure Container Registry を作成する
> * GitHub からアプリケーション ソース コードを複製する
> * アプリケーション ソースから Docker コンテナー イメージを構築する
> * レジストリにコンテナー イメージをプッシュする

以降のチュートリアルでは、コンテナーをプライベート レジストリから 2 つの Azure リージョンで実行している Web アプリにデプロイします。 次に、アプリケーションのコードを更新し、レジストリへの単一の `docker push` で、両方の Web アプリ インスタンスを更新します。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルでは、Azure CLI バージョン 2.0.20 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。

このチュートリアルの前提として、コンテナー、コンテナー イメージ、基本 Docker コマンドなど、Docker のコア概念を基本的に理解している必要があります。 必要な場合は、[Docker の入門]( https://docs.docker.com/get-started/)に関するドキュメントでコンテナーの基礎を参照してください。

このチュートリアルを完了するには、Docker 開発環境が必要です。 Docker では、[Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、または [Linux](https://docs.docker.com/engine/installation/#supported-platforms) システムで Docker を簡単に構成できるパッケージが提供されています。

Azure Cloud Shell には、このチュートリアルの各ステップを完了するのに必要な Docker コンポーネントがすべて含まれているわけではありません。 そのため、Azure CLI および Docker 開発環境のローカル インストールをお勧めします。

> [!IMPORTANT]
> Azure Container Registry の geo レプリケーション機能は現在**プレビュー**段階です。 プレビュー版は、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。
>

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

[Azure ポータル](http://portal.azure.com)にサインインします。

**[新規作成]** > **[コンテナー]** > **[Azure Container Registry]** を選択します。

![Azure Portal でコンテナー レジストリを作成する][tut-portal-01]

次の設定で、新しいレジストリを構成します。

* **レジストリ名**: Azure 内でグローバルに一意で、5 ～ 50 文字の英数字を含むレジストリ名を作成します。
* **リソース グループ**: **[新規作成]** > `myResourceGroup`
* **場所**: `West US`
* **管理者ユーザー**: `Enable` (イメージをプルするために、Web App for Containers に必要)
* **SKU**: `Premium` (geo レプリケーションに必要)

**[作成]** を選択して ACR インスタンスをデプロイします。

![Azure Portal でコンテナー レジストリを作成する][tut-portal-02]

このチュートリアルの残りの部分では、選択したコンテナー **レジストリ名**のプレースホルダーとして `<acrName>` を使用します。

> [!TIP]
> Azure Container Registry は通常、複数のコンテナー ホスト間で使用される有効期間が長いリソースであるため、独自のリソース グループにレジストリを作成することをお勧めします。 geo レプリケーション レジストリと webhook を構成すると、これらの追加リソースが同じリソース グループに配置されます。
>

## <a name="configure-geo-replication"></a>geo レプリケーションの構成

Premium レジストリを使用できるようになったので、geo レプリケーションを構成できます。 そうすると、次のチュートリアルで 2 つのリージョンで実行するように構成する Web アプリが、最も近いレジストリからそのコンテナー イメージをプルできるようになります。

Azure Portal で新しいコンテナー レジストリに移動し、**[SERVICES]** (サービス) の下の **[レプリケーション]** を選択します。

![Azure Portal のコンテナー レジストリ UI の [レプリケーション]][tut-portal-03]

マップに、geo レプリケーションで使用できる Azure リージョンを表す緑色の六角形が表示されます。

 ![Azure Portal のリージョン マップ][tut-map-01]

その緑色の六角形を選択して、米国東部リージョンにレジストリをレプリケートし、**[レプリケーションの作成]** で **[作成]** を選択します。

 ![Azure Portal の [レプリケーションの作成] UI][tut-portal-04]

レプリケーションが完了すると、ポータルで、両方のリージョンに対して*準備完了*が示されます。 **[更新]** ボタンを使用して、レプリケーションの状態を更新します。レプリカが作成され、同期されるまで、1 分ほどかかる可能性があります。

![Azure Portal の [レプリケーションの状態] UI][tut-portal-05]

## <a name="container-registry-login"></a>Container Registry のログイン

geo レプリケーションを構成したので、コンテナー イメージを構築し、レジストリにプッシュします。 イメージをプッシュする前に、まず ACR インスタンスにログインする必要があります。 [Basic、Standard、および Premium SKU](container-registry-skus.md) では、Azure ID を使用して認証できます。

[az acr login](https://docs.microsoft.com/en-us/cli/azure/acr#az_acr_login) コマンドを使用して、レジストリの資格情報を認証し、キャッシュします。 `<acrName>` を、前の手順で作成したレジストリの名前に置き換えます。

```azurecli
az acr login --name <acrName>
```

このコマンドは、完了すると、`Login Succeeded` を返します。

## <a name="get-application-code"></a>アプリケーションのコードを入手する

このチュートリアルのサンプルには、[ASP.NET Core](http://dot.net) で構築した小さな Web アプリケーションが含まれます。 アプリケーションは、Azure Container Registry によってイメージがデプロイされたリージョンを表示する HTML ページを提供します。

![ブラウザーに表示されたチュートリアル アプリ][tut-app-01]

Git を使用して、サンプルをローカル ディレクトリにダウンロードし、`cd` でそのディレクトリに変更します。

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

## <a name="update-dockerfile"></a>Dockerfile の更新

サンプルに含まれる Dockerfile は、コンテナーの構築方法を示しています。 公式の [aspnetcore](https://store.docker.com/community/images/microsoft/aspnetcore) イメージから開始し、アプリケーション ファイルをコンテナーにコピーし、依存関係をインストールして、公式の [aspnetcore-build](https://store.docker.com/community/images/microsoft/aspnetcore-build)イメージを使用して出力をコンパイルし、最後に、最適化された aspnetcore イメージを構築します。

Dockerfile は複製されたソース内の `./AcrHelloworld/Dockerfile` にあります。

```dockerfile
FROM microsoft/aspnetcore:2.0 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM microsoft/aspnetcore-build:2.0 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

*acr-helloworld* イメージ内のアプリケーションは、DNS で、レジストリのログイン サーバーに関する情報をクエリすることによって、コンテナーのデプロイ元のリージョンを判断しようとします。 Dockerfile 内の `DOCKER_REGISTRY` 環境変数に、レジストリのログイン サーバー URL を指定する必要があります。

まず、`az acr show` コマンドでレジストリのログイン サーバー URL を取得します。 `<acrName>` を、前の手順で作成したレジストリの名前に置き換えます。

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

出力:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

次に、`DOCKER_REGISTRY` 行をレジストリのログイン サーバー URL で更新します。 この例では、サンプル レジストリ名 *uniqueregistryname* を反映するように、行を更新します。

```dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>コンテナー イメージの構築

レジストリの URL で Dockerfile を更新したので、`docker build` を使用して、コンテナー イメージを作成できます。 次のコマンドを実行してイメージを作成し、プライベート レジストリの URL でそれにタグ付けして、再度 `<acrName>` をレジストリの名前に置き換えます。

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Docker イメージが構築されると、複数行の出力が表示されます (ここでは切り詰めて表示しています)。

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer
...
Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

`docker images` コマンドを使用して、構築されたイメージを参照します。

```bash
docker images
```

出力:

```bash
REPOSITORY                                     TAG                 IMAGE ID            CREATED              SIZE
uniqueregistryname.azurecr.io/acr-helloworld   v1                  c9ca1763cfb1        About a minute ago   285MB
...
```

## <a name="push-image-to-azure-container-registry"></a>Azure Container Registry へのイメージのプッシュ

最後に、`docker push` コマンドを使用して、*acr-helloworld* イメージをレジストリにプッシュします。 `<acrName>` をレジストリの名前に置き換えます。

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

geo レプリケーション用にレジストリを構成したため、この単一の `docker push` コマンドで、イメージが*米国西部*と*米国東部*リージョンの両方に自動的にレプリケートされます。

出力:

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
9716cfe18412: Pushed
074867a942d5: Pushed
a77666945b96: Pushed
953ff32f2036: Pushed
aa2e77726d3c: Pushed
98b800c91d50: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:c515bcebf249b591b558318e2d0ec21d1320340dbf335730eb32372ff7d34255 size: 1792
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、プライベートの geo レプリケーション コンテナー レジストリを作成し、コンテナー イメージを構築して、そのイメージをレジストリにプッシュしました。 このチュートリアルの手順に従って、次の作業を行いました。

> [!div class="checklist"]
> * geo レプリケーション Azure Container Registry を作成しました
> * GitHub からアプリケーション ソース コードを複製しました
> * アプリケーション ソースから Docker コンテナー イメージを構築しました
> * コンテナー イメージをレジストリにプッシュしました

次のチュートリアルに進み、geo レプリケーションを使用して、イメージをローカルで使用するために、コンテナーを複数の Azure App Service インスタンスにデプロイする方法を学習します。

> [!div class="nextstepaction"]
> [Azure App Service にコンテナーをデプロイする](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png
