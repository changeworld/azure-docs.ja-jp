---
title: テンプレートを使用して Azure Service Fabric mesh にアプリをデプロイする | Microsoft Docs
description: Azure CLI を使用してテンプレートから Service Fabric mesh に .NET Core アプリケーションをデプロイする方法について説明します。
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
ms.date: 07/12/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 356e8019f9a4a64cb1c1c113d0f44990aa4e0f95
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038557"
---
# <a name="deploy-a-service-fabric-mesh-application-to-service-fabric-mesh-using-a-template"></a>テンプレートを使用して Azure Service Fabric mesh に Azure Service Fabric mesh アプリケーションをデプロイする
この記事では、テンプレートを使用して Service Fabric mesh に .NET Core アプリケーションをデプロイする方法について説明します。 最後まで読み進めていけば、ASP.NET Core Web フロントエンドからクラスター内のバックエンド サービスに投票結果を保存する投票アプリケーションが完成します。 フロントエンドでは DNS を使用してバックエンド サービスのアドレスを解決します。

## <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric mesh CLI の設定 
Azure Cloud Shell または Azure CLI のローカル インストールを使用して、このタスクを実行できます。 こちらの[手順](service-fabric-mesh-howto-setup-cli.md)に従って、Azure Service Fabric mesh CLI 拡張モジュールをインストールしてください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン
Azure にサインインしてサブスクリプションを設定します。

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>リソース グループの作成
アプリケーションのデプロイ先となるリソース グループを作成します。 既存のリソース グループを使用して、この手順をスキップできます。 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>アプリケーションのデプロイ
`deployment create` コマンドを使用して、リソース グループにアプリケーションを作成します。

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

前述のコマンドでは、[mesh_rp.windows.json template](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json) を使用して Windows アプリケーションがデプロイされます。 Linux アプリケーションをデプロイする場合は、[mesh_rp.linux.json template](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.linux.json) を使用します。 Windows コンテナー イメージは Linux コンテナー イメージよりも大きく、デプロイに時間がかかることがあります。

数分後に、以下のようなコマンドの結果が返されます。

`VotingApp has been deployed successfully on VotingAppNetwork with public ip address <IP address>` 

## <a name="open-the-application"></a>アプリケーションを開く
アプリケーションが正常にデプロイされたら、サービス エンドポイントのパブリック IP アドレスを取得し、ブラウザーで開きます。 次の Web ページが表示されます。 

![投票アプリケーション](./media/service-fabric-mesh-howto-deploy-app-template/VotingApplication.png)

ここでは、投票オプションをアプリケーションに追加してそれに投票するか、投票オプションを削除することができます。

deployment コマンドは、サービス エンドポイントのパブリック IP アドレスを返します。 必要に応じて、ネットワーク リソースにクエリを送信して、サービス エンドポイントのパブリック IP アドレスを見つけることもできます。 

このアプリケーションのネットワーク リソース名は `VotingAppNetwork` です。次のコマンドを使用して、その情報を取得します。 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name VotingAppNetwork
```

## <a name="check-the-application-details"></a>アプリケーションの詳細の確認
`app show` コマンドを使用して、アプリケーションの状態を確認できます。 デプロイされたアプリケーションのアプリケーション名は "VotingApp" となりますので、その詳細をフェッチします。 

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name VotingApp
```

## <a name="list-the-deployed-applications"></a>デプロイされたアプリケーションを一覧表示する
"app list" コマンドを使用して、サブスクリプションにデプロイされているアプリケーションのリストを取得できます。 

```azurecli-interactive
az mesh app list -o table
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ
アプリケーションとそれに関連するリソースが不要になったら、それらを含むリソース グループを削除します。 

```azurecli-interactive
az group delete --resource-group myResourceGroup  
``` 

## <a name="next-steps"></a>次の手順
- [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) で投票サンプル アプリケーションを表示する。
- Service Fabric mesh の詳細については、[Service Fabric mesh の概要](service-fabric-mesh-overview.md)に関するページを参照してください。


