---
title: Azure Cosmos DB アカウントにおけるキーの更新とキーの再生成を監視する
description: '[更新されたアカウント キー] メトリックを使用して、アカウントにおけるキーの更新を監視します。 このメトリックによって、アカウントの主および 2 次キーが更新された回数と、それらが変更された時刻が示されます。'
ms.service: cosmos-db
ms.topic: how-to
ms.author: sngun
author: SnehaGunda
ms.date: 09/16/2021
ms.openlocfilehash: fe344bdfbd9d4c88fd5cdf4024052ac122cf0e5d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594413"
---
# <a name="monitor-your-azure-cosmos-db-account-for-key-updates-and-key-regeneration"></a>Azure Cosmos DB アカウントにおけるキーの更新とキーの再生成を監視する

Azure Monitor for Azure Cosmos DB には、ご利用のアカウントを監視するためのメトリック、アラート、ログが用意されています。 ダッシュボードを作成し、必要に応じてカスタマイズすることができます。 Azure Cosmos DB のメトリックは既定で収集されるので、何も明示的に有効にしたり構成したりする必要はありません。 アカウントのキーの更新を監視するには、 **[更新されたアカウント キー]** メトリックを使用します。 このメトリックによって、アカウントの主および 2 次キーが更新された回数と、それらが変更された時刻が示されます。 また、キーが更新されたときに通知を受け取るようにアラートを設定することもできます。

## <a name="monitor-key-updates-with-metrics"></a>メトリックを使用してキーの更新を監視する

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. 左にあるナビゲーション バーから **[監視]** を選択し、 **[メトリック]** を選択します。

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Azure Monitor のメトリック ペイン" border="true":::

1. **[メトリック]** ウィンドウで、メトリックを表示するリソースのスコープを選択します。

   1. まず、必要な **サブスクリプション** を選択します。 **[リソースの種類]** フィールドでは、 **[Azure Cosmos DB アカウント]** を選択します。 Azure Cosmos DB アカウントが置かれているリソース グループの一覧が表示されます。

   1. **[リソース グループ]** を選択し、ご利用の既存の Azure Cosmos アカウントの 1 つを選択します。 [適用] を選択します。

   :::image type="content" source="./media/monitor-account-key-updates/select-account-scope.png" alt-text="メトリックを表示するアカウント スコープを選択する" border="true":::

1. **[メトリック]** フィールドでは、 **[更新されたアカウント キー]** を選択します。 **[集計]** フィールドは、既定値の **[カウント]** のままにします。 このビューには、選択したアカウントの主および 2 次キーの更新回数の合計が表示されます。 グラフ内のタイムラインを選択して、キーが更新された日時を確認することもできます。

1. さらに変更されたキーを確認するには、 **[Apply splitting]\(分割の適用\)** オプションを選択します。 **[KeyType]** を選択し、 **[Limit]** 、 **[Sort]** プロパティの順に設定します。 次の図に示すように、グラフは主および 2 次キーの更新によって分割されるようになりました。

   :::image type="content" source="./media/monitor-account-key-updates/account-keys-updated-metric-chart.png" alt-text="主および 2 次キーが更新された場合のメトリック グラフ" border="true" lightbox="./media/monitor-account-key-updates/account-keys-updated-metric-chart.png":::

## <a name="configure-alerts-for-a-key-update"></a>キー更新に対するアラートを構成する

アカウントを監視してキーの更新を確認することができます。 キーのローテーションまたは更新が行われた場合は、引き続き機能するように依存するクライアント アプリケーションを更新する必要があります。 アラートを構成すると、キーが更新されたときに通知を受け取ることができます。

何らかの変更が加えられた場合は、[アラートの作成](create-alerts.md)に関する記事にある手順を使用してください。 アラートの条件を選択する場合は、 **[更新されたアカウント キー]** シグナルを選択します。 **[KeyType]** ディメンションを選択し、 **[主]** または **[2 次]** キーを選択します。 選択したキーの種類に基づき、キーが更新されるとアラートがトリガーされます。

次のスクリーンショットでは、アカウント キーが更新されたときのアラートの種類を "クリティカル" に構成する方法を示しています。

:::image type="content" source="./media/monitor-account-key-updates/configure-key-update-alert.png" alt-text="アカウント キーが更新されたときに電子メール通知を受け取るようにアラートを構成する":::

## <a name="next-steps"></a>次のステップ

* Azure の[診断設定](cosmosdb-monitor-resource-logs.md)を使用して Azure Cosmos DB データを監視する
* [Azure Cosmos DB コントロール プレーン操作を監査する](audit-control-plane-logs.md)