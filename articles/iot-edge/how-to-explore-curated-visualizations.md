---
title: キュレーションされた視覚化を探索する - Azure IoT Edge
description: Azure ブックを使用して、IoT Edge の組み込みのメトリックを視覚化して探索します
author: veyalla
ms.author: veyalla
ms.date: 08/11/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7be7260e993f9cc95b542406767d6794f18836f8
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015656"
---
# <a name="explore-curated-visualizations-preview"></a>キュレーションされた視覚化を探索する (プレビュー)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure Monitor ブックを使用して、Azure Portal で IoT Edge デバイスから収集されたメトリックを視覚化して探索できます。 Azure IoT Edge デバイス用のキュレーションされた監視ブックは、パブリック テンプレートの形式で提供されています。

* IoT Hub に接続されているデバイスの場合、Azure portal の **[IoT Hub]** ブレードから、 **[監視]** セクションの **[ブック]** ページに移動します。
* IoT Central に接続されているデバイスの場合、Azure portal の **[IoT Central]** ブレードから、 **[監視]** セクションの **[ブック]** ページに移動します。

キュレーション ブックでは、IoT Edge ランタイムの[組み込みのメトリック](how-to-access-built-in-metrics.md)が使用されます。 これらのビューでは、ワークロード モジュールのメトリック インストルメンテーションは必要ではありません。

## <a name="access-curated-workbooks"></a>キュレーション ブックにアクセスする

IoT 用の Azure Monitor ブックは、メトリックの視覚化を今すぐ開始するために使用したり、ソリューションに合わせてカスタマイズしたりできる一連のテンプレートです。

キュレーション ブックにアクセスするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインし、IoT Hub または IoT Central アプリケーションに移動します。

1. メニューの **[監視]** セクションから **[ブック]** を選択します。

1. パブリック テンプレートの一覧から探索するブックを選択します。

  * **IoT Edge フリート ビュー**: デバイスのフリートを監視し、特定のデバイスをドリルダウンして正常性スナップショットを表示します。
  * **IoT Edge デバイスの詳細**: IoT Edge デバイス上のメッセージング、モジュール、ホスト コンポーネントに関するデバイスの詳細を視覚化します。
  * **IoT Edge の正常性スナップショット**: 6 つの一般的なパフォーマンス メトリックに基づいてデバイスの正常性を表示します。 正常性スナップショット ブックにアクセスするには、フリート ビュー ブックから開始し、表示する特定のデバイスを選択します。 フリート ビュー ブックでは、必要なパラメーターが正常性スナップショット ビューに渡されます。

ブックを独自に探索することも、次のセクションを使用して、各ブックで提供されるデータと視覚化のプレビューを取得することもできます。

## <a name="iot-edge-fleet-view-workbook"></a>IoT Edge フリート ビュー ブック

フリート ビュー ブックでは、次の 2 つのビューを使用できます。

* **[デバイス]** ビューには、アクティブなデバイスの概要が表示されます。
* **[警告]** ビューには、[事前構成済みのアラート ルール](how-to-create-alerts.md)から生成されたアラートが表示されます。

ビューを切り替えるには、ブックの上部にあるタブを使用します。

# <a name="devices"></a>[デバイス](#tab/devices)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-fleet-view.gif" alt-text="フリート ビュー ブックの [デバイス] セクション。" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-fleet-view.gif":::

**[デバイス]** ビューで、メトリックを送信するアクティブなデバイスの概要を確認します。 このビューには、現在の IoT Hub または IoT Central アプリケーションに関連するデバイスが表示されます。

右側には、送信されたローカル メッセージとアップストリーム メッセージが表示された複合バーを含むデバイスの一覧があります。 デバイス名で一覧をフィルター処理し、デバイス名のリンクをクリックすると、その詳細なメトリックを表示できます。

左側の Hive セルの視覚化には、正常または異常なデバイスが表示されます。 また、デバイスで最後にメトリックが送信された時間も表示されます。 メトリックを 30 分以上送信していないデバイスは、青色で表示されます。 Hive セル内のデバイス名をクリックして、正常性スナップショットを表示します。 正常性を判断する際には、デバイスからの最後の 3 つの測定値だけが考慮されます。 最新のデータのみを使用するため、報告されるメトリックは一時的に急増します。

