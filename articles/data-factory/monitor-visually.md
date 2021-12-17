---
title: Azure Data Factory を視覚的に監視する
description: Azure Data Factory を視覚的に監視する方法について説明します
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 79dcf37a5f70e68bddfc165ea8798994c7af9798
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845534"
---
# <a name="visually-monitor-azure-data-factory"></a>Azure Data Factory を視覚的に監視する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory でパイプラインを作成して公開した後、それをトリガーに関連付けたり、アドホック実行を手動で開始したりできます。 Azure Data Factory ユーザー エクスペリエンスで、すべてのパイプラインの実行をネイティブに監視できます。 監視エクスペリエンスを開くには、[Azure portal](https://portal.azure.com/) の [データ ファクトリ] ブレードで **[監視と管理]** タイルを選択します。 ADF UX を既に使用している場合は、左側のサイドバーにある **[監視]** アイコンをクリックします。

既定で、すべてのデータ ファクトリの実行は、ブラウザーのローカル タイム ゾーンで表示されます。 タイム ゾーンを変更した場合、すべての日付/時刻フィールドは選択したものにスナップされます。

## <a name="monitor-pipeline-runs"></a>パイプラインの実行を監視する

既定の監視ビューは、選択した期間でトリガーされたパイプラインの実行の一覧です。 時間の範囲を変更したり、状態、パイプライン名、または注釈でフィルター処理したりすることができます。 特定のパイプラインの実行にマウス ポインターを合わせて、再実行や消費レポートなどの実行固有のアクションを取得します。

:::image type="content" source="media/monitor-visually/pipeline-runs.png" alt-text="パイプラインの実行を監視するためのリスト ビュー":::

パイプラインの実行グリッドには、次の列が含まれています。

