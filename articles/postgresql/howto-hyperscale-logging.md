---
title: ログ - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - Hyperscale (Citus) のデータベース ログにアクセスする方法
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 1dc7bc8e119de7c8fdcf09713286be2633457486
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90895860"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) のログ

PostgreSQL のログは、Hyperscale (Citus) サーバー グループのすべてのノードで使用できます。 ストレージ サーバーまたは分析サービスにログを送信できます。 ログは、構成エラーと十分に最適化されていないパフォーマンスの特定、トラブルシューティング、修復に使用できます。

## <a name="accessing-logs"></a>ログへのアクセス

Hyperscale (Citus) コーディネーターまたはワーカー ノードの PostgreSQL ログにアクセスするには、Azure portal でノードを開きます。

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="ノードのリスト":::

選択したノードで **[診断設定]** を開き、 **[+ 診断設定の追加]** をクリックします。

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="ノードのリスト":::

新しい診断設定の名前を選択し、 **[PostgreSQLLogs]** チェック ボックスをオンにします。  ログの受信先を選択します。

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="ノードのリスト":::

## <a name="next-steps"></a>次のステップ

- [Log Analytics クエリの使用方法](/azure/azure-monitor/log-query/get-started-portal)
- [Azure Event Hubs](/azure/event-hubs/event-hubs-about) について
