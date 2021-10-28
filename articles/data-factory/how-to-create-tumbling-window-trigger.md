---
title: タンブリング ウィンドウ トリガーを作成する
titleSuffix: Azure Data Factory & Azure Synapse
description: タンブリング ウィンドウでパイプラインを実行するトリガーを Azure Data Factory または Azure Synapse Analytics で作成する方法について説明します。
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 6df2a24ca029f14a4743b28641b8ac4c2b88e851
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223786"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>タンブリング ウィンドウでパイプラインを実行するトリガーの作成

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、タンブリング ウィンドウ トリガーを作成、起動、および監視する手順について説明します。 トリガーとサポートされる種類の全般的な情報については、[パイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)に関する記事をご覧ください。

タンブリング ウィンドウ トリガーは、状態を維持しながら、指定した開始時刻から定期的に実行される種類のトリガーです。 タンブリング ウィンドウとは、固定サイズで重複しない一連の連続する時間間隔です。 タンブリング ウィンドウ トリガーはパイプラインと 1 対 1 の関係を持ち、単一のパイプラインのみを参照できます。 タンブリング ウィンドウ トリガーはスケジュール トリガーの代替として適しており、複雑なシナリオ ([他のタンブリング ウィンドウ トリガーに対する依存関係](#tumbling-window-trigger-dependency)、[失敗したジョブの再実行](tumbling-window-trigger-dependency.md#monitor-dependencies)、および[パイプラインに対するユーザー再試行の設定](#user-assigned-retries-of-pipelines)) のための一連の機能を提供します。 スケジュール トリガーとタンブリング ウィンドウ トリガーの違いについて詳しく理解するには、[こちら](concepts-pipeline-execution-triggers.md#trigger-type-comparison)をご覧ください。

## <a name="azure-data-factory-and-synapse-portal-experience"></a>Azure Data Factory と Synapse のポータル エクスペリエンス

1. Azure portal でタンブリング ウィンドウ トリガーを作成するには、**[トリガー]** タブを選択し、**[新規]** を選択します。 
1. [トリガーの構成] ウィンドウが開いたら、 **[タンブリング ウィンドウ]** を選択し、タンブリング ウィンドウ トリガーのプロパティを定義します。 
1. 終了したら、 **[保存]** を選択します。

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
:::image type="content" source="media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png" alt-text="タンブリング ウィンドウ トリガーを Azure Portal で作成する":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
:::image type="content" source="media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger-synapse.png" alt-text="タンブリング ウィンドウ トリガーを Azure Portal で作成する":::

---

## <a name="tumbling-window-trigger-type-properties"></a>タンブリング ウィンドウのトリガーの種類のプロパティ

タンブリング ウィンドウには、次のトリガーの種類のプロパティがあります。

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "parameter1": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter2": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
            }
        }
    }
}
```

次の表に、タンブリング ウィンドウ トリガーの繰り返しとスケジュール設定に関連する主な JSON 要素の概要を示します。

| JSON 要素 | 説明 | Type | 使用できる値 | 必須 |
|:--- |:--- |:--- |:--- |:--- |
| **type** | トリガーの種類。 種類は固定値の "TumblingWindowTrigger" です。 | String | "TumblingWindowTrigger" | はい |
| **runtimeState** | トリガー実行時の現在の状態。<br/>**注**:この要素は \<readOnly> です。 | String | "Started"、"Stopped"、"Disabled" | はい |
| **frequency** | トリガーが繰り返される頻度の単位 (分または時間) を表す文字列。 **startTime** の日付値が **frequency** 値よりも細かい場合、ウィンドウの境界を計算するときに **startTime** の日付が考慮されます。 たとえば、**frequency** 値が時間単位で、**startTime** 値が 2017-09-01T10:10:10Z の場合、最初のウィンドウは (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z) になります。 | String | "minute"、"hour"  | はい |
| **interval** | トリガーの実行頻度を決定する、**frequency** 値の間隔を示す正の整数。 たとえば、**interval** が 3 で **frequency** が "hour" の場合、トリガーは 3 時間ごとに繰り返されます。 <br/>**注**:最小ウィンドウ間隔は 5 分です。 | Integer | 正の整数。 | はい |
| **startTime**| 最初の発生。これは過去の場合があります。 最初のトリガー間隔は、(**startTime**、**startTime** + **interval**) になります。 | DateTime | DateTime 値。 | はい |
| **endTime**| 最後の発生。これは過去の場合があります。 | DateTime | DateTime 値。 | はい |
| **delay** | ウィンドウのデータ処理の開始の遅延時間。 パイプライン実行は、予想される実行時間 + **delay** の時間が経過してから開始されます。 **delay** は、トリガーが期限を過ぎてから新しい実行をトリガーするまでの待機時間を定義します。 **delay** によってウィンドウの **startTime** が変更されるわけではありません。 たとえば、**delay** 値が 00:10:00 の場合、10 分の遅延を意味します。 | Timespan<br/>(hh:mm:ss)  | 期間の値。既定値は 00:00:00 です。 | いいえ |
| **maxConcurrency** | 準備ができているウィンドウに対して発生する同時トリガー実行の数。 たとえば、前日の実行を 1 時間ごとにバックフィルすると、24 ウィンドウになります。 **maxConcurrency** = 10 の場合、トリガー イベントは最初の 10 ウィンドウ (00:00-01:00 - 09:00-10:00) に対してのみ発生します。 最初の 10 回がトリガーされたパイプライン実行が完了すると、次の 10 ウィンドウ (10:00-11:00 - 19:00-20:00) に対してトリガー実行が発生します。 **maxConcurrency** = 10 のこの例を続けると、準備ができているウィンドウが 10 個ある場合、パイプライン実行は合計 10 回になります。 準備ができているウィンドウが 1 つしかない場合、パイプライン実行は 1 回だけになります。 | Integer | 1 ～ 50 の整数。 | はい |
| **retryPolicy:Count** | パイプライン実行前の再試行回数は "Failed" とマークされます。  | Integer | 整数。既定値は 0 (再試行なし) です。 | いいえ |
| **retryPolicy: intervalInSeconds** | 秒単位で指定された再試行の間の遅延。 | Integer | 秒数。既定値は 30 です。 | いいえ |
| **dependsOn: type** | TumblingWindowTriggerReference の種類。 依存関係が設定されている場合は必須です。 | String |  "TumblingWindowTriggerDependencyReference"、"SelfDependencyTumblingWindowTriggerReference" | いいえ |
| **dependsOn: size** | 依存関係のタンブリング ウィンドウのサイズ。 | Timespan<br/>(hh:mm:ss)  | 正の timespan 値。既定値は子トリガーのウィンド ウサイズです。  | いいえ |
| **dependsOn: offset** | 依存関係トリガーのオフセット。 | Timespan<br/>(hh:mm:ss) |  自己依存関係内の負の値を指定する必要がある timespan 値。 値が指定されていない場合、ウィンドウはトリガーそのものと同じになります。 | 自己依存関係:はい<br/>その他:いいえ  |

> [!NOTE]
> タンブリング ウィンドウ トリガーの発行後に **interval** と **frequency** を編集することはできません。

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart および WindowEnd システム変数

**パイプライン** 定義 (つまり、クエリの一部) で、タンブリング ウィンドウ トリガーの **WindowStart** および **WindowEnd** システム変数を使用できます。 システム変数は、**トリガー** 定義でパイプラインにパラメーターとして渡します。 次の例は、これらの変数をパラメーターとして渡す方法を示しています。

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "MyWindowStart": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "MyWindowEnd": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                }
            }
        }
    }
}
```