| **列名** | **説明** |
| --- | --- |
| Pipeline Name (パイプライン名) | パイプラインの名前 |
| Run Start (実行の開始) | パイプラインの実行の開始日時 (MM/DD/YYYY, HH:MM:SS AM/PM) |
| 実行終了日時 | パイプラインの実行の終了日時 (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duration | 実行期間 (HH:MM:SS) |
| Triggered By (トリガー元) | パイプラインを開始したトリガーの名前 |
| Status | **失敗**、**成功**、**進行中**、**キャンセル済**、または **キューに登録済み** |
| 注釈 | パイプラインに関連付けられたフィルター可能なタグ  |
| パラメーター | パイプラインの実行のパラメーター (名前/値のペア) |
| エラー | パイプラインが失敗した場合の実行エラー |
| Run ID (実行 ID) | パイプライン実行の ID。 |

パイプラインとアクティビティの実行一覧を最新の情報に更新するには、 **[最新の情報に更新]** ボタンを手動で選択する必要があります。 現在、自動更新はサポートされていません。

:::image type="content" source="media/monitor-visually/refresh.png" alt-text="[更新] ボタン":::

デバッグ実行の結果を表示するには、 **[デバッグ]** タブを選択します。

:::image type="content" source="media/iterative-development-debugging/view-debug-runs.png" alt-text="[View active debug runs] アイコンを選択":::

## <a name="monitor-activity-runs"></a>アクティビティの実行を監視する

特定のパイプライン実行の個々のアクティビティの実行の詳細ビューを取得するには、パイプライン名をクリックします。

:::image type="content" source="media/monitor-visually/view-activity-runs.png" alt-text="アクティビティの実行の表示":::

リスト ビューには、各パイプラインの実行に対応するアクティビティの実行が表示されます。 特定のアクティビティの実行にマウス ポインターを合わせると、JSON 入力、JSON 出力、アクティビティ固有の詳細な監視エクスペリエンスなどの実行固有の情報を取得できます。

:::image type="content" source="media/monitor-visually/activity-runs.png" alt-text="SalesAnalyticsMLPipeline に関する情報に続いて、アクティビティの実行の一覧が表示されます。":::

| **列名** | **説明** |
| --- | --- |
| Activity Name (アクティビティ名) | パイプライン内のアクティビティの名前 |
| Activity Type (アクティビティの種類) | アクティビティの種類 (**Copy**、**ExecuteDataFlow**、**AzureMLExecutePipeline**  など) |
| Actions | JSON 入力情報、JSON 出力情報、またはアクティビティ固有の詳細な監視エクスペリエンスの表示を可能にするアイコン | 
| Run Start (実行の開始) | アクティビティの実行の開始日時 (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duration | 実行期間 (HH:MM:SS) |
| Status | **失敗**、**成功**、**進行中**、または **キャンセル済** |
| 統合ランタイム | アクティビティが実行された統合ランタイム |
| ユーザー プロパティ | アクティビティのユーザー定義プロパティ |
| エラー | アクティビティが失敗した場合の実行エラー |
| Run ID (実行 ID) | アクティビティ実行の ID。 |

アクティビティが失敗した場合、エラー列のアイコンをクリックすると、詳細なエラー メッセージを確認できます。 

:::image type="content" source="media/monitor-visually/activity-run-error.png" alt-text="エラー コード、エラーの種類、エラーの詳細などのエラー詳細が含まれる通知が表示されます。":::

### <a name="promote-user-properties-to-monitor"></a>監視対象のユーザー プロパティを昇格する

監視対象のエンティティになるように、任意のパイプラインのアクティビティ プロパティをユーザー プロパティとして昇格します。 たとえば、パイプラインのコピー アクティビティの **Source** プロパティと **Destination** プロパティをユーザー プロパティとして昇格できます。

> [!NOTE]
> ユーザー プロパティとして昇格できるパイプライン アクティビティ プロパティは、最大 5 つです。

:::image type="content" source="media/monitor-visually/promote-user-properties.png" alt-text="ユーザー プロパティの作成":::

作成したユーザー プロパティは、監視リスト ビューで監視できます。

:::image type="content" source="media/monitor-visually/choose-user-properties.png" alt-text="アクティビティの実行の一覧へのユーザー プロパティの列の追加":::

 コピー アクティビティのソースがテーブル名の場合、アクティビティの実行のリスト ビューでソースのテーブル名を列として監視できます。

:::image type="content" source="media/monitor-visually/view-user-properties.png" alt-text="アクティビティ実行の一覧とユーザー プロパティの列":::


## <a name="rerun-pipelines-and-activities"></a>パイプラインとアクティビティの再実行
 
 コンテナー アクティビティの再実行動作は次のとおりです。
 
- `Wait` - アクティビティは以前と同様に動作します。
- `Set Variable` - アクティビティは以前と同様に動作します。
- `Filter` - アクティビティは以前と同様に動作します。
- `Until` アクティビティは、式を評価し、条件が満たされるまでループします。 再実行ルールに基づいて、内側のアクティビティはスキップされる場合があります。
- `Foreach` アクティビティは、受け取った項目に対して常にループします。 再実行ルールに基づいて、内側のアクティビティはスキップされる場合があります。
- `If and switch` - 条件は常に評価されます。 再実行ルールに基づいて、内側のアクティビティはスキップされる場合があります。
- `Execute pipeline activity` - 子パイプラインがトリガーされますが、再実行ルールに基づいて、子パイプライン内のすべてのアクティビティが依然としてスキップされる場合があります。


以前に実行されたパイプラインを最初から再実行するには、特定のパイプラインの実行にマウス ポインターを合わせ、 **[再実行]** を選択します。 複数のパイプラインを選択した場合、 **[再実行]** ボタンを使用して、それらをすべて実行できます。

:::image type="content" source="media/monitor-visually/rerun-pipeline.png" alt-text="パイプラインの再実行":::

特定のポイントから再実行を開始する場合は、アクティビティの実行ビューから行うことができます。 開始するアクティビティを選択し、 **[アクティビティから再実行する]** を選択します。 

:::image type="content" source="media/monitor-visually/rerun-activity.png" alt-text="アクティビティの実行の再実行":::

### <a name="rerun-from-failed-activity"></a>失敗したアクティビティから再実行する

アクティビティが失敗した場合、タイムアウトした場合、または取り消された場合、 **[Rerun from failed activity]\(失敗したアクティビティから再実行する\)** を選択して、失敗したアクティビティからパイプラインを再実行できます。

:::image type="content" source="media/monitor-visually/rerun-failed-activity.png" alt-text="失敗したアクティビティを再実行する":::

### <a name="view-rerun-history"></a>再実行履歴を表示する

パイプラインの全実行の再実行履歴は、リスト ビューで確認することができます。

:::image type="content" source="media/monitor-visually/rerun-history-1.png" alt-text="履歴を表示する":::

特定のパイプラインの実行について、再実行履歴を確認することもできます。

:::image type="content" source="media/monitor-visually/view-rerun-history.png" alt-text="パイプラインの実行履歴の表示":::

## <a name="monitor-consumption"></a>消費量の監視

実行の横にある使用量アイコンをクリックすると、パイプラインの実行によって消費されているリソースを確認できます。 

:::image type="content" source="media/monitor-visually/monitor-consumption-1.png" alt-text="パイプラインによって使用されているリソースを確認できる場所を示すスクリーンショット。":::

このアイコンをクリックすると、そのパイプラインの実行によって使用されているリソースの消費量レポートが開きます。 

:::image type="content" source="media/monitor-visually/monitor-consumption-2.png" alt-text="消費量の監視":::

これらの値を [Azure 料金計算ツール](https://azure.microsoft.com/pricing/details/data-factory/)に組み込んで、パイプラインの実行のコストを見積もることができます。 Azure Data Factory 料金の詳細については、[価格に関するページ](pricing-concepts.md)を参照してください。

> [!NOTE]
> 料金計算ツールによって返される値は、推定値です。 Azure Data Factory で課金される正確な金額は反映されません。 

## <a name="gantt-views"></a>ガント ビュー

ガント チャートは、ある時間の範囲の実行履歴を表示できるビューです。 ガント ビューに切り替えると、名前でグループ化されたすべてのパイプライン実行が、実行にかかった時間に相対的なバーとして表示されます。 また、パイプラインに作成した注釈やタグでグループ化することもできます。 ガント ビューは、アクティビティの実行レベルでも使用できます。

:::image type="content" source="media/monitor-visually/select-gantt.png" alt-text="ガント チャートの例":::

バーの長さはパイプラインの期間を示します。 バーを選択して詳細を表示することもできます。

:::image type="content" source="media/monitor-visually/view-gantt-run.png" alt-text="ガント チャートの期間":::

## <a name="alerts"></a>警告

Data Factory で、サポートされるメトリックに対してアラートを生成できます。 Data Factory の監視ページで **[監視]**  >  **[Alerts & Metrics]\(アラートとメトリック\)** の順に選択して開始します。

:::image type="content" source="media/monitor-visually/start-page.png" alt-text="Data Factory の監視ページ":::

この機能の概要とデモンストレーションについては、以下の 7 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>アラートを作成する

1.  **[新しいアラート ルール]** を選択して新しいアラートを作成します。

    :::image type="content" source="media/monitor-visually/new-alerts.png" alt-text="[新しいアラート ルール] ボタン":::

1.  ルール名を指定し、アラートの重大度を選択します。

    :::image type="content" source="media/monitor-visually/name-and-severity.png" alt-text="ルール名と重要度のボックス":::

1.  アラートの条件を選択します。

    :::image type="content" source="media/monitor-visually/add-criteria-1.png" alt-text="対象の条件のボックス":::

    :::image type="content" source="media/monitor-visually/add-criteria-2.png" alt-text="アラートの条件を設定するためにメトリックを 1 つ選択する場所を示すスクリーンショット。":::

    :::image type="content" source="media/monitor-visually/add-criteria-3.png" alt-text="条件の一覧":::

    アラートは、さまざまなメトリックに対して作成できます。これには、ADF エンティティの数またはサイズ、アクティビティ、パイプライン、またはトリガーの実行、Integration Runtime (IR) CPU の使用率、メモリ、ノード数、またはキューに加えて、SSIS パッケージ実行および SSIS IR の開始または停止操作などのメトリックが含まれます。

1.  アラート ロジックを構成します。 すべてのパイプラインと対応するアクティビティについて、選択したメトリックのアラートを作成できます。 また、特定のアクティビティの種類、アクティビティ名、パイプライン名、またはエラーの種類を選択することもできます。

    :::image type="content" source="media/monitor-visually/alert-logic.png" alt-text="アラート ロジックの構成のオプション":::

1.  アラートに対して、メール、SMS、プッシュ、および音声での通知を構成します。 アラート通知用にアクション グループを作成するか、既存のものを選択します。

    :::image type="content" source="media/monitor-visually/configure-notification-1.png" alt-text="通知の構成のオプション":::

    :::image type="content" source="media/monitor-visually/configure-notification-2.png" alt-text="通知の追加のオプション":::

1.  アラート ルールを作成します。

    :::image type="content" source="media/monitor-visually/create-alert-rule.png" alt-text="アラート ルールの作成のオプション":::

## <a name="next-steps"></a>次のステップ

パイプラインの監視と管理の詳細については、[プログラムでのパイプラインの監視と管理](./monitor-programmatically.md)に関する記事をご覧ください。
