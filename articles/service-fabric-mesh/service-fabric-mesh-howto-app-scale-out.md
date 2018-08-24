---
title: Azure Service Fabric mesh アプリケーションのサービスをスケールする | Microsoft Docs
description: Azure CLI を使用して Service Fabric mesh で実行されているアプリケーション内のサービスを個別にスケールする方法について説明します。
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
ms.date: 08/08/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: e68bcd135c33c7fd83908b8fed0fd098a698fd36
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038589"
---
# <a name="scale-services-within-an-application-running-on-service-fabric-mesh"></a>Service Fabric mesh 上で実行されているアプリケーション内でのサービスのスケール

この記事では、アプリケーション内でマイクロ サービスを個別にスケールする方法を示します。 この例では、Visual Objects アプリケーションは 2 つのマイクロ サービス `web` と `worker` で構成されています。

`web` サービスは、ASP.NET Core アプリケーションで、ブラウザーに三角形を表示する Web ページがあります。 `worker` サービスのインスタンスごとに三角形が 1 つブラウザーに表示されます。

`worker` サービスは、事前定義された間隔で空間内で三角形を移動し、三角形の位置を `web` サービスに送信します。 DNS を使用して `web` サービスのアドレスを解決します。

## <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric mesh CLI の設定

Azure Cloud Shell または Azure CLI のローカル インストールを使用して、このタスクを実行できます。 こちらの[手順](service-fabric-mesh-howto-setup-cli.md)に従って、Azure Service Fabric mesh CLI 拡張モジュールをインストールしてください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure にサインインしてサブスクリプションを設定します。

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>リソース グループの作成

アプリケーションのデプロイ先となるリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application-with-one-worker-service"></a>1 つの worker サービスを使ってアプリケーションをデプロイします。

`deployment create` コマンドを使用して、リソース グループにアプリケーションを作成します。

次の例では、[mesh_rp.base.linux.json テンプレート](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json)を使用して Linux アプリケーションをデプロイします。 Windows アプリケーションをデプロイするには、[[mesh_rp.base.windows.json テンプレート](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.windows.json)を使用します。 Windows コンテナー イメージは Linux コンテナー イメージよりも大きく、デプロイに時間がかかることがあります。

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

数分後に、次のようなコマンドの結果が返されます。

`visualObjectsApp has been deployed successfully on visualObjectsNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>アプリケーションを開く

deployment コマンドは、サービス エンドポイントのパブリック IP アドレスを返します。 アプリケーションが正常にデプロイされたら、サービス エンドポイントのパブリック IP アドレスを取得し、ブラウザーで開きます。 Web ページと移動する三角形が表示されます。

このアプリケーションのネットワーク リソース名は `visualObjectsNetwork` です。 説明、場所、リソース グループなどのアプリに関する情報は、次のコマンドを使用して確認できます。

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name visualObjectsNetwork
```

## <a name="scale-worker-service"></a>`worker` サービスのスケール

次のコマンドを使用して、`worker` サービスを 3 つのインスタンスにスケールします。 次の例では、[mesh_rp.scaleout.linux.json テンプレート](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json)を使用して Linux アプリケーションをデプロイします。 Windows アプリケーションをデプロイするには、[mesh_rp.scaleout.windows.json テンプレート](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.windows.json)を使用します。 大規模なコンテナー イメージはデプロイに時間がかかる場合があります。

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```

アプリケーションが正常にデプロイされると、ブラウザーに Web ページと 3 つの移動する三角形が表示されます。

## <a name="delete-the-resources"></a>リソースの削除

Azure で使用しなくなったリソースは頻繁に削除してください。 この例に関連するリソースを削除するには、次のコマンドを使用して、それらがデプロイされたリソース グループを削除します (これにより、リソース グループに関連付けられたすべてが削除されます)。

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>次の手順

- サンプル アプリケーションを [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) に表示します。
- Service Fabric リソース モデルの詳細については、[Service Fabric mesh リソース モデル](service-fabric-mesh-service-fabric-resources.md)に関するページを参照してください。
- Service Fabric mesh の詳細については、[Service Fabric mesh の概要](service-fabric-mesh-overview.md)に関するページを参照してください。