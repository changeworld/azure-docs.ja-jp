---
title: Azure Cosmos DB コントロール プレーン操作を監査する方法
description: リージョンの追加、スループットの更新、リージョンのフェールオーバー、VNet の追加など、コントロール プレーン操作を Azure Cosmos DB 内で監査する方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: sngun
ms.openlocfilehash: 64ad8e6b1101d8486268c857b3a7752e1801f52c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420272"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Azure Cosmos DB コントロール プレーン操作を監査する方法

コントロール プレーン操作には、Azure Cosmos アカウントまたはコンテナーに対する変更が含まれます。 たとえば、Azure Cosmos アカウントの作成、リージョンの追加、スループットの更新、リージョンのフェールオーバー、VNet の追加などは、コントロール プレーン操作の一部です。 この記事では、Azure Cosmos DB でコントロール プレーン操作を監査する方法について説明します。

## <a name="disable-key-based-metadata-write-access"></a>キー ベースのメタデータ書き込みアクセスを無効にする
 
Azure Cosmos DB でコントロール プレーン操作を監査する前に、アカウントでのキーベースのメタデータ書き込みアクセスを無効にします。 キーベースのメタデータ書き込みアクセスが無効になっている場合、アカウント キーを使用して Azure Cosmos アカウントに接続しているクライアントは、アカウントにアクセスできません。 書き込みアクセスを無効にするには、`disableKeyBasedMetadataWriteAccess` プロパティを true に設定します。 このプロパティを設定すると、適切なロールベースのアクセス制御 (RBAC) ロールと資格情報のみを持つユーザーが、任意のリソースに変更を加えることができます。 このプロパティを設定する方法の詳細については、「[SDK からの変更を防止する](role-based-access-control.md#preventing-changes-from-cosmos-sdk)」の記事を参照してください。

 メタデータ書き込みアクセスを無効にする場合は、次の点を考慮してください。

* SDK またはアカウント キーを使用して、アプリケーションが上記のリソース (コレクションの作成、スループットの更新、... など) を変更するメタデータ呼び出しを行わないようにして評価します。

* 現時点では、Azure portal はメタデータ操作にアカウント キーを使用するため、これらの操作はブロックされます。 または、Azure CLI、SDK、または Resource Manager テンプレートのデプロイを使用して、上記の操作を実行します。

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>コントロール プレーン操作の診断ログを有効にする

Azure portal を使用して、コントロール プレーン操作の診断ログを有効にすることができます。 コントロールプレーン操作のログ記録を有効にするには、次の手順を使用します。

1. [Azure portal](https://portal.azure.com) にサインインし、Azure Cosmos アカウントに移動します。

1. **[診断設定]** ウィンドウを開き、作成するログの **名称** を指定します。

1. ログの種類として **[ControlPlaneRequests]** を選択し、 **[Log Analytics に送信]** オプションを選択します。

また、ログをストレージ アカウントに保存したり、イベント ハブにストリーム配信したりすることもできます。 この記事では、ログ分析にログを送信してから、クエリを実行する方法について説明します。 有効にした後、診断ログが有効になるまで数分かかります。 その時点より後に実行されたすべてのコントロール プレーン操作を追跡できます。 次のスクリーンショットは、コントロール プレーン ログを有効化する方法を示したものです。

![コントロール プレーン要求のログを有効にする](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>コントロール プレーン操作の表示

ログを有効にした後、次の手順を使用して、特定のアカウントの操作を追跡します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側のナビゲーションから **[モニター]** タブを開き、 **[ログ]** ペインを選択します。 これにより、スコープ内の特定のアカウントでクエリを簡単に実行できる UI が開きます。 コントロール プレーンのログを表示するには、次のクエリを実行します。

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

次のスクリーンショットでは、VNET が Azure Cosmos アカウントに追加されたときにログをキャプチャします。

![VNet が追加されたときのコントロール プレーンのログ](./media/audit-control-plane-logs/add-ip-filter-logs.png)

次のスクリーンショットは、Cassandra テーブルのスループットが更新されたときにログをキャプチャします。

![スループットが更新されたときのコントロール プレーンのログ](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>特定の操作に関連付けられている ID を識別する

さらにデバッグする場合は、アクティビティ ID の使用または操作のタイムスタンプにより、**アクティビティ ログ** 内の特定の操作を特定できます。 タイムスタンプは、アクティビティ ID が明示的に渡されていない一部の Resource Manager クライアントに使用されます。 アクティビティ ログには、操作が開始された ID の詳細が示されます。 次のスクリーンショットは、アクティビティ ID を使用し、アクティビティ ログでその ID に関連付けられている操作を検索する方法を示しています。

![アクティビティ ID を使用して操作を検索する](./media/audit-control-plane-logs/find-operations-with-activity-id.png)

## <a name="next-steps"></a>次のステップ

* [Azure Monitor for Azure Cosmos DB の詳細](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Azure Cosmos DB のメトリックを使用した監視とデバッグ](use-metrics.md)