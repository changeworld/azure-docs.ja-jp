---
title: アラートを使用して問題に関する通知を受け取る - Azure IoT Edge
description: 大規模な監視に Azure Monitor の警告ルールを使用する
author: veyalla
ms.author: veyalla
ms.date: 06/08/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1de507f113f5bf457a4394be9e5fb86c37291e95
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015486"
---
# <a name="get-notified-about-issues-using-alerts-preview"></a>アラートを使用して問題に関する通知を受け取る (プレビュー)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

[Azure Monitor のログ アラート](../azure-monitor/alerts/alerts-unified-log.md)を使用して、大規模な IoT Edge デバイスを監視します。 [ソリューション アーキテクチャ](how-to-collect-and-transport-metrics.md#architecture)で強調されているように、Azure Monitor の Log Analytics はメトリック データベースとして使用されます。 この統合により、リソース中心のログ アラートを使用して、強力で柔軟なアラート機能を利用できるようになります。

> [!IMPORTANT]
> この機能は現在、IoT Hub でのみ使用可能であり、IoT Central では使用できません。

## <a name="create-an-alert-rule"></a>アラート ルールを作成する

デバイス群のさまざまな条件を監視するために、[ログ警告ルールを作成する](../azure-monitor/alerts/alerts-log.md)ことができます。

IoT Hub のリソースでは、[KQL](/azure/data-explorer/kusto/query/) アラート クエリのサンプルが提供されています。 エッジ デバイスのメトリック データに対して動作するクエリのタイトルの先頭には、*IoT Edge:* が付いています。 これらの例をそのまま使用するか、必要に応じて変更してニーズに合ったクエリを作成します。

アラート クエリの例にアクセスするには、次の手順のようにします。

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。
1. メニューの **[監視]** セクションから **[ログ]** を選択します。
1. **[クエリ]** を選択し、例のクエリ ブラウザーを開きます。

:::image type="content" source="./media/how-to-create-alerts/example-alerts.png" alt-text="アラート クエリの例にアクセスします。" lightbox="./media/how-to-create-alerts/example-alerts.png":::

[メトリック コレクター モジュール](how-to-collect-and-transport-metrics.md#metrics-collector-module)により、すべてのデータが標準の [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) テーブルに取り込まれます。 同じテーブルのクエリを実行することで、カスタム モジュールのメトリック データに基づく警告ルールを作成できます。

### <a name="split-by-device-dimension"></a>デバイスのディメンションで分割する

すべての警告ルールの例では、デバイス ID ごとの集計値のクエリが実行されます。 このグループ化は、アラートが発生する原因となったデバイスを特定するために必要です。 特定のデバイスを選択して警告ルールを有効にしたり、すべてのデバイスで有効にしたりすることができます。 アラートのロジックを設定する前に、プレビュー グラフを使用してデバイスごとの傾向を調べます。

### <a name="choose-notification-preferences"></a>通知の基本設定を選択する

[アクション グループ](../azure-monitor/alerts/action-groups.md)で通知の基本設定を構成し、警告ルールを作成するときにそれを警告ルールに関連付けます。

## <a name="select-alert-rule-scope"></a>警告ルールのスコープを選択する

前のセクションのガイダンスを使用すると、1 つの IoT ハブをスコープとする警告ルールが作成されます。 しかし、複数の IoT ハブに対して同じルールを作成したいことがあります。 スコープをリソース グループまたはサブスクリプション全体に変更し、そのスコープ内のすべてのハブで警告ルールを有効にします。

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。
1. メニューの **[監視]** セクションから **[ログ]** を選択します。
1. 警告ルールのスコープを変更するには、 **[スコープの選択]** を選択します。

:::image type="content" source="./media/how-to-create-alerts/change-scope.png" alt-text="アラートのスコープを変更する" lightbox="./media/how-to-create-alerts/change-scope.png":::

`_ResourceId` フィールドごとに値を集計し、警告ルールを作成するときに、"*リソース ID 列*" としてそれを選択します。 この方法を使用すると、アラートが適切なリソースに関連付けられ、利便性が向上します。

## <a name="viewing-alerts"></a>アラートの表示

[IoT Edge フリート ビュー ブック](how-to-explore-curated-visualizations.md#iot-edge-fleet-view-workbook)の **[アラート]** タブで、複数の IoT ハブのデバイスに対して生成されたアラートを表示します。

警告ルールの名前をクリックすると、アラートの詳細が表示されます。 デバイス名のリンクをクリックすると、アラートが発生した時刻前後のデバイスの詳細なメトリックが表示されます。

## <a name="next-steps"></a>次のステップ

[カスタム モジュールのメトリック](how-to-add-custom-metrics.md)を使用して、監視ソリューションを強化します。