---
title: Azure portal でインスタンスのインベントリを表示する
description: Azure portal でインスタンスのインベントリを表示する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 5a5f13c74799e172d7ce9aefb170626cf61eb98e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558784"
---
# <a name="inventory-of-arc-enabled-data-services"></a>Arc 対応データ サービスのインベントリ


## <a name="view-resources-in-azure-portal"></a>Azure portal でリソースを表示する

[メトリック、ログ](upload-metrics-and-logs-to-azure-monitor.md)、または[使用状況](view-billing-data-in-azure.md)をアップロードすると、Azure Arc 対応の SQL マネージド インスタンスまたは Azure Arc 対応 Postgres Hyperscale サーバー グループを Azure portal で表示できるようになります。 [portal](https://portal.azure.com) でリソースを表示するには、次の手順に従います。

1. **[すべてのサービス]** に移動します。
1. データベース インスタンスの種類を検索します。
1. その種類をお気に入りに追加します。
1. 左側のメニューで、インスタンスの種類を選択します。
1. Azure SQL または Azure PostgreSQL Hyperscale の他のリソースと同じビューにインスタンスが表示されます (詳細なビューを表示するにはフィルターを使用します)。

## <a name="view-resources-in-your-kubernetes-cluster"></a>kubernetes クラスター内のリソースを表示する

Azure Arc データ コントローラーが **間接** 接続モードでデプロイされている場合は、次のコマンドを実行して、すべての Azure Arc SQL マネージド インスタンスの一覧を取得できます。
```
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
#Example
az sql mi-arc list --k8s-namespace arc --use-k8s
```

Azure Arc データ コントローラーが **直接** 接続モードでデプロイされている場合は、次のコマンドを実行して、すべての Azure Arc SQL マネージド インスタンスの一覧を取得できます。
```
az sql mi-arc list --resource-group <resourcegroup>
#Example
az sql mi-arc list --resource-group myResourceGroup
```