パイプライン定義で **WindowStart** および **WindowEnd** システム変数値を使用するには、"MyWindowStart" パラメーターと "MyWindowEnd" パラメーターを適宜使用します。

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>バックフィル シナリオでのウィンドウの実行順序

トリガーの startTime が過去の場合、M=(CurrentTime- TriggerStartTime)/TumblingWindowSize という数式に基づき、トリガーはトリガーのコンカレンシーを考慮して、{M} 回のバックフィル (過去分) の実行を並行して生成してから、今後の実行を行います。 ウィンドウの実行順序は、最も古い間隔から最も新しい間隔までの間で決定論的となります。 現時点では、この動作を変更することはできません。

### <a name="existing-triggerresource-elements"></a>既存の TriggerResource 要素

既存の **TriggerResource** 要素の更新には、次の点が適用されます。

* トリガーが作成されると、トリガーの **頻度** 要素 (またはウィンドウ サイズ) と **間隔** 要素の値を変更することはできません。 このことは triggerRun の再実行と依存関係の評価を適切に機能させるために必要です
* トリガーの **endTime** 要素の値が変更 (追加または更新) されても、既に処理されたウィンドウの状態はリセット *されません*。 トリガーは新しい **endTime** 値に従います。 新しい **endTime** 値が既に実行されたウィンドウよりも前の場合、トリガーは停止します。 それ以外の場合は、新しい **endTime** 値に達すると、トリガーは停止します。

