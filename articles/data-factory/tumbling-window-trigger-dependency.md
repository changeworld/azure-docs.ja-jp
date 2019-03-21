---
title: Azure Data Factory でのタンブリング ウィンドウ トリガーの依存関係の作成 | Microsoft Docs
description: Azure Data Factory でタンブリング ウィンドウ トリガーの依存関係を作成する方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: f3a547f5b953262d7263d1be0897161cf4091a1d
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57574391"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>タンブリング ウィンドウ トリガーの依存関係の作成

この記事では、タンブリング ウィンドウ トリガーの依存関係を作成する手順について説明します。 タンブリング ウィンドウ トリガーに関する全般な情報については、[タンブリング ウィンドウ トリガーを作成する方法](how-to-create-tumbling-window-trigger.md)に関するページを参照してください。

依存関係チェーンを構築し、データ ファクトリ内の別のトリガーの実行が成功した後でのみトリガーが実行されるようにするには、この高度な機能を使用して、タンブリング ウィンドウの依存関係を作成します。

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Data Factory UI で依存関係を作成する

トリガーの依存関係を作成するには、**[トリガー]、[詳細設定]、[新規]** の順に選択した後、適切なオフセットとサイズに依存するトリガーを選択します。 **[完了]** を選択して、依存関係に対するデータ ファクトリの変更を発行して有効にします。

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png)

## <a name="tumbling-window-dependency-properties"></a>タンブリング ウィンドウの依存関係プロパティ

タンブリング ウィンドウ トリガーの依存関係には、次のプロパティがあります。

```json
{
    "name": "DemoTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00.000Z",
            "delay": "00:00:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": "-02:00:00",
                    "offset": "-01:00:00",
                    "referenceTrigger": {
                        "referenceName": "DemoDependency1",
                        "type": "TriggerReference"
                    }
                }
            ]
        }
    }
}
```

次の表に、タンブリング ウィンドウの依存関係を定義するために必要な属性の一覧を示します。

| **プロパティ名** | **説明**  | **Type** | **必須** |
|---|---|---|---|
| トリガー  | 既存のすべてのタンブリング ウィンドウ トリガーがこのドロップダウンに表示されます。 依存関係を設定するトリガーを選択します。  | TumblingWindowTrigger | はい |
| offset | 依存関係トリガーのオフセット。 時間範囲形式で値を指定します。正と負の両方のオフセットを指定できます。 このパラメーターは、トリガーが自己に依存する場合は必須であり、それ以外の場合は省略可能です。 自己依存関係は、常に負のオフセットにする必要があります。 | Timespan | 自己依存関係:はい その他:いいえ  |
| ウィンドウ サイズ | 依存関係のタンブリング ウィンドウのサイズ。 時間範囲形式で値を指定します。 このパラメーターは省略可能です。 | Timespan | いいえ   |
|||||

## <a name="tumbling-window-self-dependency-properties"></a>タンブリング ウィンドウの自己依存関係プロパティ

前のウィンドウが正常に完了するまではトリガーが次のウィンドウに進まないシナリオでは、自己依存関係を構築します。 自己依存関係トリガーには、次のプロパティがあります。

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```

タンブリング ウィンドウの依存関係プロパティのシナリオと使用法をイラストで次に示します。

## <a name="dependency-offset"></a>依存関係のオフセット

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png)

## <a name="dependency-size"></a>依存関係のサイズ

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png)

## <a name="self-dependency"></a>自己依存関係

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png)

## <a name="usage-scenarios"></a>使用シナリオ

### <a name="dependency-on-another-tumbling-window-trigger"></a>別のタンブリング ウィンドウ トリガーに対する依存関係

たとえば、過去 7 日間の出力を集計する別の日次ジョブに依存する日次テレメトリ処理ジョブで、7 日分のローリング ウィンドウ ストリームを生成するとします。

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png)

### <a name="dependency-on-itself"></a>自己に対する依存関係

ジョブの出力ストリームにずれがない日次ジョブ:

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png)

## <a name="monitor-dependencies"></a>依存関係を監視する

[トリガーの実行] 監視ページで、依存関係チェーンと該当するウィンドウを監視できます。 **[監視]、[トリガーの実行]** の順に移動します。

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png)

選択したウィンドウに依存するすべてのトリガーの実行を表示するには、眼鏡をクリックします。

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png)

## <a name="next-steps"></a>次の手順

[タンブリング ウィンドウ トリガーの作成方法](how-to-create-tumbling-window-trigger.md)を確認します。