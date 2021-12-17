---
title: Data Factory のメトリックとアラート
description: Azure Data Factory の監視に使用できるメトリックについて説明します。
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: becbd026a45a6eedf7cbb9162335f8d6d47de4dd
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849294"
---
# <a name="data-factory-metrics-and-alerts"></a>Data Factory のメトリックとアラート

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory では、サービスの監視を可能にする、以下のメトリックとアラートが提供されています。

## <a name="data-factory-metrics"></a>Data Factory のメトリック

Azure Monitor を使用すると、Azure ワークロードのパフォーマンスと正常性を可視化できます。 最も重要な種類の Monitor データは、パフォーマンス カウンターとも呼ばれるメトリックです。 メトリックは、ほとんどの Azure リソースによって出力されます。 Monitor には、監視およびトラブルシューティングのために、これらのメトリックを構成して使用する方法が複数用意されています。

Azure Data Factory バージョン 2 で出力されるメトリックの一部を次に示します。

| **メトリック**                           | **メトリックの表示名**                  | **単位** | **集計の種類** | **説明**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | キャンセルされたアクティビティの実行回数のメトリック           | Count    | 合計                | 1 分の枠内でキャンセルされたアクティビティの実行の合計数。 |
| ActivityFailedRuns                   | 失敗したアクティビティ実行回数のメトリック             | Count    | 合計                | 1 分の枠内で失敗したアクティビティの実行の合計数。 |
| ActivitySucceededRuns                | 成功したアクティビティ実行回数のメトリック          | Count    | 合計                | 1 分の枠内で成功したアクティビティの実行の合計数。 |
| PipelineCancelledRuns                 | キャンセルされたパイプライン実行回数のメトリック           | Count    | 合計                | 1 分の枠内でキャンセルされたパイプライン実行の合計数。 |
| PipelineFailedRuns                   | 失敗したパイプライン実行回数のメトリック             | Count    | 合計                | 1 分の枠内で失敗したパイプライン実行の合計数。 |
| PipelineSucceededRuns                | 成功したパイプライン実行回数のメトリック          | Count    | 合計                | 1 分の枠内で成功したパイプライン実行の合計数。 |
| TriggerCancelledRuns                  | キャンセルされたトリガー実行回数のメトリック            | Count    | 合計                | 1 分の枠内でキャンセルされたトリガー実行の合計数。 |
| TriggerFailedRuns                    | 失敗したトリガー実行の回数メトリック              | Count    | 合計                | 1 分の枠内で失敗したトリガー実行の合計数。 |
| TriggerSucceededRuns                 | 成功したトリガー実行の回数メトリック           | Count    | 合計                | 1 分の枠内で成功したトリガー実行の合計数。 |
| SSISIntegrationRuntimeStartCancelled  | 取り消された SSIS 統合ランタイム開始メトリック           | カウント    | 合計                | 1 分の枠内で取り消された SSIS 統合ランタイム開始の合計数。 |
| SSISIntegrationRuntimeStartFailed    | 失敗した SSIS 統合ランタイム開始メトリック             | Count    | 合計                | 1 分の枠内で失敗した SSIS 統合ランタイム開始の合計数。 |
| SSISIntegrationRuntimeStartSucceeded | 成功した SSIS 統合ランタイム開始メトリック          | Count    | 合計                | 1 分の枠内で成功した SSIS 統合ランタイム開始の合計数。 |
| SSISIntegrationRuntimeStopStuck      | 停止した SSIS 統合ランタイム停止メトリック               | Count    | 合計                | 1 分の枠内で停止した SSIS 統合ランタイム停止の合計数。 |
| SSISIntegrationRuntimeStopSucceeded  | 成功した SSIS 統合ランタイム停止メトリック           | Count    | 合計                | 1 分の枠内で成功した SSIS 統合ランタイム停止の合計数。 |
| SSISPackageExecutionCancelled         | キャンセルされた SSIS パッケージ実行回数のメトリック  | Count    | 合計                | 1 分の枠内でキャンセルされた SSIS パッケージ実行の合計数。 |
| SSISPackageExecutionFailed           | 失敗した SSIS パッケージ実行回数のメトリック    | Count    | 合計                | 1 分の枠内で失敗した SSIS パッケージ実行の合計数。 |
| SSISPackageExecutionSucceeded        | 成功した SSIS パッケージ実行回数のメトリック | Count    | 合計                | 1 分の枠内で成功した SSIS パッケージ実行の合計数。 |
| PipelineElapsedTimeRuns | 経過時間パイプライン実行メトリック | Count | 合計 | 1 分の枠内で、ユーザー定義の予想される持続時間を超えてパイプラインが実行された回数 [(詳細情報)。](tutorial-operationalize-pipelines.md) |

メトリックにアクセスするには、「[Azure Monitor データ プラットフォーム](../azure-monitor/data-platform.md)」に記載された手順に従います。

> [!NOTE]
> _PipelineElapsedTimeRuns_ の例外を除き、完了済みのトリガーされたアクティビティとパイプラインの実行からのイベントのみが出力されます。 進行中およびデバッグの実行は出力 "*されません*"。 その一方で、呼び出しメソッドに関係なく、*すべての* SSIS パッケージ実行からのイベントが出力されます。これには、呼び出し方法に関係なく、完了済みのものと進行中のものが含まれます。 たとえば、SQL Server Management Studio、SQL Server エージェント、またはその他の指定されたツールで T-SQL を使用して、Data Factory パイプラインでの Execute SSIS Package アクティビティのトリガーされたまたはデバッグの実行として、Azure 対応 SQL Server Data Tools のパッケージ実行を呼び出すことができます。

## <a name="data-factory-alerts"></a>Data Factory のアラート

Azure portal にサインインし、 **[モニター]**  >  **[アラート]** の順に選択してアラートを作成します。

:::image type="content" source="media/monitor-using-azure-monitor/alerts_image3.png" alt-text="ポータル メニューのアラートを示すスクリーンショット。":::

### <a name="create-alerts"></a>アラートを作成する

1. **[+ 新しいアラート ルール]** を選択して新しいアラートを作成します。

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image4.png" alt-text="新しいアラート ルールの作成を示すスクリーンショット。":::

1. アラートの条件を定義します。

    > [!NOTE]
    > **[リソースの種類でフィルター]** ドロップダウン リストでは **[すべて]** を選択するようにしてください。

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image5.png" alt-text="リソースを選択するペインを開くための選択項目を示すスクリーンショット。":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image6.png" alt-text="シグナル ロジックを構成するペインを開くための選択項目を示すスクリーンショット。":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image7.png" alt-text="シグナル ロジックの構成を示すスクリーンショット。":::

1. アラートの詳細を定義します。

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image8.png" alt-text="アラートの詳細を示すスクリーンショット。":::

1. アクション グループを定義します。

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image9.png" alt-text="[新しいアクション グループ] が強調表示された、ルールの作成を示すスクリーンショット。":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image10.png" alt-text="新しいアプリ グループの作成を示すスクリーンショット。":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image11.png" alt-text="メール、SMS、プッシュ、音声の構成を示すスクリーンショット。":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image12.png" alt-text="アクション グループの定義を示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

[診断設定とワークスペースを構成する](monitor-configure-diagnostics.md)
