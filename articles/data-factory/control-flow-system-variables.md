---
title: Azure Data Factory のシステム変数
description: この記事では、Azure Data Factory でサポートされているシステム変数について説明します。 Data Factory エンティティを定義するときに、式でこれらの変数を使用できます。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: b85efa7ac4481ab9eb2b2637aee7d9e5e76e8f3f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786058"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Azure Data Factory でサポートされているシステム変数

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory でサポートされているシステム変数について説明します。 Data Factory エンティティを定義するときに、式でこれらの変数を使用できます。

## <a name="pipeline-scope"></a>パイプラインのスコープ

これらのシステム変数は、パイプライン JSON 内の任意の場所で参照できます。

| 変数名 | 説明 |
| --- | --- |
| @pipeline().DataFactory |パイプライン実行が実行されているデータ ファクトリの名前 |
| @pipeline().Pipeline |パイプラインの名前 |
| @pipeline().RunId |特定のパイプライン実行の ID |
| @pipeline().TriggerType |パイプラインを呼び出したトリガーの種類 (例: `ScheduleTrigger`、`BlobEventsTrigger`)。 サポートされているトリガーの種類の一覧については、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)」を参照してください。 `Manual` のトリガーの種類は、パイプラインが手動でトリガーされたことを示します。 |
| @pipeline（）.TriggerId|パイプラインを呼び出したトリガーの ID |
| @pipeline（）.TriggerName|パイプラインを呼び出したトリガーの名前 |
| @pipeline（）.TriggerTime|パイプラインを呼び出したトリガーの実行時刻。 これは、トリガーがパイプライン実行を呼び出すために **実際に** 起動した時刻であり、トリガーのスケジュールされた時刻とはやや異なる場合があります。  |

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
| @triggerBody().folderName  |`@triggerBody().fileName` で指定されたファイルを含むフォルダーへのパス。 フォルダー パスの最初のセグメントは、Azure Blob Storage コンテナーの名前です。  |
| @trigger().startTime |パイプライン実行を呼び出すためにトリガーが起動した時刻。 |

## <a name="custom-event-trigger-scope"></a>カスタム イベント トリガーのスコープ

トリガーの種類が [CustomEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger) の場合、これらのシステム変数は、トリガー JSON 内の任意の場所で参照できます。

>[!NOTE]
>Azure Data Factory では、 [Azure Event Grid イベント スキーマ](../event-grid/event-schema.md)を使用してカスタム イベントを書式設定する必要があります。

| 変数名 | 説明
| --- | --- |
| @triggerBody().event.eventType | カスタム イベント トリガーの実行をトリガーしたイベントの種類。 イベントの種類は、顧客が定義したフィールドで、文字列型の任意の値を取ります。 |
| @triggerBody().event.subject | トリガーを起動するカスタム イベントの件名。 |
| @triggerBody().event.data._keyName_ | カスタム イベントのデータ フィールドは、JSON BLOB から無料で利用でき、顧客がメッセージとデータを送信するために使用できます。 各フィールドを参照するには、data._keyName_ を使用します。 たとえば、@triggerBody().event.data.callback は、 _[データ]_ の下に格納されている _[コールバック]_ フィールドの値を返します。 |
| @trigger().startTime | パイプライン実行を呼び出すためにトリガーが起動した時刻。 |

## <a name="next-steps"></a>次のステップ

* これらの変数を式で使用する方法については、[式言語と関数](control-flow-expression-language-functions.md)に関するページを参照してください。
* パイプラインでトリガー スコープ システム変数を使用するには、[パイプライン実行でのトリガー メタデータの参照](how-to-use-trigger-parameterization.md)に関する記事を参照してください。
