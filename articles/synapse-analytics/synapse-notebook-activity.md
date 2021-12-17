---
title: Synapse Notebook を実行してデータを変換する
description: この記事では、Synapse ノートブック アクティビティと Synapse パイプラインを作成して開発する方法について説明します。
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 05/19/2021
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: c8c2246822cdf9faa06833020cd1951bde9e86a3
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123449299"
---
# <a name="transform-data-by-running-a-synapse-notebook"></a>Synapse Notebook を実行してデータを変換する

[!INCLUDE[appliesto-adf-xxx-md](../data-factory/includes/appliesto-xxx-asa-md.md)]

[Synapse パイプライン](../data-factory/concepts-pipelines-activities.md)の Azure Synapse ノートブック アクティビティは、Azure Synapse ワークスペースの Synapse ノートブックを実行します。 この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、 [データ変換アクティビティ](../data-factory/transform-data.md) に関する記事に基づいています。 

## <a name="create-a-synapse-notebook-activity"></a>Synapse ノートブック アクティビティを作成する

Synapse ノートブック アクティビティは、Synapse パイプライン キャンバスまたはノートブック エディターから直接作成できます。 Synapse ノートブック アクティビティは、Synapse ノートブックで選択された Spark プールで実行されます。 

### <a name="add-a-synapse-notebook-activity-from-pipeline-canvas"></a>パイプライン キャンバスから Synapse ノートブック アクティビティを追加する

**[アクティビティ]** の下にある **Synapse ノートブック** を Synapse パイプライン キャンバスにドラッグ アンド ドロップします。 Synapse ノートブック アクティビティ ボックス上を選択し、 **[設定]** で現在のアクティビティのノートブック コンテンツを構成します。 現在のワークスペースから既存のノートブックを選択することも、新しいものを追加することもできます。 

![ノートブック アクティビティの作成を示すスクリーンショット](./media/synapse-notebook-activity/create-synapse-notebook-activity.png)

> [!NOTE]
> Azure Synapse パイプラインにおける並列 Spark Notebooks の実行はキューに格納されて FIFO 方式で実行されます。キューにおけるジョブの順序は時系列に基づきます。また、キューに格納されるジョブの有効期限は 3 日です。ノートブックのキューは Synapse パイプラインでしか機能しないことに注意してください。


### <a name="add-a-notebook-to-synapse-pipeline"></a>Synapse パイプラインにノートブックを追加する

右上隅にある **[パイプラインへの追加]** ボタンを選択して、ノートブックを既存のパイプラインに追加するか、新しいパイプラインを作成します。

![パイプラインへのノートブックの追加を示すスクリーンショット](./media/synapse-notebook-activity/add-to-pipeline.png)

## <a name="passing-parameters"></a>パラメーターを渡す

### <a name="designate-a-parameters-cell"></a>パラメーター セルを指定する

# <a name="classic-notebook"></a>[クラシック ノートブック](#tab/classical)

ノートブックをパラメーター化するには、省略記号 (...) を選択して、右端にある別のセル アクション メニューにアクセスします。 次に、 **[パラメーター セルを切り替えます]** を選択して、セルをパラメーター セルとして指定します。

