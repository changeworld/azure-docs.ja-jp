---
title: Azure Marketplace から Azure Data Factory Analytics ソリューションをインストールする
description: Azure Marketplace から Azure Data Factory Analytics ソリューションをインストールする方法について説明します。
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 07e60aef3e6e3a968d22f76351d00cb722e99f78
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850415"
---
# <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Azure Marketplace から Azure Data Factory Analytics ソリューションをインストールする

このソリューションからは、データ ファクトリの全体的な正常性の概要が提供されます。また、詳細を確認したり、予期しない動作パターンのトラブルシューティングを実行したりするためのオプションも備わっています。 すぐに利用できる豊富なビュー機能を活用することで、次の主要な処理に関する分析情報を得ることができます。

* データ ファクトリのパイプライン、アクティビティ、トリガー実行に関する概要
* データ ファクトリのアクティビティ実行を種類別に詳しく表示する機能
* データ ファクトリのトップ パイプライン アクティビティ エラーの概要

1. **Azure Marketplace** にアクセスして **[分析]** フィルターを選択し、**Azure Data Factory Analytics (プレビュー)** を検索します。

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image3.png" alt-text="Azure Marketplace にアクセスして [分析] フィルターを選択し、Azure Data Factory Analytics (プレビュー) を選択する場面を示すスクリーンショット。":::

1. **Azure Data Factory Analytics (プレビュー)** の詳細を確認します。

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image4.png" alt-text="Azure Data Factory Analytics (プレビュー) の詳細を示すスクリーンショット。":::

1. **[作成]** を選択し、 **[Log Analytics ワークスペース]** を作成または選択します。

   :::image type="content" source="media/data-factory-monitor-oms/monitor-log-analytics-image-5.png" alt-text="新しいソリューションの作成を示すスクリーンショット。":::

## <a name="monitor-data-factory-metrics"></a>Data Factory メトリックを監視する

このソリューションをインストールすると、選択した Log Analytics ワークスペースの [ブック] セクションに既定のセットのビューが作成されます。 その結果、次のメトリックが有効になります。

* データの実行 - 1) データ ファクトリによるパイプラインの実行
* ADF の実行 - 2) データ ファクトリによるアクティビティの実行
* ADF の実行 - 3) データ ファクトリによるトリガーの実行
* ADF のエラー - 1) データ ファクトリによるパイプラインのエラーの上位 10 件
* ADF のエラー - 2) データ ファクトリによるアクティビティの実行エラーの上位 10 件
* ADF のエラー - 3) データ ファクトリによるトリガーのエラーの上位 10 件
* ADF の統計 - 1) 種類別のアクティビティの実行
* ADF の統計 - 2) 種類別のトリガーの実行
* ADF の統計 - 3) パイプラインの実行の最長期間

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image6.png" alt-text="Workbooks (プレビュー) と AzureDataFactoryAnalytics が強調表示されているウィンドウを示すスクリーンショット。":::

前述のメトリックの視覚化、これらのメトリックの背後にあるクエリの確認、クエリの編集、アラートの作成、およびその他のアクションを実行できます。

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image8.png" alt-text="データ ファクトリによるパイプライン実行のグラフィック表示のスクリーンショット。":::

> [!NOTE]
> Azure Data Factory Analytics (プレビュー) では、診断ログが "_リソース固有_" の宛先テーブルに送られます。 次のテーブルに対するクエリを作成できます: _ADFPipelineRun_、_ADFTriggerRun_、_ADFActivityRun_。

## <a name="next-steps"></a>次のステップ

[プログラムでのパイプラインの監視と管理](monitor-programmatically.md)
