---
title: Azure Files ベースのボリュームをコンテナー内にマウントして Azure Service Fabric Mesh アプリケーションに状態を保存する | Microsoft Docs
description: Azure CLI を使用して、Azure Files ベースのボリュームをコンテナー内にマウントして Azure Service Fabric Mesh アプリケーションに状態を保存する方法について説明します。
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
ms.date: 08/09/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 3b350deff2883761af6a3a2b3c5c9ef22235bde0
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038356"
---
# <a name="store-state-in-an-azure-service-fabric-mesh-application-by-mounting-an-azure-files-based-volume-inside-the-container"></a>Azure Files ベースのボリュームをコンテナー内にマウントして Azure Service Fabric Mesh アプリケーションに状態を保存する

この記事では、Service Fabric Mesh アプリケーションのコンテナー内にボリュームをマウントして Azure Files に状態を保存する方法について説明します。 この例のカウンター アプリケーションには、ブラウザーの Web ページにカウンター値を表示する ASP.NET Core サービスがあります。 

`counterService` は、ファイルからカウンター値を繰り返し読み取り、増分してファイルに書き戻します。 このファイルは、Azure Files 共有がサポートするボリュームにマウントされたフォルダーに格納されます。

## <a name="prerequisites"></a>前提条件

Azure Cloud Shell または Azure CLI のローカル インストールを使用して、このタスクを実行できます。 この記事で Azure CLI を使用するには、`az --version` が少なくとも `azure-cli (2.0.43)` を返すことを確認します。  こちらの[手順](service-fabric-mesh-howto-setup-cli.md)に従って、Azure Service Fabric Mesh CLI 拡張モジュールをインストール (または更新) してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure にサインインしてサブスクリプションを設定します。

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-file-share"></a>ファイル共有を作成する

こちらの[手順](/azure/storage/files/storage-how-to-create-file-share)に従って、Azure Files 共有を作成します。 ストレージ アカウント名、ストレージ アカウント キー、およびファイル共有名は、次の手順では `<storageAccountName>`、`<storageAccountKey>`、および `<fileShareName>` と呼ばれます。 これらの値は、Azure portal で利用できます。
* <storageAccountName> - **[ストレージ アカウント]** では、これはファイル共有を作成するときに使用したストレージ アカウントの名前です。
* <storageAccountKey> - **[ストレージ アカウント]** でストレージ アカウントを選択し、**[アクセス キー]** を選択して **key1** の下の値を使用します。
* <fileShareName> - **[ストレージ アカウント名]** でストレージ アカウントを選択し、**[ファイル]** を選択します。 使用する名前は、先ほど作成したファイル共有の名前です。

## <a name="create-a-resource-group"></a>リソース グループの作成

アプリケーションのデプロイ先となるリソース グループを作成します。 次のコマンドは、米国東部の場所に `myResourceGroup` という名前のリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>テンプレートのデプロイ

次のコマンドを使用して、アプリケーションおよび関連リソースを作成し、前の「[ファイル共有を作成する](#create-a-file-share)」の手順の `storageAccountName`、`storageAccountKey`、および `fileShareName` の値を指定します。

テンプレートの `storageAccountKey` パラメーターはセキュリティで保護された文字列です。 デプロイの状態および `az mesh service show` コマンドには表示されません。 次のコマンドで正しく指定されていることを確認してください。

次のコマンドでは、[mesh_rp.linux.json テンプレート](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json)を使用して Linux アプリケーションがデプロイされます。 Windows アプリケーションをデプロイするには、[mesh_rp.windows.json テンプレート](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json)を使用します。 大規模なコンテナー イメージはデプロイに時間がかかる場合があります。

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

数分後に、`counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>` のようなコマンドの結果が返されます。

## <a name="open-the-application"></a>アプリケーションを開く

deployment コマンドは、サービス エンドポイントのパブリック IP アドレスを返します。 アプリケーションが正常にデプロイされたら、サービス エンドポイントのパブリック IP アドレスを取得し、ブラウザーで開きます。 Web ページには、毎秒更新されるカウンター値が表示されます。

このアプリケーションのネットワーク リソース名は `counterAppNetwork` です。 説明、場所、リソース グループなどのアプリに関する情報は、次のコマンドを使用して確認できます。

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>アプリケーションがボリュームを使用できることを確認する

アプリケーションは、`counter/counterService` フォルダー内のファイル共有に `counter.txt`という名前のファイルを作成します。 このファイルの内容は、Web ページに表示されるカウンター値です。

このファイルは、[Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) など、Azure Files のファイル共有を参照できる任意のツールを使用してダウンロードできます。

## <a name="delete-the-resources"></a>リソースの削除

Azure で使用しなくなったリソースは頻繁に削除してください。 この例に関連するリソースを削除するには、次のコマンドを使用して、それらがデプロイされたリソース グループを削除します (これにより、リソース グループに関連付けられたすべてが削除されます)。

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>次の手順

- [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) 上の Azure Files ボリューム サンプル アプリケーションを確認します。
- Service Fabric リソース モデルの詳細については、[Service Fabric メッシュ リソース モデル](service-fabric-mesh-service-fabric-resources.md)に関するページを参照してください。
- Service Fabric mesh の詳細については、[Service Fabric mesh の概要](service-fabric-mesh-overview.md)に関するページを参照してください。