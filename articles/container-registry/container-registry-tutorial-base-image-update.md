---
title: チュートリアル - Azure Container Registry Build を使用して基本イメージの更新時のコンテナー イメージ ビルドを自動化する
description: このチュートリアルでは、基本イメージが更新されたときにクラウドでコンテナー イメージ ビルドを自動的にトリガーするようにビルド タスクを構成する方法を説明します。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: a302cdcf94baa869e55262c4cd380fc05bf64299
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38461607"
---
# <a name="tutorial-automate-image-builds-on-base-image-update-with-azure-container-registry-build"></a>チュートリアル: Azure Container Registry Build を使用して基本イメージの更新時のイメージ ビルドを自動化する

ACR Build では、いずれかの基本イメージ内で OS またはアプリケーション フレームワークに修正プログラムを適用したときなど、コンテナーの基本イメージが更新されたときに、自動化されたビルドの実行をサポートしています。 このチュートリアルでは、コンテナーの基本イメージがレジストリにプッシュされている場合に、クラウドでビルドをトリガーする ACR Build でビルド タスクを作成する方法を説明します。

シリーズの最後の部分であるこのチュートリアルでは、以下のことを行います。

> [!div class="checklist"]
> * 基本イメージをビルドする
> * アプリケーション イメージ ビルド タスクを作成する
> * アプリケーション イメージ ビルドをトリガーするよう基本イメージを更新する
> * トリガーされたビルドを表示する
> * 更新されたアプリケーション イメージを確認する

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

ローカルで Azure CLI を使用する場合は、Azure CLI のバージョン **2.0.32** 以降がインストールされている必要があります。 バージョンを確認するには、`az --version` を実行します。 CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

### <a name="complete-previous-tutorials"></a>前のチュートリアルを完了しておく

このチュートリアルでは、シリーズの最初の 2 つのチュートリアルの以下の手順を完了していることを前提としています。

* Azure Container Registry の作成
* サンプル リポジトリのフォーク
* サンプル リポジトリの複製
* GitHub 個人用アクセス トークンの作成

まだ完了していない場合は、続行する前に最初の 2 つのチュートリアルを完了してください。

[Azure Container Registry Build を使用してクラウド内のコンテナー イメージをビルドする](container-registry-tutorial-quick-build.md)

[Azure Container Registry Build を使用してコンテナー イメージ ビルドを自動化する](container-registry-tutorial-build-task.md)

### <a name="configure-environment"></a>環境の構成

次のシェル環境変数に、環境に適した値を設定します。 これは必須ではありませんが、このチュートリアルの複数行の Azure CLI コマンドの実行が少し簡単になります。 これらの値を設定しなかった場合、サンプル コマンドで任意に出現する各値を手動で置き換える必要があります。

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>基本イメージ

ほとんどのコンテナー イメージを定義する Docker ファイルは、基になる親イメージを指定します。これは多くの場合、"*基本イメージ*" と呼ばれます。 通常、基本イメージにはオペレーティング システム ([Alpine Linux][base-alpine] または [Windows Nano Server][base-windows] など) が含まれます。ここでは、残りのコンテナーのレイヤーが適用されます。 [Node.js][base-node] や [.NET Core][base-dotnet] などのアプリケーション フレームワークが含まれる場合もあります。

### <a name="base-image-updates"></a>基本イメージの更新

基本イメージは多くの場合、OS またはイメージのフレームワークに新機能や拡張機能を含むように、イメージのメンテナンス ツールによって更新されます。 基本イメージの更新の理由として、セキュリティ パッチも挙げられます。

基本イメージが更新されるとき、レジストリに基づいてレジストリ内にコンテナー イメージをリビルドして、新機能やパッチを含めるかどうか聞かれます。 ACR Build には、コンテナーの基本イメージが更新されたときにイメージを自動的にビルドする機能が備わっています。

### <a name="base-image-update-scenario"></a>基本イメージ更新シナリオ

このチュートリアルでは、基本イメージの更新シナリオについて説明します。 [コード サンプル][code-sample]には 2 つの Docker ファイルが含まれます。これは、アプリケーション イメージと、アプリケーションイメージがベースとして指定するイメージです。 以下のセクションでは、基本イメージの新しいバージョンがコンテナー レジストリにプッシュされたときに、アプリケーション イメージ ベースのビルドを自動的にトリガーする ACR Build タスクを作成します。

