---
title: 診断設定とワークスペースを構成する
description: Azure Data Factory を監視するように診断設定と Log Analytics ワークスペースを構成する方法について説明します。
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 5673dd881d96dc14922e8a9f9bedc0501d2f5d18
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837852"
---
# <a name="configure-diagnostic-settings-and-workspace"></a>診断設定とワークスペースを構成する

データ ファクトリに診断設定を作成または追加します。

1. ポータルで Monitor に移動します。 **[設定]**  >  **[診断設定]** の順に選択します。

1. 診断設定の対象となるデータ ファクトリを選択します。

1. 選択したデータ ファクトリの設定が存在しない場合は、設定を作成するように求められます。 **[診断を有効にする]** を選択します。

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image1.png" alt-text="設定が存在しない場合に診断設定を作成する":::

   データ ファクトリに既存の設定が存在する場合は、データ ファクトリで構成済みの設定の一覧が表示されます。 **[診断設定の追加]** を選択します。

   :::image type="content" source="media/data-factory-monitor-oms/add-diagnostic-setting.png" alt-text="設定が存在する場合に診断設定を追加する":::

1. 設定に名前を付け、 **[Log Analytics への送信]** を選択して、 **[Log Analytics ワークスペース]** からワークスペースを選択します。

    * _Azure Diagnostics_ モードでは、診断ログは _AzureDiagnostics_ テーブルに送られます。

    * _Resource-Specific_ モードでは、Azure Data Factory からの診断ログは以下のテーブルに送られます。
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      お客様のワークロードに関連するさまざまなログを選択して、Log Analytics テーブルに送信できます。 たとえば、SQL Server Integration Services (SSIS) をまったく使用しない場合は、SSIS ログを選択する必要はありません。 SSIS Integration Runtime (IR) の開始、停止、またはメンテナンス操作をログに記録する場合は、SSIS IR ログを選択できます。 SQL Server Management Studio (SSMS)、SQL Server エージェント、またはその他の指定されたツールで T-SQL を使用して SSIS パッケージ実行を呼び出す場合は、SSIS パッケージ ログを選択できます。 SSIS パッケージの実行を ADF パイプラインの SSIS パッケージ実行アクティビティ経由で呼び出す場合は、すべてのログを選択できます。

    * _AllMetrics_ を選択すると、監視したりアラートを生成したりするために、ADF アクティビティ、パイプライン、およびトリガーの実行のメトリックに加え、SSIS IR 操作と SSIS パッケージ実行のメトリックを含むさまざまな ADF メトリックを使用できるようになります。

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="設定に名前を付けてログ分析ワークスペースを選択する":::

    > [!NOTE]
    > Azure ログ テーブルには 500 個を超える列を含めることができないため、"_リソース固有モード_" を選択することを **強くお勧めします**。 詳細については、[Azure Diagnostics のログ リファレンス](/azure/azure-monitor/reference/tables/azurediagnostics)に関するページを参照してください。

1. **[保存]** を選択します。

しばらくすると、このデータ ファクトリに対する設定の一覧に新しい設定が表示されます。 新しいイベント データが生成されるとすぐに、診断ログがそのワークスペースにストリーミングされます。 イベントが生成されてから、Log Analytics に表示されるまでに最大 15 分かかかる場合があります。

## <a name="next-steps"></a>次の手順

[Azure Monitor REST API を使用して診断ログを設定する](monitor-logs-rest.md)