### <a name="user-assigned-retries-of-pipelines"></a>ユーザー割り当てのパイプラインの再試行

パイプライン エラーが発生した場合、タンブリング ウィンドウ トリガーは、ユーザーの介入なしに、同じ入力パラメーターを使用して、参照されたパイプラインの実行を自動的に再試行することができます。 これは、トリガー定義の "retryPolicy" プロパティを使用して指定できます。

### <a name="tumbling-window-trigger-dependency"></a>タンブリング ウィンドウ トリガーの依存関係

データ ファクトリ内の別のタンブリング ウィンドウ トリガーの実行が成功した後でのみ、タンブリング ウィンドウ トリガーが実行されることを確認する場合は、[タンブリング ウィンドウ トリガーの依存関係を作成](tumbling-window-trigger-dependency.md)します。

### <a name="cancel-tumbling-window-run"></a>タンブリング ウィンドウの実行のキャンセル

タンブリング ウィンドウ トリガーの実行をキャンセルできるのは、特定のウィンドウが _待機中_、_依存関係の待機中_、または _実行中_ 状態である場合です

* ウィンドウが **実行中** 状態である場合は、関連付けられている _パイプラインの実行_ をキャンセルすると、その後のトリガーの実行が _キャンセル済み_ とマークされます
* ウィンドウが **待機中** または **依存関係の待機中** 状態の場合は、ウィンドウの監視をキャンセルできます。

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="media/how-to-create-tumbling-window-trigger/cancel-tumbling-window-trigger.png" alt-text="[監視] ページからタンブリング ウィンドウ トリガーをキャンセルする":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

:::image type="content" source="media/how-to-create-tumbling-window-trigger/cancel-tumbling-window-trigger-synapse.png" alt-text="[監視] ページからタンブリング ウィンドウ トリガーをキャンセルする":::

---

キャンセルされたウィンドウを再実行することもできます。 再実行すると _最新の_ パブリッシュされたトリガーの定義が取得され、指定したウィンドウの依存関係が再実行時に _再評価されます_

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="media/how-to-create-tumbling-window-trigger/rerun-tumbling-window-trigger.png" alt-text="以前にキャンセルされた実行に対してタンブリング ウィンドウ トリガーを再実行する":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

:::image type="content" source="media/how-to-create-tumbling-window-trigger/rerun-tumbling-window-trigger-synapse.png" alt-text="以前にキャンセルされた実行に対してタンブリング ウィンドウ トリガーを再実行する":::

---

## <a name="sample-for-azure-powershell-and-azure-cli"></a>Azure PowerShell と Azure CLI のサンプル

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

ここでは、Azure PowerShell を使用してトリガーを作成、起動、および監視する方法について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。 

- **Azure PowerShell**。 「[PowerShellGet を使用した Windows への Azure PowerShell のインストール](/powershell/azure/install-az-ps)」に記載されている手順に従います。 

- **Azure Data Factory**。 「[PowerShell を使用した Azure Data Factory の作成](./quickstart-create-data-factory-powershell.md)」に記載されている手順に従って、データ ファクトリとパイプラインを作成します。

### <a name="sample-code"></a>サンプル コード

1. 次の内容が含まれた **MyTrigger.json** という名前の JSON ファイルを C:\ADFv2QuickStartPSH\ フォルダーに作成します。

    > [!IMPORTANT]
    > JSON ファイルを保存する前に、**startTime** 要素の値を現在の UTC 時間に設定します。 **endTime** 要素の値を現在の UTC 時間の 1 時間後に設定します。

    ```json
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "endTime" : "2017-09-08T06:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```

2. [Set-AzDataFactoryV2Trigger](/powershell/module/az.datafactory/set-azdatafactoryv2trigger) コマンドレットを使用してトリガーを作成します。

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

