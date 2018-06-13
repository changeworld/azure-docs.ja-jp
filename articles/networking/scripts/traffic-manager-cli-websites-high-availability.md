---
title: Azure CLI スクリプト サンプル - 高可用性アプリケーションのためのトラフィックのルーティング | Microsoft Docs
description: Azure CLI スクリプト サンプル - 高可用性アプリケーションのためのトラフィックのルーティング
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: tysonn
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 38d0f55c266a77e6b786c483d6acdf62c50aab6f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2018
ms.locfileid: "29847354"
---
# <a name="route-traffic-for-high-availability-of-applications"></a>高可用性アプリケーションのためのトラフィックのルーティング

このスクリプトは、1 つのリソース グループ、2 つの App Service プラン、2 つの Web アプリ、1 つの Traffic Manager プロファイル、および 2 つの Traffic Manager エンドポイントを作成します。 Traffic Manager は、プライマリ リージョンのアプリケーションにトラフィックを誘導し、プライマリ リージョンのアプリケーションが利用できない場合には、セカンダリ リージョンにトラフィックを誘導します。 スクリプトを実行する前に、MyWebApp、MyWebAppL1、および MyWebAppL2 の値を、Azure 全体で一意の値に変更する必要があります。 スクリプトを実行した後は、URL mywebapp.trafficmanager.net でプライマリ リージョンのアプリケーションにアクセスできます。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、App Service アプリ、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、Web アプリ、Traffic Manager プロファイル、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | App Service プランを作成します。 App Service プランとは、Azure Web アプリ用のサーバー ファームのようなものです。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_create) | App Service プラン内に Azure Web アプリを作成します。 |
| [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) | Azure Traffic Manager プロファイルを作成します。 |
| [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) | Azure Traffic Manager プロファイルにエンドポイントを追加します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure のネットワークに関するドキュメント](../cli-samples.md)のページにあります。
