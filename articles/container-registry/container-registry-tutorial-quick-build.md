---
title: チュートリアル - Azure Container Registry Build を使用してクラウド内のコンテナー イメージをビルドする
description: このチュートリアルでは、Azure Container Registry Build (ACR Build) 使用して Azure で Docker コンテナー イメージをビルドして、Azure Container Instances にデプロイする方法を説明します。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: d86c47c890fd15515c590e06b395ca82f9747ffe
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38461471"
---
# <a name="tutorial-build-container-images-in-the-cloud-with-azure-container-registry-build"></a>チュートリアル: Azure Container Registry Build を使用してクラウド内のコンテナー イメージをビルドする

**ACR Build** は、Azure Container Registry 内の機能スイートで、Azure での合理的かつ効率的な Docker コンテナー イメージ ビルドを実現します。 この記事では、ACR Build の*クイック ビルド*機能を使用する方法を説明します。 クイック ビルドは開発の "内部ループ" をクラウドに拡張し、ビルド成功の検証と、正常にビルドされたイメージのコンテナー レジストリへの自動プッシュを提供します。 イメージは、お使いのレジストリの近くのクラウドにネイティブにビルドされるため、デプロイが高速化されます。

Dockerfile に関する専門知識をすべて、ACR Build に直接転送できます。 ACR Build を使用してクラウドにビルドするために Dockerfile を変更する必要はありません。必要なのはコマンドの実行だけです。

このチュートリアルでは、シリーズの第 1 部で次のことを行います。

> [!div class="checklist"]
> * サンプル アプリケーションのソース コードを取得する
> * Azure でコンテナー イメージをビルドする
> * コンテナーを Azure Container Instances にデプロイする

以降のチュートリアルでは、コードのコミット時と基本イメージ更新時に自動化されたコンテナー イメージ ビルド用に ACR Build のビルド タスクを使用する方法を説明します。

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

ローカルで Azure CLI を使用する場合は、Azure CLI のバージョン **2.0.32** 以降がインストールされている必要があります。 バージョンを確認するには、`az --version` を実行します。 CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

### <a name="github-account"></a>GitHub アカウント

まだお持ちでない場合は、https://github.com でアカウントを作成します。 このチュートリアル シリーズでは、GitHub リポジトリを使用して ACR Build での自動化されたイメージ ビルドを示します。

### <a name="fork-sample-repository"></a>サンプル リポジトリのフォーク

次に、GitHub UI を使用して、サンプル リポジトリを GitHub アカウントにフォークします。 このチュートリアルでは、ソースからのコンテナー イメージをリポジトリにビルドし、次のチュートリアルでは、リポジトリのフォークにコミットをプッシュして自動化されたビルドを開始します。

以下でリポジトリをフォーク: https://github.com/Azure-Samples/acr-build-helloworld-node

![GitHub の [フォーク] ボタン (マーク済み) のスクリーンショット][quick-build-01-fork]

### <a name="clone-your-fork"></a>フォークの複製

リポジトリをフォークしたら、フォークを複製して、ローカル クローンを含むディレクトリを入力します。

`git` を使用してリポジトリを複製し、**\<your-github-username\>** を GitHub のユーザー名に置き換えます。

```azurecli-interactive
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

ソース コードを含むディレクトリを入力します。

```azurecli-interactive
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Bash シェル

このチュートリアルの一連のコマンドは、Bash シェル用にフォーマットされています。 PowerShell、コマンド プロンプト、または他のシェルを使用したい場合は、必要に応じて行継続と環境変数の調整が必要になる場合があります。

## <a name="build-in-azure-with-acr-build"></a>ACR Build を使用した Azure でのビルド

これで、コンピューターにソース コードを取得したので、次の手順に従ってコンテナー レジストリを作成して、ACR Build を使用してコンテナー イメージをビルドします。

サンプル コマンドの実行を簡単にするために、このシリーズのチュートリアルではシェル環境変数を使用します。 次のコマンドを実行して、`ACR_NAME` 変数を設定します。 **\<registry-name\>** を新しいコンテナー レジストリの一意の名前に置き換えます。 レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 このチュートリアルで作成する他のリソースはこの名前に基づくため、この最初の変数のみ変更する必要があります。

```azurecli-interactive
ACR_NAME=<registry-name>
```

コンテナー レジストリ環境変数は入力済みなので、チュートリアルの残りのコマンドをコピーして貼り付けられるようになりました。値を編集する必要はありません。 次のコマンドを実行して、リソース グループとコンテナー レジストリを作成します。

