---
title: カスタム イメージを使用して Linux で関数を作成する (プレビュー) | Microsoft Docs
description: カスタム Linux イメージで実行する Azure Functions を作成する方法について説明します。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: fab67b503d060c8c01b5a3692c8a07b24c425c78
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437408"
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>カスタム イメージを使用して Linux で関数を作成する (プレビュー)

Azure Functions を使用して、独自のカスタム コンテナー内の Linux 上で関数をホストできます。 [既定の Azure App Service コンテナー上でホストする](functions-create-first-azure-function-azure-cli-linux.md)こともできます。 この機能は現在プレビュー段階であり、同じくプレビュー段階の [Functions 2.0 ランタイム](functions-versions.md)が必要です。

このチュートリアルでは、カスタム Docker イメージとして Function App をデプロイする方法について説明します。 このパターンは、組み込みの App Service コンテナー イメージをカスタマイズする必要がある場合に便利です。 特定の言語バージョン、特定の依存関係、または組み込みイメージで提供されない構成が関数に必要になるときに、カスタム イメージを使用することがあります。

このチュートリアルでは、Azure Functions を使用してカスタム イメージを作成し、Docker Hub にプッシュする方法について説明します。 その後、Linux 上で実行される Function App のデプロイ ソースとして、このイメージを使用できます。 Docker を使用してイメージをビルドし、プッシュします。 Azure CLI を使用して Function App を作成し、Docker Hub からイメージをデプロイします。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Docker を使用してカスタム イメージをビルドします。
> * カスタム イメージをコンテナー レジストリに発行します。 
> * Azure ストレージ アカウントを作成します。 
> * Linux App Service プランを作成します。 
> * Docker Hub から Function App をデプロイします。
> * Function App にアプリケーション設定を追加します。 

