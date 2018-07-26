---
title: プライベート レジストリからのアプリを Azure Service Fabric mesh にデプロイする | Microsoft Docs
description: Azure CLI を使用して、プライベート コンテナー レジストリを使用するアプリを Service Fabric mesh にデプロイする方法について説明します。
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/16/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: af92d3c6ea881d00ec687a5560bf4db35aa431c5
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089488"
---
# <a name="deploy-a-service-fabric-mesh-app-from-a-private-container-image-registry"></a>プライベート コンテナー イメージ レジストリからの Service Fabric mesh アプリをデプロイする

この記事では、プライベート コンテナー イメージ レジストリを使用する Azure Service Fabric mesh アプリをデプロイする方法を説明します。

## <a name="prerequisites"></a>前提条件

### <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric mesh CLI の設定 
Azure Cloud Shell または Azure CLI のローカル インストールを使用して、このタスクを実行できます。 こちらの[手順](service-fabric-mesh-howto-setup-cli.md)に従って、Azure Service Fabric mesh CLI 拡張モジュールをインストールしてください。

### <a name="install-docker"></a>Docker をインストールする

Service Fabric mesh が使用するコンテナー化された Service Fabric アプリをサポートするため、Docker をインストールします。

### <a name="windows-10"></a>Windows 10

最新バージョンの [Docker Community Edition for Windows][download-docker] をダウンロードしてインストールします。 

インストール中に求められた場合は、**[Use Windows containers instead of Linux containers]\(Linux コンテナーの代わりに Windows コンテナーを使用する\)** を選択します。 その後、サインアウトし、もう一度サインインします。 もう一度サインインした後、それまでに Hyper-V を有効にしていなかった場合は、Hyper-V の有効化を求めるメッセージが表示されることがあります。 Hyper-V を有効にしてから、コンピューターを再起動します。

コンピューターが再起動すると、Docker から**コンテナー**機能を有効にするよう求められます。有効にして、コンピューターを再起動します。

### <a name="windows-server-2016"></a>Windows Server 2016

次の PowerShell コマンドを使用して Docker をインストールします。 詳細については、[Docker Enterprise Edition for Windows Server][download-docker-server] に関するドキュメントを参照してください。

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

コンピューターを再起動します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure にサインインして有効なサブスクリプションを設定します。

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-a-container-registry-and-push-an-image-to-it"></a>コンテナー レジストリを作成し、それにイメージをプッシュする

[Azure CLI を使用した Azure のプライベート Docker レジストリの作成](../container-registry/container-registry-get-started-azure-cli.md)の指示に従って Azure Container Registry を作成します。 [ACR にログインする](../container-registry/container-registry-get-started-azure-cli.md#log-in-to-acr)手順を実行します。 

### <a name="push-image-to-acr"></a>ACR にイメージをプッシュする

Azure Container Registry にイメージをプッシュするには、まずイメージを用意する必要があります。 ローカル コンテナー イメージがまだない場合は、次のコマンドを実行して、既存のイメージを Docker Hub からプルします。

```bash
docker pull seabreeze/azure-mesh-helloworld:1.1-alpine
```

イメージをレジストリにプッシュするには、ACR ログイン サーバーの完全修飾名を使用して、そのイメージにタグを付けておく必要があります。 次のコマンドを実行して、ACR インスタンスの完全なログイン サーバー名を取得します。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

[docker tag][docker-tag] コマンドを使用してイメージにタグを付けます。 `<acrLoginServer>` を ACR インスタンスのログイン サーバー名で置き換えます。

```bash
docker tag seabreeze/azure-mesh-helloworld:1.1-alpine <acrLoginServer>/azure-mesh-helloworld:1.1-alpine
```
### <a name="list-container-images"></a>コンテナー イメージの一覧表示

次の例は、レジストリ内のリポジトリを一覧表示します。

```azurecli
az acr repository list --name <acrName> --output table
```

出力:

```bash
Result
----------------
azure-mesh-helloworld
```

次の例は、**azure-mesh-helloworld** リポジトリのタグを一覧表示します。

```azurecli
az acr repository show-tags --name <acrName> --repository azure-mesh-helloworld --output table
```

出力:

```bash
Result
--------
1.1-alpine
```
上記の出力により、プライベート コンテナー レジストリ内に `azure-mesh-helloworld:1.1-alpine` があることを確認します。

## <a name="retrieve-credentials-for-the-registry"></a>レジストリの資格情報を取得する

作成されたレジストリからコンテナー インスタンスをデプロイするには、デプロイ時に資格情報を指定する必要があります。 次のコマンドを使用して、レジストリの管理者ユーザーを有効にします。

```azurecli-interactive
az acr update --name <acrName> --admin-enabled true
```

次のコマンドを使用して、レジストリ サーバー名、ユーザー名とパスワードを取得します。

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name <acrName> --query username
az acr credential show --name <acrName> --query "passwords[0].value"
```

前のコマンドで指定された値は、次のコマンドでは `<acrLoginServer>`、`<acrUserName>`、および `<acrPassword>` と参照されています。


## <a name="deploy-the-template"></a>テンプレートのデプロイ

次のコマンドを使用して、アプリケーションおよび関連リソースを作成し、前の手順からの資格情報を指定します。

テンプレートの `registry-password` パラメーターは `securestring` です。 デプロイの状態および `az mesh service show` コマンドには表示されません。 次のコマンドで正しく指定されていることを確認してください。

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}, \"registry-server\": {\"value\": \"<acrLoginServer>\"}, \"registry-username\": {\"value\": \"<acrUserName>\"}, \"registry-password\": {\"value\": \"<acrPassword>\"}}" 
```

数分後に、以下のようなコマンドの結果が返されます。

`helloWorldPrivateRegistryApp has been deployed successfully on helloWorldPrivateRegistryNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>アプリケーションを開く
アプリケーションが正常にデプロイされたら、サービス エンドポイントのパブリック IP アドレスを取得し、ブラウザーで開きます。 Azure Service Fabric mesh のロゴの付いた Web ページが表示されます。

deployment コマンドは、サービス エンドポイントのパブリック IP アドレスを返します。 必要に応じて、ネットワーク リソースにクエリを送信して、サービス エンドポイントのパブリック IP アドレスを見つけることもできます。 
 
このアプリケーションのネットワーク リソース名は `helloWorldPrivateRegistryNetwork` です。次のコマンドを使用して、その情報を取得します。 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name helloWorldPrivateRegistryNetwork
```

## <a name="delete-the-resources"></a>リソースの削除

プレビュー プログラムに割り当てられている限られたリソースを節約するために、リソースは頻繁に削除してください。 この例に関連するリソースを削除するには、それらがデプロイされているリソース グループを削除します。

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>次の手順
- Hello World サンプル アプリケーションを [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) に表示します。
- Service Fabric リソース モデルの詳細については、[Service Fabric mesh リソース モデル](service-fabric-mesh-service-fabric-resources.md)に関するページを参照してください。
- Service Fabric mesh の詳細については、[Service Fabric mesh の概要](service-fabric-mesh-overview.md)に関するページを参照してください。

[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/