[Dockerfile-app][dockerfile-app]: 基になる Node.js バージョンを表示する静的な Web ページをレンダリングする小さな Node.js Web アプリケーションです。 バージョン文字列がシミュレートされ、ここでは、基本イメージで定義される、環境変数 `NODE_VERSION` の内容が表示されます。

[Dockerfile-base][dockerfile-base]: `Dockerfile-app` によってそのベースとして指定されるイメージ。 これ自体が [Node][base-node] イメージに基づき、`NODE_VERSION` 環境変数を含みます。

次のセクションでは、ビルド タスクを作成し、基本イメージ Docker ファイルで値 `NODE_VERSION` を更新してから、ACR Build を使用して基本イメージをビルドします。 ACR Build によって新しい基本イメージがレジストリにプッシュされたとき、アプリケーション イメージのビルドが自動的にトリガーされます。 必要に応じて、アプリケーション コンテナー イメージをローカルで実行して、ビルドされたイメージの別のバージョンの文字列を表示します。

## <a name="build-base-image"></a>基本イメージのビルド

ACR Build の*クイック ビルド*を使用して基本イメージをビルドして開始します。 シリーズの[最初のチュートリアル](container-registry-tutorial-quick-build.md)で説明したように、これによってイメージがビルドされるだけでなく、ビルドが成功した場合にコンテナー レジストリにイメージがプッシュされます。

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-build-task"></a>ビルド タスクの作成

次に、[az acr build-task create][az-acr-build-task-create] を使用してビルド タスクを作成します。

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --build-arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

このビルド タスクは、[前のチュートリアル](container-registry-tutorial-build-task.md)で作成されたタスクに似ています。 これは、コミットが `--context` によって指定されたリポジトリにプッシュされたときに ACR Build にイメージ ビルドをトリガーするように指示します。

相違点はその動作です。このタスクでは、その*基本イメージ*が更新されたときにイメージのビルドもトリガーされます。 `--file` 引数によって指定された Docker ファイル、[Dockerfile-app][dockerfile-app] では、そのベースと同じレジストリ内からのイメージの指定がサポートされています。

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

ビルド タスクを実行したとき、ACR Build によってイメージの依存関係が検出されます。 `FROM` ステートメントで指定された基本イメージが同じレジストリ内にある場合、ベースが更新されたときにこのイメージがいつでもリビルドされるようにフックが追加されます。

> [!NOTE]
> プレビュー中、ACR Build では派生したイメージ ビルドのトリガーがサポートされますが、これは、基本イメージとそれを参照するイメージの両方が同じ Azure コンテナー レジストリに存在する場合にのみサポートされます。

## <a name="build-application-container"></a>アプリケーション コンテナーのビルド

ACR Build でコンテナー イメージの依存関係 (基本イメージがどこに含まれるか) を特定し追跡できるようにするために、最初に**少なくとも 1 回**、そのビルド タスクをトリガーする**必要があります**。

[az acr build-task run][az-acr-build-task-run] を使用して、ビルド タスクを手動でトリガーし、アプリケーション イメージをビルドします。

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

次のオプションの手順を実行する場合は、ビルドの完了後に**ビルド ID** ("aa6" など) をメモしておきます。

### <a name="optional-run-application-container-locally"></a>オプション: アプリケーション コンテナーをローカルで実行する

ローカルで作業し (Cloud Shell ではなく)、Docker がインストールされている場合は、コンテナーを実行して、その基本イメージをリビルドする前に Web ブラウザーでアプリケーションのレンダリングを確認します。 Cloud Shell を使用する場合は、このセクションをスキップしてください (Cloud Shell では `az acr login` または `docker run` はサポートされていません)。

最初に、[az acr ログイン][az-acr-login]を使用してコンテナー レジストリにログインします。

```azurecli
az acr login --name $ACR_NAME
```

次に、`docker run` を使ってコンテナーをローカルで実行します。 **\<build-id\>** を前の手順の出力にあったビルド ID ("aa6" など) に置き換えます。