次の手順は、Mac、Windows、または Linux コンピューターでサポートされます。  

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* [Git](https://git-scm.com/downloads)
* 有効な [Azure サブスクリプション](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Docker](https://docs.docker.com/install/)
* [Docker Hub アカウント](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>サンプルのダウンロード

ターミナル ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製し、サンプル コードを含むディレクトリに移動します。

```bash
git clone https://github.com/Azure-Samples/functions-linux-custom-image.git --config core.autocrlf=input
cd functions-linux-custom-image
```

## <a name="build-the-image-from-the-docker-file"></a>Docker ファイルからイメージを作成する

この Git リポジトリで、_Dockerfile_ を確認してください。 このファイルには、Linux 上で Function App を実行するために必要な環境が記述されています。 

```docker
# Base the image on the built-in Azure Functions Linux image.
FROM microsoft/azure-functions-runtime:2.0.0-jessie
ENV AzureWebJobsScriptRoot=/home/site/wwwroot

# Add files from this repo to the root site folder.
COPY . /home/site/wwwroot 
```
>[!NOTE]
> プライベート コンテナー レジストリでイメージをホストする場合は、Dockerfile の **ENV** 変数を使用することで Function App に接続の設定を追加する必要があります。 このチュートリアルではプライベート レジストリが使用されると保証できないため、セキュリティのベスト プラクティスとして、接続の設定は[デプロイの後で Azure CLI を使用して追加されます](#configure-the-function-app)。   

### <a name="run-the-build-command"></a>ビルド コマンドの実行
Docker イメージを作成するには、`docker build` コマンドを実行し、`mydockerimage` という名前を付け、タグ `v1.0.0` を付けます。 `<docker-id>` を Docker Hub アカウント ID で置換します。

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

コマンドによって次のような出力が生成されます。

```bash
Sending build context to Docker daemon  169.5kB
Step 1/3 : FROM microsoft/azure-functions-runtime:v2.0.0-jessie
v2.0.0-jessie: Pulling from microsoft/azure-functions-runtime
b178b12f7913: Pull complete
2d9ce077a781: Pull complete
4775d4ba55c8: Pull complete
Digest: sha256:073f45fc167b3b5c6642ef4b3c99064430d6b17507095...
Status: Downloaded newer image for microsoft/azure-functions-runtime:v2.0.0-jessie
 ---> 217799efa500
Step 2/3 : ENV AzureWebJobsScriptRoot /home/site/wwwroot
 ---> Running in 528fa2077d17
 ---> 7cc6323b8ae0
Removing intermediate container 528fa2077d17
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5bdac9878423
Successfully built 5bdac9878423
Successfully tagged ggailey777/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>ローカルでのイメージのテスト
ローカル コンテナーで Docker イメージを実行することで、ビルドされたイメージの動作を確認します。 [docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを発行し、イメージの名前とタグを渡します。 必ず `-p` 引数を使用してポートを指定してください。

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

ローカルの Docker コンテナーでカスタム イメージを実行させながら、<http://localhost:8080> に移動して Function App とコンテナーが正しく機能していることを確認します。

![Function App をローカルでテストします。](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

コンテナー内に Function App を確認した後で、実行を停止します。 これで、カスタム イメージを Docker Hub アカウントにプッシュできます。

## <a name="push-the-custom-image-to-docker-hub"></a>Docker Hub へのカスタム イメージのプッシュ

レジストリは、イメージをホストし、サービス イメージとコンテナー サービスを提供するアプリケーションです。 イメージを共有するには、レジストリにプッシュする必要があります。 Docker Hub は Docker イメージのレジストリであり、パブリックまたはプライベートの独自のリポジトリをホストすることができます。 

イメージをプッシュする前に、[docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドを使用して Docker Hub にサインインする必要があります。 `<docker-id>` をアカウント名に置き換え、プロンプトで、コンソールにパスワードを入力します。 その他の Docker Hub パスワード オプションについては、[docker login コマンドのドキュメント](https://docs.docker.com/engine/reference/commandline/login/)を参照してください。

```bash
docker login --username <docker-id> 
```

「ログインに成功しました」のメッセージで、ログインしていることを確認します。 サインインしたら、[docker push](https://docs.docker.com/engine/reference/commandline/push/) コマンドを使用して Docker Hub にイメージをプッシュします。

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

コマンドの出力を調べて、プッシュが成功したことを確認します。

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from microsoft/azure-functions-runtime
e7796c35add2: Mounted from microsoft/azure-functions-runtime
ae9a05b85848: Mounted from microsoft/azure-functions-runtime
45c86e20670d: Mounted from microsoft/azure-functions-runtime
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 2422
```
これで、Azure の新しい Function App のデプロイ ソースとして、このイメージを使用できます。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0.21 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Linux App Service プランの作成

現在、従量課金プランでは Linux での Functions のホスティングはサポートされていません。 Linux App Service プランで実行する必要があります。 ホスティングの詳細については、「[Azure Functions のホスティング プランの比較](functions-scale.md)」を参照してください。 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]


## <a name="create-and-deploy-the-custom-image"></a>カスタム イメージの作成とデプロイ

Function App は関数の実行をホストします。 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) コマンドを使用して Docker Hub イメージから Function App を作成します。 

次のコマンドでは、`<app_name>` プレースホルダーを一意の Function App 名で、`<storage_name>` をストレージ アカウント名で置き換えます。 `<app_name>` は、Function App の既定の DNS ドメインとして使用されます。そのため、名前は Azure のすべてのアプリ間で一意である必要があります。 以前と同様に、`<docker-id>` は Docker アカウント名です。

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0 
```
Function App が作成されると、Azure CLI によって次の例のような情報が表示されます。

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

_deployment-container-image-name_ パラメーターは、Function App を作成するために使用する、Docker Hub でホストされているイメージを示します。 


## <a name="configure-the-function-app"></a>Function App を構成する

関数が既定のストレージ アカウントに接続するには接続文字列が必要です。 カスタム イメージをプライベート コンテナー アカウントに発行するときに、代わりにこれらのアプリケーション設定を Dockerfile の環境変数として設定する必要があります。[ENV](https://docs.docker.com/engine/reference/builder/#env) に関するページなどを参照してください。 

ここでは、`<storage_account>` は作成したストレージ アカウントの名前です。 [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string) コマンドで接続文字列を取得します。 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) コマンドで、これらのアプリケーション設定を Function App に追加します。

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

これで、Linux 上で実行される関数を Azure でテストできます。

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Docker を使用してカスタム イメージをビルドします。
> * カスタム イメージをコンテナー レジストリに発行します。 
> * Azure ストレージ アカウントを作成します。 
> * Linux App Service プランを作成します。 
> * Docker Hub から Function App をデプロイします。
> * Function App にアプリケーション設定を追加します。

App Service のコア プラットフォームに組み込まれる継続的インテグレーション機能を有効にする方法について説明します。 関数アプリを構成して、Docker Hub 内のイメージを更新するときに、コンテナーが再デプロイされるように設定できます。

> [!div class="nextstepaction"] 
> [Web App for Containers での継続的デプロイ](../app-service/containers/app-service-linux-ci-cd.md)