[![パラメーター切り換えを示すスクリーンショット](./media/synapse-notebook-activity/toggle-parameter-cell.png)](./media/synapse-notebook-activity/toggle-parameter-cell.png#lightbox)

# <a name="preview-notebook"></a>[プレビュー版ノートブック](#tab/preview)

ノートブックをパラメーター化するには、省略記号 (...) を選択して、セル ツールバーの **[more commands]\(その他のコマンド\)** にアクセスします。 次に、 **[パラメーター セルを切り替えます]** を選択して、セルをパラメーター セルとして指定します。

[![Azure ノートブックのパラメーター切り換えを示すスクリーンショット](./media/synapse-notebook-activity/azure-notebook-toggle-parameter-cell.png)](./media/synapse-notebook-activity/azure-notebook-toggle-parameter-cell.png#lightbox)

---

Azure Data Factory によりパラメーター セルが検索され、その値が実行時に渡されるパラメーターの既定値として使用されます。 実行エンジンにより、既定値を上書きするために、入力パラメーターを含んだ新しいセルがパラメーター セルの下に追加されます。 


### <a name="assign-parameters-values-from-a-pipeline"></a>パイプラインからパラメーター値を割り当てる

パラメーター付きのノートブックを作成したら、Synapse ノートブック アクティビティを使用して、パイプラインから実行できます。 アクティビティをパイプライン キャンバスに追加した後、 **[設定]** タブの **[Base parameters]\(基本パラメーター\)** セクションで、パラメーターの値を設定できます。 

[![パラメーターの割り当てを示すスクリーンショット](./media/synapse-notebook-activity/assign-parameter.png)](./media/synapse-notebook-activity/assign-parameter.png#lightbox)

パラメーター値を割り当てるときは、[パイプライン式言語](../data-factory/control-flow-expression-language-functions.md) または [システム変数](../data-factory/control-flow-system-variables.md)を使用できます。


## <a name="read-synapse-notebook-cell-output-value"></a>Synapse ノートブックのセル出力値を読み取る

下の手順に従って、次のアクティビティ内のノートブック セル出力値を読み取ることができます。
1. Synapse ノートブック アクティビティで [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook) API を呼び出して、アクティビティの出力に表示する値を返します。次に例を示します。  

    ```python
    mssparkutils.notebook.exit("hello world") 
    ```
    
    ノートブックのコンテンツを保存してパイプラインを再トリガーすると、手順 2 の次に続くアクティビティで使用できる exitValue がノートブック アクティビティの出力に含まれます。 

2.  ノートブック アクティビティの出力から exitValue プロパティを読み取ります。 ノートブック アクティビティの出力から取り込まれた exitValue が「hello world」と等しいかどうかを確認するために使用されるサンプル式を次に示します。 

    [![exitValue の読み取りを示すスクリーンショット](./media/synapse-notebook-activity/synapse-read-exit-value.png)](./media/synapse-notebook-activity/synapse-read-exit-value.png#lightbox)


## <a name="run-another-synapse-notebook"></a>別の Synapse ノートブックを実行する 

[%run マジック](./spark/apache-spark-development-using-notebooks.md#notebook-reference)または [mssparkutils ノートブック ユーティリティ](./spark/microsoft-spark-utilities.md#notebook-utilities)を呼び出して、Synapse ノートブック アクティビティ内の他のノートブックを参照できます。 どちらも、入れ子になっている関数呼び出しをサポートしています。 シナリオに基づいて考慮する必要があるこれら 2 つの方法の主な違いは次のとおりです。

- [%run マジック](./spark/apache-spark-development-using-notebooks.md#notebook-reference)は、参照されているノートブックのすべてのセルを %run セルにコピーし、変数コンテキストを共有します。 notebook1 が `%run notebook2` を使用して notebook2 を参照し、notebook2 が [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook) 関数を呼び出すとき、notebook1 のセルの実行は停止されます。 ノートブック ファイルを "含める" 必要がある場合は、%run マジックを使用することをお勧めします。
- [mssparkutils ノートブック ユーティリティ](./spark/microsoft-spark-utilities.md#notebook-utilities)は、参照されているノートブックをメソッドまたは関数として呼び出します。 変数コンテキストは共有されません。 notebook1 が `mssparkutils.notebook.run("notebook2")` を使用して notebook2 を参照し、notebook2 が [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook) 関数を呼び出すとき、notebook1 のセルの実行は続行されます。 ノートブックを "インポート" する必要がある場合は、mssparkutils ノートブック ユーティリティを使用することをお勧めします。

>[!Note]
> Synapse パイプラインからの別の Synapse ノートブックの実行は、プレビュー版が有効になっているノートブックでのみ機能します。


## <a name="see-notebook-activity-run-history"></a>ノートブック アクティビティの実行履歴を表示する

**[監視]** タブで **[パイプラインの実行]** にアクセスすると、トリガーされたパイプラインが表示されます。 ノートブック アクティビティを含むパイプラインを開いて、実行履歴を表示します。 

**[open notebook]\(ノートブックを開く\)** ボタンを選択すると、セルの入力と出力の両方を含む最新のノートブック実行スナップショットを表示できます。 
![ノートブック アクティビティ履歴の表示](./media/synapse-notebook-activity/input-output-open-notebook.png)


ノートブック アクティビティの入力または出力を表示するには、 **[入力]** または **[出力]** ボタンを選択します。 ユーザー エラーでパイプラインが失敗した場合は、 **[出力]** を選択して **[result]** フィールドを調べて、ユーザー エラーの詳細なトレースバックを確認します。

![出力のユーザー エラーを示すスクリーンショット](./media/synapse-notebook-activity/notebook-output-user-error.png)


## <a name="synapse-notebook-activity-definition"></a>Synapse ノートブック アクティビティの定義

Synapse ノートブック アクティビティのサンプル JSON 定義を次に示します。

```json
{
    "name": "parameter_test",
    "type": "SynapseNotebook",
    "dependsOn": [],
    "policy": {
        "timeout": "7.00:00:00",
        "retry": 0,
        "retryIntervalInSeconds": 30,
        "secureOutput": false,
        "secureInput": false
    },
    "userProperties": [],
    "typeProperties": {
        "notebook": {
            "referenceName": "parameter_test",
            "type": "NotebookReference"
        },
        "parameters": {
            "input": {
                "value": {
                    "value": "@pipeline().parameters.input",
                    "type": "Expression"
                }
            }
        }
    }
}

```


## <a name="synapse-notebook-activity-output"></a>Synapse ノートブック アクティビティの出力

Synapse ノートブック アクティビティの出力のサンプル JSON を次に示します。

```json

{
    "status": {
        "Status": 1,
        "Output": {
            "status": <livySessionInfo>
            },
            "result": {
                "runId": "<GUID>",
                "runStatus": "Succeed",
                "message": "Notebook execution is in Succeeded state",
                "lastCheckedOn": "2021-03-23T00:40:10.6033333Z",
                "errors": {
                    "ename": "",
                    "evalue": ""
                },
                "sessionId": 4,
                "sparkpool": "sparkpool",
                "snapshotUrl": "https://myworkspace.dev.azuresynapse.net/notebooksnapshot/{guid}",
                "exitCode": "abc" // return value from user notebook via mssparkutils.notebook.exit("abc")
            }
        },
        "Error": null,
        "ExecutionDetails": {}
    },

    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US 2)",
    "executionDuration": 234,
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    },
    "billingReference": {
        "activityType": "ExternalActivity",
        "billableDuration": [
            {
                "meterType": "AzureIR",
                "duration": 0.06666666666666667,
                "unit": "Hours"
            }
        ]
    }
}

```