# <a name="alerts"></a>[警告](#tab/alerts)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-alerts.gif" alt-text="フリート ビュー ブックの [警告] セクション。" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-alerts.gif":::

**[警告]** ビューで、[事前に作成されたアラート ルール](how-to-create-alerts.md)から生成されたアラートを確認します。 このビューでは、複数の IoT Hub または IoT Central アプリケーションからのアラートを表示できます。

左側には、アラートの重大度とその数の一覧があります。 右側には、リージョンごとのアラートの合計を含むマップがあります。

重大度の行をクリックすると、アラートの詳細が表示されます。 **[アラート ルール]** リンクをクリックするとアラート コンテキストにアクセスでき、 **[デバイス]** リンクをクリックすると詳細なメトリック ブックが開きます。 このビューから開いた場合、デバイスの詳細ブックは、アラートが発生した時間の範囲に自動的に調整されます。

---

## <a name="iot-edge-device-details-workbook"></a>IoT Edge デバイスの詳細ブック

デバイスの詳細ブックでは、次の 3 つのビューを使用できます。

* **[メッセージング]** ビューでは、デバイスのメッセージ ルートが視覚化され、メッセージング システムの全体的な正常性が報告されます。
* **[モジュール]** ビューでは、デバイス上の個々のモジュールがどのように実行されているのかが表示されます。
* **[ホスト]** ビューでは、ホスト コンポーネントのバージョン情報やリソースの使用など、ホスト デバイスに関する情報が表示されます。

ビューを切り替えるには、ブックの上部にあるタブを使用します。

デバイスの詳細ブックでは、IoT Edge ポータル ベースのトラブルシューティング エクスペリエンスと統合して、デバイスの **[ライブ ログ]** を表示することもできます。 このエクスペリエンスにアクセスするには、ブックの上にある **[ライブ \<device name> のトラブルシューティング]** ボタンを選択します。

# <a name="messaging"></a>[メッセージング](#tab/messaging)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-messaging-details.gif" alt-text="デバイスの詳細ブックの [メッセージング] セクション。" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-messaging-details.gif":::

**[メッセージング]** ビューには、ルーティングの詳細、ルーティング グラフ、メッセージングの正常性の 3 つのサブセクションが含まれています。 任意の時間グラフをドラッグして移動し、選択した範囲に合わせてグローバル時間範囲を調整します。

**[ルーティング]** セクションには、モジュールの送信とモジュールの受信の間のメッセージ フローが表示されます。 メッセージの数、レート、接続されているクライアントの数などの情報が表示されます。 送信者または受信者をクリックすると、さらに詳しい情報が表示されます。 送信者をクリックすると、送信者が経験した待機時間の傾向グラフと送信されたメッセージの数が表示されます。 受信者をクリックすると、受信側のキューの長さの傾向と受信したメッセージの数が表示されます。

**[グラフ]** セクションには、モジュール間のメッセージ フローの視覚的な表現が表示されます。 ドラッグとズームを使用してグラフを調整できます。

**[正常性]** セクションには、メッセージング サブシステムの全体的な正常性に関連するさまざまなメトリックが表示されます。 エラーが検出された場合は、段階的に詳細にドリルインできます。

# <a name="modules"></a>[モジュール](#tab/modules)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-module-details.gif" alt-text="デバイスの詳細ブックの [モジュール] セクション。" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-module-details.gif":::

**[モジュール]** ビューには、edgeAgent モジュールから収集されたメトリックが表示されます。ここでは、デバイス上で実行中のすべてのモジュールの状態が報告されます。 次のような情報が含まれています。

* モジュールの可用性
* モジュールごとの CPU とメモリの使用
* すべてのモジュールでの CPU とメモリの使用
* モジュールの再起動回数と再起動のタイムライン

# <a name="host"></a>[Host](#tab/host)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-host-details.gif" alt-text="デバイスの詳細ブックの [ホスト] セクション。" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-host-details.gif":::

**[ホスト]** ビューには、edgeAgent モジュールのメトリックが表示されます。 次のような情報が含まれています。