```azurecli
docker run -d -p 8080:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

ブラウザーで http://localhost:8080 に移動します。次のような Node.js バージョン番号が Web ページに表示されます。 後の手順で、バージョン文字列に "a" を追加して、バージョンを増やします。

![ブラウザーに表示されたサンプル アプリケーションのスクリーンショット][base-update-01]

## <a name="list-builds"></a>ビルドの一覧表示

次に、ACR Build がレジストリについて完了したビルドを一覧表示します。

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

前のチュートリアルを完了している場合 (レジストリを削除していない場合)、次のような出力が表示されます。 ビルドの番号と最新の BUILD ID を書き留めます。これによって、次のセクションで基本イメージを更新した後に出力を比較できます。

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
aa6         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T20:00:12Z  00:00:50
aa5                          Linux       Succeeded  Manual      2018-05-10T19:57:35Z  00:00:55
aa4         buildhelloworld  Linux       Succeeded  Git Commit  2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual      2018-05-10T19:10:14Z  00:00:55
```

## <a name="update-base-image"></a>基本イメージの更新

ここで、基本イメージでフレームワークのパッチをシミュレートします。 **Dockerfile-base** を編集して、`NODE_VERSION` で定義されたバージョン番号の後に "a" を追加します。

```Dockerfile
ENV NODE_VERSION 9.11.1a
```

ACR Build でクイックビルドを実行して、変更された基本イメージをビルドします。 出力の **Build ID** を書き留めます。

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

ビルドが完了し、ACR Build によって新しい基本イメージがレジストリにプッシュされたら、アプリケーション イメージのビルドがトリガーされます。 前に作成した ACR Build がアプリケーション イメージ ビルドをトリガーするのに少し時間がかかる場合があります。これは、新しく完了され、プッシュされた基本イメージを検出する必要があるためです。

## <a name="list-builds"></a>ビルドの一覧表示

これで、基本イメージを更新したので、もう一度ビルドを一覧表示して以前の一覧と比べます。 最初、出力が違わない場合は、定期的にコマンドを実行して、一覧に表示される新しいビルドを確認します。

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

出力は次のようになります。 最後に実行されたビルドのトリガーは "イメージの更新" で、これは、ビルド タスクが基本イメージのクイック ビルドによって開始されたことを示します。

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER       STARTED               DURATION
----------  ---------------  ----------  ---------  ------------  --------------------  ----------
aa8         buildhelloworld  Linux       Succeeded  Image Update  2018-05-10T20:09:52Z  00:00:45
aa7                          Linux       Succeeded  Manual        2018-05-10T20:09:17Z  00:00:40
aa6         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T20:00:12Z  00:00:50
aa5                          Linux       Succeeded  Manual        2018-05-10T19:57:35Z  00:00:55
aa4         buildhelloworld  Linux       Succeeded  Git Commit    2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual        2018-05-10T19:10:14Z  00:00:55
```

新しくビルドされたコンテナーを実行する次のオプションの手順を実行し、更新後のバージョン番号を表示するには、イメージの更新によってトリガーされたビルドの **[BUILD ID]** の値をメモしておきます (前の出力では "aa8")。

### <a name="optional-run-newly-built-image"></a>オプション: 新しくビルドされたイメージを実行する

ローカルで作業しており (Cloud Shell ではなく)、Docker がインストールされている場合は、そのビルドが完了したら新しいアプリケーション イメージを実行します。 `<build-id>` には、前の手順で取得した BUILD ID を指定します。 Cloud Shell を使用する場合は、このセクションをスキップしてください (Cloud Shell では `docker run` はサポートされていません)。

```bash
docker run -d -p 8081:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

ブラウザーで http://localhost:8081 に移動します。Web ページに更新された Node.js バージョン番号 ("a" が追加された) が表示されます。

![ブラウザーに表示されたサンプル アプリケーションのスクリーンショット][base-update-02]

**基本**イメージを新しいバージョン番号に更新しましたが、最後にビルドされた**アプリケーション** イメージによって新しいバージョンが表示されることに注意してください。 ACR Build によって変更が基本イメージに反映され、アプリケーション イメージが自動的にリビルドされました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアル シリーズで作成したすべてのリソース (コンテナー レジストリ、コンテナー インスタンス、キー コンテナー、サービス プリンシパルなど) を削除するには、次のコマンドを実行します。

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ビルド タスクを使用して、イメージの基本イメージが更新されたときにコンテナー イメージ ビルドを自動的にトリガーする方法を説明しました。 次に、コンテナー レジストリの認証について説明します。

> [!div class="nextstepaction"]
> [Azure Container Registry の 認証](container-registry-authentication.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png