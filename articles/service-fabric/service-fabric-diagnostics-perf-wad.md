---
title: "Azure Service Fabric - Windows Azure 診断拡張機能を使用したパフォーマンスの監視 | Microsoft Docs"
description: "Windows Azure 診断を使用して、Azure Service Fabric クラスターのパフォーマンス カウンターを収集します。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/05/2017
ms.author: dekapur
ms.openlocfilehash: f71c483eba201eae91c15b84a2ed42fcb68ae575
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2017
---
# <a name="performance-monitoring-with-windows-azure-diagnostics-extension"></a>Windows Azure 診断拡張機能を使用したパフォーマンスの監視

このドキュメントでは、Windows Azure 診断 (WAD) 拡張機能を使用して Windows クラスター用のパフォーマンス カウンターの収集を設定するために必要な手順について説明します。 Linux クラスターの場合は、[OMS エージェント](service-fabric-diagnostics-oms-agent.md)を設定して、ノードのパフォーマンス カウンターを収集します。 

 > [!NOTE]
> これらの手順が機能するように、WAD 拡張機能をクラスターにデプロイする必要があります。 設定されていない場合は、「[Windows Azure 診断を使用したイベントの集計と収集](service-fabric-diagnostics-event-aggregation-wad.md)」を参照して、「*診断拡張機能のデプロイ*」の手順に従ってください。

## <a name="collect-performance-counters-via-the-wadcfg"></a>WadCfg を介してパフォーマンス カウンターを収集する

WAD を介してパフォーマンス カウンターを収集するには、クラスターの Resource Manager テンプレートで構成を適切に変更する必要があります。 次の手順に従って、収集するパフォーマンス カウンターをテンプレートに追加し、Resource Manager リソースのアップグレードを実行します。

1. クラスターのテンプレートで WAD の構成を検索します (`WadCfg` を検索)。 収集するパフォーマンス カウンターは、`DiagnosticMonitorConfiguration` の下に追加します。

2. `DiagnosticMonitorConfiguration` に次のセクションを追加することにより、パフォーマンス カウンターを収集するように構成を設定します。 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    `scheduledTransferPeriod` は、収集されたカウンターの値が Azure Storage テーブルと構成された任意のシンクに転送される頻度を定義します。 

3. 収集するパフォーマンス カウンターを、前の手順で宣言した `PerformanceCounterConfiguration` に追加します。 収集する各カウンターは、`counterSpecifier`、`sampleRate`、`unit`、`annotation`、および関連する任意の `sinks` を使用して定義されます。 "*合計プロセッサ時間*" (CPU が処理操作に使用された時間の合計) のパフォーマンス カウンターの例を次に示します。

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
            {
                "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                "sampleRate": "PT1M",
                "unit": "Percent",
                "annotation": [
                ],
                "sinks": ""
            }
        ]
    }
    ```

    カウンターのサンプル レートは、ニーズに応じて変更できます。 形式は `PT<time><unit>` です。カウンターを 1 秒ごとに収集する場合は、`"sampleRate": "PT15S"` と設定します。

    同様に、`PerformanceCounterConfiguration` のリストに追加することで、他の複数のパフォーマンス カウンターを収集できます。 `*` を使用して、名前が類似したパフォーマンス カウンターのグループを指定することができますが、シンクを経由して (Application Insights に) カウンターを送信する際は、それぞれが個別に宣言されている必要があります。 

4. 収集が必要なパフォーマンス カウンターを適切に追加したら、クラスター リソースをアップグレードして、これらの変更を実行中のクラスターに反映させる必要があります。 変更した `template.json` を保存して、PowerShell を開きます。 `New-AzureRmResourceGroupDeployment` を使用すると、クラスターをアップグレードできます。 呼び出しは、リソース グループ、更新したテンプレート ファイル、パラメーター ファイルの名前を必要とし、更新したリソースに適切な変更を加えるよう求めます。 自分のアカウントでサインインし、適切なサブスクリプションがあれば、次のコマンドを使用してアップグレードを実行します。

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. アップグレードのロールアウトが完了したら (15 ～ 45 分かかります)、WAD はパフォーマンス カウンターを収集し、`WadCfg` で宣言されたストレージ アカウント内のテーブルに送信します。

## <a name="next-steps"></a>次のステップ
* `WadCfg` に Application Insights シンクを追加することにより、Application Insights のパフォーマンス カウンターを確認します。 「[Application Insights を使用したイベント分析と視覚化](service-fabric-diagnostics-event-analysis-appinsights.md)」の "*Resource Manager テンプレートに AI シンクを追加する*" セクションを参照して、Application Insights シンクを構成します。
* クラスターのその他のパフォーマンス カウンターを収集します。 収集が必要なカウンターの一覧については、「[パフォーマンス メトリック](service-fabric-diagnostics-event-generation-perf.md)」を参照してください。
* [Windows VM と Azure Resource Manager テンプレートで監視と診断を利用](../virtual-machines/windows/extensions-diagnostics-template.md)して、`WadCfg` にさらに変更を加えます (診断データを送信する追加のストレージ アカウントの構成など)。