```azurecli-interactive
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

レジストリができたので、ACR Build を使用して、サンプル コードからコンテナー イメージをビルドします。 [az acr build][az-acr-build] コマンドを実行して、*クイック ビルド*を実行します。

```azurecli-interactive
az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
```

[az acr build][az-acr-build] コマンドの出力は次の例のようになります。 ソース コード ("コンテキスト") の Azure へのアップロードと、クラウドで ACR Build によって実行された `docker build` 操作の詳細を表示できます。 ACR Build は `docker build` を使用してイメージをビルドするため、Dockerfile を変更することなく ACR Build の使用をすぐに開始できます。

```console
$ az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
Sending build context (4.909 KiB) to ACR.
Queued a build with build ID: aa1
Waiting for a build agent...
Sending build context to Docker daemon  22.53kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
Status: Image is up to date for node:9-alpine
 ---> 7af437a39ec2
Step 2/5 : COPY . /src
 ---> 0c4814714938
Step 3/5 : RUN cd /src && npm install
 ---> Running in 4f77ce7b330d
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.096s
Removing intermediate container 4f77ce7b330d
 ---> e0eb24339e07
Step 4/5 : EXPOSE 80
 ---> Running in 872bd29edbc7
Removing intermediate container 872bd29edbc7
 ---> 22ba8d8ffb4e
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 1a40c05c4122
Removing intermediate container 1a40c05c4122
 ---> 0a9a4b74fb53
Successfully built 0a9a4b74fb53
Successfully tagged mycontainerregistry.azurecr.io/helloacrbuild:v1
time="2018-05-10T19:10:20Z" level=info msg="Running command docker push mycontainerregistry.azurecr.io/helloacrbuild:v1"
The push refers to repository [mycontainerregistry.azurecr.io/helloacrbuild]
d2b301f7ef94: Preparing
d0e0f2bb8747: Preparing
26b0c207c4a9: Preparing
917e7cdebc8b: Preparing
9dfa40a0da3b: Preparing
26b0c207c4a9: Pushed
d2b301f7ef94: Pushed
d0e0f2bb8747: Pushed
9dfa40a0da3b: Pushed
917e7cdebc8b: Pushed
v1: digest: sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd size: 1366
time="2018-05-10T19:11:07Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistry.azurecr.io/helloacrbuild:v1"
"["mycontainerregistry.azurecr.io/helloacrbuild@sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd"]"
time="2018-05-10T19:11:07Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistry.azurecr.io
    repository: helloacrbuild
    tag: v1
    digest: sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3

Build complete
Build ID: aa1 was successful after 52.522222729s
```

出力の最後のほうに、ACR Build によって、イメージの検出された依存関係が表示されます。 これにより、ACR Build は基本イメージの更新時 (OS またはフレームワークの修正プログラムによる基本イメージの更新時) にイメージ ビルドを自動化できます。 基本イメージの更新プログラムの ACR Build のサポートについては、このチュートリアル シリーズで後ほど説明します。

## <a name="deploy-to-azure-container-instances"></a>Azure Container Instances へのデプロイ

ACR Build によって、既定でビルド イメージがレジストリに自動的に正常にプッシュされるため、レジストリからすぐにイメージをデプロイできます。

このセクションでは、Azure Key Vault とサービス プリンシパルを作成し、サービス プリンシパルの資格情報を使用して Azure Container Instances (ACI) にコンテナーをデプロイします。

### <a name="configure-registry-authentication"></a>レジストリの認証を構成する

すべての運用環境では、[サービス プリンシパル][service-principal-auth]を使用して、Azure コンテナー レジストリにアクセスします。 サービス プリンシパルを使用すると、コンテナー イメージへのロールベースのアクセス制御を提供できます。 たとえば、レジストリへのプルのみのアクセス権を持つサービス プリンシパルを設定できます。

#### <a name="create-key-vault"></a>キー コンテナーの作成

[Azure Key Vault](/azure/key-vault/) に資格情報コンテナーがない場合、次のコマンドを使用して Azure CLI で 1 つ作成します。

```azurecli-interactive
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-service-principal-and-store-credentials"></a>サービス プリンシパルを作成し、資格情報を格納する

これで、サービス プリンシパルを作成し、その資格情報をキー コンテナーに格納する必要があります。

[az ad sp create-for-rbac][az-ad-sp-create-for-rbac] コマンドを使用してサービス プリンシパルを作成し、[az keyvault secret set][az-keyvault-secret-set] を使用して資格情報コンテナーにサービス プリンシパルの**パスワード**を格納します。