3. [Get-AzDataFactoryV2Trigger](/powershell/module/az.datafactory/get-azdatafactoryv2trigger) コマンドレットを使用して、トリガーの状態が **Stopped** であることを確認します。

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. [Start-AzDataFactoryV2Trigger](/powershell/module/az.datafactory/start-azdatafactoryv2trigger) コマンドレットを使用してトリガーを起動します。

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. [Get-AzDataFactoryV2Trigger](/powershell/module/az.datafactory/get-azdatafactoryv2trigger) コマンドレットを使用して、トリガーの状態が **Started** であることを確認します。

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. [Get-AzDataFactoryV2TriggerRun](/powershell/module/az.datafactory/get-azdatafactoryv2triggerrun) コマンドレットを使用して、Azure PowerShell でトリガー実行を取得します。 トリガー実行に関する情報を取得するには、次のコマンドを定期的に実行します。 トリガー定義の値に合わせて、**TriggerRunStartedAfter** と **TriggerRunStartedBefore** の値を更新します。

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

ここでは、Azure CLI を使用してトリガーを作成、起動、および監視する方法について説明します。

### <a name="prerequisites"></a>前提条件

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 「[Azure CLI を使用して Azure Data Factory を作成する](./quickstart-create-data-factory-azure-cli.md)」に記載されている手順に従って、データ ファクトリとパイプラインを作成します。

### <a name="sample-code"></a>サンプル コード

1. 作業ディレクトリで、トリガーのプロパティを使用して、**MyTrigger.json** という名前の JSON ファイルを作成します。 このサンプルでは、次のコンテンツを使用します。

    > [!IMPORTANT]
    > JSON ファイルを保存する前に、**referenceName** の値をパイプライン名に設定します。 **startTime** 要素の値を現在の UTC 時間に設定します。 **endTime** 要素の値を現在の UTC 時間の 1 時間後に設定します。

    ```json
    {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-12-08T00:00:00Z",
          "endTime": "2017-12-08T01:00:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
    }
    ```

2. [az datafactory trigger create](/cli/azure/datafactory/trigger#az_datafactory_trigger_create) コマンドを使用してトリガーを作成します。

    > [!IMPORTANT]
    > この手順とそれ以降のすべての手順で、`ResourceGroupName` をリソース グループ名に置き換えます。 `DataFactoryName` は、データ ファクトリ名に置き換えます。

    ```azurecli
    az datafactory trigger create --resource-group "ResourceGroupName" --factory-name "DataFactoryName"  --name "MyTrigger" --properties @MyTrigger.json  
    ```

3. [az datafactory trigger show](/cli/azure/datafactory/trigger#az_datafactory_trigger_show) コマンドを使用して、トリガーの状態が **Stopped** であることを確認します。

    ```azurecli
    az datafactory trigger show --resource-group "ResourceGroupName" --factory-name "DataFactoryName" --name "MyTrigger" 
    ```

4. [az datafactory trigger start](/cli/azure/datafactory/trigger#az_datafactory_trigger_start) コマンドを使用してトリガーを起動します。

    ```azurecli
    az datafactory trigger start --resource-group "ResourceGroupName" --factory-name "DataFactoryName" --name "MyTrigger" 
    ```

5. [az datafactory trigger show](/cli/azure/datafactory/trigger#az_datafactory_trigger_show) コマンドを使用して、トリガーの状態が **Started** であることを確認します。

    ```azurecli
    az datafactory trigger show --resource-group "ResourceGroupName" --factory-name "DataFactoryName" --name "MyTrigger" 
    ```

6. [az datafactory trigger-run query-by-factory](/cli/azure/datafactory/trigger-run#az_datafactory_trigger_run_query_by_factory) コマンドを使用して、Azure CLI でトリガー実行を取得します。 トリガー実行に関する情報を取得するには、次のコマンドを定期的に実行します。 トリガー定義の値に合わせて、**last-updated-after** と **last-updated-before** の値を更新します。

    ```azurecli
    az datafactory trigger-run query-by-factory --resource-group "ResourceGroupName" --factory-name "DataFactoryName" --filters operand="TriggerName" operator="Equals" values="MyTrigger" --last-updated-after "2017-12-08T00:00:00Z" --last-updated-before "2017-12-08T01:00:00Z"
    ```
---

Azure Portal でトリガー実行とパイプライン実行を監視するには、[パイプライン実行の監視](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)に関するセクションをご覧ください。

## <a name="next-steps"></a>次のステップ

* トリガーについて詳しくは、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md#trigger-execution)」をご覧ください。
* [タンブリング ウィンドウ トリガーの依存関係を作成](tumbling-window-trigger-dependency.md)します。
* パイプラインでトリガー メタデータを参照する方法については、[パイプライン実行でのトリガー メタデータの参照](how-to-use-trigger-parameterization.md)に関する記事を参照してください