* ホスト コンポーネントのバージョン情報
* Uptime
* ホスト レベルでの CPU、メモリ、ディスク領域の使用

# <a name="live-logs"></a>[ライブ ログ](#tab/livelogs)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-troubleshoot-live.gif" alt-text="デバイスの詳細ブックを使用してライブ ログにアクセスします。" lightbox="./media/how-to-explore-curated-visualizations/how-to-troubleshoot-live.gif":::

このブックは、ポータル ベースのトラブルシューティング エクスペリエンスと直接統合されます。 **[ライブ トラブルシューティング]** ボタンをクリックすると、トラブルシューティング画面に移動します。 ここでは、デバイスから抽出されたモジュール ログをオンデマンドで簡単に表示できます。 時間の範囲はブックの時間の範囲に自動的に設定されます。そのため、すぐに一時的なコンテキストになります。 このエクスペリエンスから任意のモジュールを再起動することもできます。

---

## <a name="iot-edge-health-snapshot-workbook"></a>IoT Edge の正常性スナップショット ブック

正常性スナップショット ブックには、フリート ビュー ブック内からアクセスできます。 フリート ビュー ブックによって、正常性スナップショット ビューを初期化するために必要なパラメーターが渡されます。 Hive セルでデバイス名を選択すると、そのデバイスの正常性スナップショットが表示されます。

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-access-health-snapshot.png" alt-text="フリート ビュー ブックでデバイスを選択して、正常性スナップショット ブックにアクセスします。" lightbox="./media/how-to-explore-curated-visualizations/how-to-access-health-snapshot.png":::

既定では、正常性スナップショットは次の 6 つのシグナルで構成されています。

* アップストリーム メッセージ
* ローカル メッセージ
* Queue length
* ディスク使用量
* ホスト レベルの CPU 使用率
* ホスト レベルのメモリ使用率

これらのシグナルは、デバイスが正常かどうかを判断するために、構成可能なしきい値に対して測定されます。 ブックを編集すると、しきい値を調整したり、新しいシグナルを追加したりすることができます。 ブックのカスタマイズについては、次のセクションを参照してください。

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-health-snapshot.gif" alt-text="正常性スナップショット ブックを表示します。" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-health-snapshot.gif":::

## <a name="customize-workbooks"></a>ブックをカスタマイズする

[Azure Monitor ブック](../azure-monitor/visualize/workbooks-overview.md)はさまざまにカスタマイズできます。 パブリック テンプレートは、要件に合わせて編集できます。 すべての視覚化は、[InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) テーブルに対するリソース中心の [KQL](/azure/data-explorer/kusto/query/) クエリによって駆動されます。 正常性しきい値を編集する次の例を参照してください。

ブックのカスタマイズを開始するには、最初に編集モードに入ります。 ブックのメニュー バーにある **[編集]** ボタンを選択します。

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-access-edit-mode.png" alt-text="ブックの編集モードに入ります。" lightbox="./media/how-to-explore-curated-visualizations/how-to-access-edit-mode.png":::

キュレーション ブックでは、ブック グループを幅広く使用します。 視覚化クエリを表示するには、入れ子になった複数のグループで **[編集]** をクリックする必要がある場合があります。

変更内容を新しいブックとして保存します。 保存したブックをチームと[共有](../azure-monitor/visualize/workbooks-access-control.md)したり、組織のリソース デプロイの一部として[プログラムでデプロイ](../azure-monitor/visualize/workbooks-automate.md)したりできます。

たとえば、デバイスが正常または異常と見なされる場合のしきい値を変更できます。 これを行うには、このブックでデバイスを比較するすべてのメトリックのしきい値を含む **device-health-graph** クエリ項目にアクセスするまで、フリート ビュー ブック テンプレートをドリルダウンします。

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-edit-thresholds.gif" alt-text="クエリ項目に到達するまで、入れ子になったコンポーネントの編集モードを開き続けます。" lightbox="./media/how-to-explore-curated-visualizations/how-to-edit-thresholds.gif":::

## <a name="next-steps"></a>次の手順

カスタム モジュールの[アラート ルール](how-to-create-alerts.md)と[メトリック](how-to-add-custom-metrics.md)を使用して、監視ソリューションをカスタマイズします。