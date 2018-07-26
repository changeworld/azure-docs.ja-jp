---
title: Azure Files ベースのボリュームを Service Fabric Mesh アプリケーションのコンテナー内にマウントして状態を保存する | Microsoft Docs
description: Azure CLI を使用して、Azure Files ベースのボリュームを Service Fabric Mesh アプリケーションのコンテナー内にマウントして状態を保存する方法について説明します。
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 94a4e17e6285893520a2f6482b32a69b1229e2fa
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090634"
---
# <a name="store-state-by-mounting-azure-files-based-volume-in-service-fabric-mesh-application"></a>Azure Files ベースのボリュームを Service Fabric Mesh アプリケーションにマウントして状態を保存する

この記事では、Service Fabric Mesh アプリケーションのコンテナー内にボリュームをマウントして Azure Files に状態を保存する方法について説明します。 この例のカウンター アプリケーションには、ブラウザーの Web ページにカウンター値を表示する ASP.NET Core サービスがあります。 

`counterService` は、ファイルからカウンター値を繰り返し読み取り、増分してファイルに書き戻します。 このファイルは、Azure Files 共有がサポートするボリュームにマウントされたフォルダーに格納されます。 

## <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric mesh CLI の設定 
Azure Cloud Shell または Azure CLI のローカル インストールを使用して、このタスクを完了できます。 こちらの[手順](service-fabric-mesh-howto-setup-cli.md)に従って、Azure Service Fabric Mesh CLI 拡張モジュールをインストールしてください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン
Azure にサインインしてサブスクリプションを設定します。

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-file-share"></a>ファイル共有の作成 
こちらの[手順](/azure/storage/files/storage-how-to-create-file-share)に従って、Azure Files 共有を作成します。 ストレージ アカウント名、ストレージ アカウント キー、およびファイル共有名は、次の手順では `<storageAccountName>`、`<storageAccountKey>`、および `<fileShareName>` と呼ばれます。

## <a name="create-resource-group"></a>リソース グループの作成
アプリケーションをデプロイするリソース グループを作成します。 既存のリソース グループを使用すると、この手順をスキップできます。 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>テンプレートのデプロイ

次のコマンドを使用して、アプリケーションおよび関連リソースを作成し、前の手順の `storageAccountName`、`storageAccountKey`、および `fileShareName` の値を指定します。

テンプレートの `storageAccountKey` パラメーターは `securestring` です。 デプロイの状態および `az mesh service show` コマンドには表示されません。 次のコマンドで正しく指定されていることを確認してください。

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

前述のコマンドでは、[mesh_rp.linux.json template](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json) を使用して Linux アプリケーションがデプロイされます。 Windows アプリケーションをデプロイする場合は、[mesh_rp.windows.json template](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json) を使用します。 Windows コンテナー イメージは Linux コンテナー イメージよりも大きく、デプロイに時間がかかることがあります。

数分後に、以下のようなコマンドの結果が返されます。

`counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>アプリケーションを開く
アプリケーションが正常にデプロイされたら、サービス エンドポイントのパブリック IP アドレスを取得し、ブラウザーで開きます。 Web ページには、毎秒更新されるカウンター値が表示されます。

deployment コマンドは、サービス エンドポイントのパブリック IP アドレスを返します。 必要に応じて、ネットワーク リソースにクエリを送信して、サービス エンドポイントのパブリック IP アドレスを見つけることもできます。 
 
このアプリケーションのネットワーク リソース名は `counterAppNetwork` です。次のコマンドを使用して、その情報を取得します。 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>アプリケーションがボリュームを使用できることを確認する
アプリケーションは、`counter/counterService` フォルダー内のファイル共有に `counter.txt`という名前のファイルを作成します。 このファイルの内容は、Web ページに表示されるカウンター値です。

このファイルは、Azure Files のファイル共有を参照できる任意のツールを使用してダウンロードできます。 たとえば、[Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) などのツールです。

## <a name="delete-the-resources"></a>リソースの削除

プレビュー プログラムに割り当てられている限られたリソースを節約するために、リソースは頻繁に削除してください。 この例に関連するリソースを削除するには、それらがデプロイされているリソース グループを削除します。

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>次の手順

- [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) 上の Azure Files ボリューム サンプル アプリケーションを確認します。
- Service Fabric リソース モデルの詳細については、[Service Fabric メッシュ リソース モデル](service-fabric-mesh-service-fabric-resources.md)に関するページを参照してください。
- Service Fabric メッシュの詳細については、[Service Fabric メッシュの概要](service-fabric-mesh-overview.md)に関するページを参照してください。
