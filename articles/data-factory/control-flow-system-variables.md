---
title: システム変数
titleSuffix: Azure Data Factory & Azure Synapse
description: この記事では、Azure Data Factory と Azure Synapse Analytics でサポートされているシステム変数について説明します。 これらの変数は、いずれかのサービス内でエンティティを定義するときに式で使用できます。
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: a272e5c7f4d467dadfe618f6d70b9a36225c48ff
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130175828"
---
# <a name="system-variables-supported-by-azure-data-factory-and-azure-synapse-analytics"></a>Azure Data Factory と Azure Synapse Analytics でサポートされているシステム変数

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse でサポートされているシステム変数について説明します。 これらの変数は、いずれかのサービス内でエンティティを定義するときに式で使用できます。

## <a name="pipeline-scope"></a>パイプラインのスコープ

これらのシステム変数は、パイプライン JSON 内の任意の場所で参照できます。

| 変数名 | 説明 |
| --- | --- |
| @pipeline().DataFactory |パイプライン実行が実行されているデータまたは Synapse ワークスペースの名前 |
| @pipeline().Pipeline |パイプラインの名前 |
| @pipeline().RunId |特定のパイプライン実行の ID |
| @pipeline().TriggerType |パイプラインを呼び出したトリガーの種類 (例: `ScheduleTrigger`、`BlobEventsTrigger`)。 サポートされているトリガーの種類の一覧については、「[パイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)」を参照してください。 `Manual` のトリガーの種類は、パイプラインが手動でトリガーされたことを示します。 |
| @pipeline（）.TriggerId|パイプラインを呼び出したトリガーの ID |
| @pipeline（）.TriggerName|パイプラインを呼び出したトリガーの名前 |
| @pipeline（）.TriggerTime|パイプラインを呼び出したトリガーの実行時刻。 これは、トリガーがパイプライン実行を呼び出すために **実際に** 起動した時刻であり、トリガーのスケジュールされた時刻とはやや異なる場合があります。  |
| @pipeline().GroupId | パイプラインの実行が属するグループの ID。 |
| @pipeline()?TriggeredByPipelineName | パイプラインの実行をトリガーするパイプラインの名前。 ExecutePipeline アクティビティによってパイプラインの実行がトリガーされる場合に関係します。 その他の状況で使用された場合は、_Null_ に評価されます。 @pipeline() の後の疑問符に注意してください。 |
| @pipeline()?TriggeredByPipelineRunId | パイプラインの実行をトリガーするパイプラインの実行 ID。 ExecutePipeline アクティビティによってパイプラインの実行がトリガーされる場合に関係します。 その他の状況で使用された場合は、_Null_ に評価されます。 @pipeline() の後の疑問符に注意してください。 |

>[!NOTE]
>トリガー関連の日付/時刻のシステム変数 (パイプラインとトリガーの両方のスコープ) は、ISO 8601 形式で UTC 日付を返します (例: `2017-06-01T22:20:00.4061448Z`)。

## <a name="schedule-trigger-scope"></a>スケジュール トリガーのスコープ

トリガーの種類が [ScheduleTrigger](concepts-pipeline-execution-triggers.md#schedule-trigger) の場合、これらのシステム変数は、トリガー JSON 内の任意の場所で参照できます。

| 変数名 | 説明 |
| --- | --- |
| @trigger().scheduledTime |トリガーがパイプライン実行を呼び出すようにスケジュールされた時間。 |
| @trigger().startTime |トリガーがパイプライン実行を呼び出すために **実際に** 起動した時間。 これは、トリガーのスケジュールされた時刻とはやや異なる場合があります。 |

## <a name="tumbling-window-trigger-scope"></a>タンブリング ウィンドウ トリガーのスコープ

トリガーの種類が [TumblingWindowTrigger](concepts-pipeline-execution-triggers.md#tumbling-window-trigger) の場合、これらのシステム変数は、トリガー JSON 内の任意の場所で参照できます。

| 変数名 | 説明 |
| --- | --- |
| @trigger().outputs.windowStartTime |トリガーの実行に関連付けられているウィンドウの開始。 |
| @trigger().outputs.windowEndTime |トリガーの実行に関連付けられているウィンドウの終了。 |
| @trigger().scheduledTime |トリガーがパイプライン実行を呼び出すようにスケジュールされた時間。 |
| @trigger().startTime |トリガーがパイプライン実行を呼び出すために **実際に** 起動した時間。 これは、トリガーのスケジュールされた時刻とはやや異なる場合があります。 |

## <a name="storage-event-trigger-scope"></a>ストレージ イベント トリガーのスコープ

トリガーの種類が [BlobEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger) の場合、これらのシステム変数は、トリガー JSON 内の任意の場所で参照できます。

| 変数名 | 説明 |
| --- | --- |
| @triggerBody().fileName  |作成または削除によってトリガーが起動されたファイルの名前。   |
| @triggerBody().folderPath  |`@triggerBody().fileName` で指定されたファイルを含むフォルダーへのパス。 フォルダー パスの最初のセグメントは、Azure Blob Storage コンテナーの名前です。  |
| @trigger().startTime |パイプライン実行を呼び出すためにトリガーが起動した時刻。 |

   > [!NOTE]
   > [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) でパイプラインとトリガーを作成する場合は、パラメーターとして `@trigger().outputs.body.fileName` と `@trigger().outputs.body.folderPath` を使用する必要があります。 これら 2 つのプロパティによって、BLOB 情報がキャプチャされます。 `@triggerBody().fileName` と `@triggerBody().folderPath` の代わりに、これらのプロパティを使用してください。

## <a name="custom-event-trigger-scope"></a>カスタム イベント トリガーのスコープ

トリガーの種類が [CustomEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger) の場合、これらのシステム変数は、トリガー JSON 内の任意の場所で参照できます。

>[!NOTE]
>サービスでは、[Azure Event Grid イベント スキーマ](../event-grid/event-schema.md)を使用してカスタム イベントを書式設定する必要があります。

| 変数名 | 説明
| --- | --- |
| @triggerBody().event.eventType | カスタム イベント トリガーの実行をトリガーしたイベントの種類。 イベントの種類は、顧客が定義したフィールドで、文字列型の任意の値を取ります。 |
| @triggerBody().event.subject | トリガーを起動するカスタム イベントの件名。 |
| @triggerBody().event.data._keyName_ | カスタム イベントのデータ フィールドは、JSON BLOB から無料で利用でき、顧客がメッセージとデータを送信するために使用できます。 各フィールドを参照するには、data._keyName_ を使用します。 たとえば、@triggerBody().event.data.callback は、 _[データ]_ の下に格納されている _[コールバック]_ フィールドの値を返します。 |
| @trigger().startTime | パイプライン実行を呼び出すためにトリガーが起動した時刻。 |

## <a name="next-steps"></a>次のステップ

* これらの変数を式で使用する方法については、[式言語と関数](control-flow-expression-language-functions.md)に関するページを参照してください。
* パイプラインでトリガー スコープ システム変数を使用するには、[パイプライン実行でのトリガー メタデータの参照](how-to-use-trigger-parameterization.md)に関する記事を参照してください。
