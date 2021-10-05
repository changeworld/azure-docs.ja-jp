---
title: Azure Marketplace から Azure Data Factory Analytics ソリューションをインストールする
description: Azure Marketplace から Azure Data Factory Analytics ソリューションをインストールする方法について説明します。
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 4ae7015c575dc10c9f8c2ec2b896e75e1a2a2a91
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837757"
---
# <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Azure Marketplace から Azure Data Factory Analytics ソリューションをインストールする

このソリューションを使用すると、Data Factory の全体的な正常性の概要を確認できます。また、詳細を確認したり、予期しない動作パターンのトラブルシューティングを実行したりするためのオプションも備わっています。 すぐに利用できる豊富なビュー機能を活用することで、次の主要な処理に関する分析情報を得ることができます。

* データ ファクトリ パイプライン、アクティビティ、トリガーの実行に関する概要
* データ ファクトリ アクティビティの実行を種類別に詳しく表示する機能
* データ ファクトリのトップ パイプライン、アクティビティ エラーの概要

1. **Azure Marketplace** にアクセスして、 **[分析]** フィルターを選択し、**Azure Data Factory Analytics (プレビュー)** を検索します。

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image3.png" alt-text="&quot;Azure Marketplace&quot; にアクセスして、「&quot;Analytics filter&quot;」と入力し、&quot;[Azure Data Factory Analytics (プレビュー)]&quot; を選択する":::

1. **Azure Data Factory Analytics (プレビュー)** の詳細

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image4.png" alt-text="&quot;Azure Data Factory Analytics (プレビュー)&quot; の詳細":::

1. **[作成]** を選択し、**Log Analytics ワークスペース** を作成または選択します。

   :::image type="content" source="media/data-factory-monitor-oms/monitor-log-analytics-image-5.png" alt-text="新しいソリューションの作成":::

## <a name="monitor-data-factory-metrics"></a>Data Factory メトリックを監視する

このソリューションをインストールすると、選択した Log Analytics ワークスペースの [ブック] セクションに既定のセットのビューが作成されます。 その結果、次のメトリックが有効になります。

* ADF の実行 - 1) Data Factory によるパイプラインの実行
* ADF の実行 - 2) Data Factory によるアクティビティの実行
* ADF の実行 - 3) Data Factory によるトリガーの実行
* ADF のエラー - 1) Data Factory によるパイプラインのエラーの上位 10 件
* ADF のエラー - 2) Data Factory によるアクティビティの実行エラーの上位 10 件
* ADF のエラー - 3) Data Factory によるトリガーのエラーの上位 10 件
* ADF の統計 - 1) 種類別のアクティビティの実行
* ADF の統計 - 2) 種類別のトリガーの実行
* ADF の統計 - 3) パイプラインの実行の最長期間

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image6.png" alt-text="&quot;Workbooks (プレビュー)&quot; と &quot;AzureDataFactoryAnalytics&quot; が強調表示されているウィンドウ":::

前述のメトリックの視覚化、これらのメトリックの背後にあるクエリの確認、クエリの編集、アラートの作成、およびその他のアクションを実行できます。

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image8.png" alt-text="データ ファクトリによるパイプライン実行のグラフィック表示":::

> [!NOTE]
> Azure Data Factory Analytics (プレビュー) では、診断ログが _リソース固有_ の宛先テーブルに送られます。 次のテーブルに対するクエリを作成できます: _ADFPipelineRun_、_ADFTriggerRun_、_ADFActivityRun_。

## <a name="next-steps"></a>次の手順

[プログラムでのパイプラインの監視と管理](monitor-programmatically.md)