```azurecli-interactive
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

上記のコマンドの `--role` 引数により、*reader* ロールを持つサービス プリンシパルが構成されます。これにより、レジストリへのプルのみのアクセス権が付与されます。 プッシュ アクセス権とプル アクセス権の両方を付与するには、`--role` 引数を *contributor* に変更します。

次にサービス プリンシパルの *appId* を資格情報コンテナーに格納します。appId は、認証のために Azure Container Registry に渡す**ユーザー名**です。

```azurecli-interactive
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Azure Key Vault を作成してに 2 つのシークレットを格納します。

* `$ACR_NAME-pull-usr`: サービス プリンシパルID。コンテナー レジストリの**ユーザー名**として使用します。
* `$ACR_NAME-pull-pwd`: サービス プリンシパルのパスワード。コンテナー レジストリの**パスワード**として使用します。

これらのシークレットは、アプリケーションおよびサービスがレジストリからイメージをプルしたときの名前で参照できます。

### <a name="deploy-container-with-azure-cli"></a>Azure CLI でコンテナーをデプロイする

これでサービス プリンシパルの資格情報が Azure Key Vault シークレットに格納されたので、格納した資格情報をアプリケーションとサービスで使用してプライベート レジストリにアクセスできます。

次の [az container create][az-container-create] コマンドを実行して、コンテナー インスタンスをデプロイします。 このコマンドは、Azure Key Vault に格納されたサービス プリンシパルの資格情報を使用して、コンテナー レジストリに対する認証を行います。

```azurecli-interactive
az container create \
    --resource-group $RES_GROUP \
    --name acr-build \
    --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-build-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

前のコマンドが、コンテナーの DNS 名のラベルにコンテナーのレジストリの名前を追加するため、`--dns-name-label` の値は Azure 内で一意でなければなりません。 コマンドの出力には、次のようにコンテナーの完全修飾ドメイン名 (FQDN) が表示されます。

```console
$ az container create \
>     --resource-group $RES_GROUP \
>     --name acr-build \
>     --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
>     --registry-login-server $ACR_NAME.azurecr.io \
>     --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
>     --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
>     --dns-name-label acr-build-$ACR_NAME \
>     --query "{FQDN:ipAddress.fqdn}" \
>     --output table
FQDN
-------------------------------------------
acr-build-1175.eastus.azurecontainer.io
```

コンテナーの FQDN を書き留めます。これは次のセクションで使用します。

### <a name="verify-deployment"></a>デプロイの確認

コンテナーの起動プロセスを監視するには、[az container attach][az-container-attach] コマンドを使用します。

```azurecli-interactive
az container attach --resource-group $RES_GROUP --name acr-build
```

`az container attach` 出力では、イメージを取得して開始するときに最初にコンテナーの状態が表示され、次にローカル コンソールの STDOUT と STDERR がコンテナーにバインドされます。

```console
$ az container attach --resource-group $RES_GROUP --name acr-build
Container 'acr-build' is in state 'Waiting'...
Container 'acr-build' is in state 'Running'...
(count: 1) (last timestamp: 2018-04-03 19:45:37+00:00) pulling image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Successfully pulled image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Created container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Started container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413

Start streaming logs:
Server running at http://localhost:80
```

`Server running at http://localhost:80` が表示されたら、ブラウザーでコンテナーの FQDN に移動して、実行中のアプリケーションを表示します。

![ブラウザーに表示されたサンプル アプリケーションのスクリーンショット][quick-build-02-browser]

コンテナーからコンソールをデタッチするには、`Control+C` を押します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[az container delete][az-container-delete] コマンドを使用してコンテナー インスタンスを停止します。

```azurecli-interactive
az container delete --resource-group $RES_GROUP --name acr-build
```

このチュートリアルで作成した "*すべての*" リソース (コンテナー レジストリ、キー コンテナー、サービス プリンシパルなど) を削除するには、次のコマンドを実行します。 ただし、これらのリソースはシリーズの[次のチュートリアル](container-registry-tutorial-build-task.md)で使用するため、次のチュートリアルに進んでいる場合は削除しないことをお勧めします。

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>次の手順

これで、クイックビルドで内部ループをテストしたので、ソース コードを Git リポジトリにコミットしたときにコンテナー イメージ ビルドをトリガーするように**ビルド タスク**を構成します。

> [!div class="nextstepaction"]
> [ビルド タスクによる自動ビルドのトリガー](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
