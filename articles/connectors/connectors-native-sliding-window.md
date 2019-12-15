---
title: 連続したデータを処理するタスクをスケジュールする
description: Azure Logic Apps のスライディング ウィンドウを使用して、連続したデータを処理する定期的なタスクを作成および実行する
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786913"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Azure Logic Apps のスライディング ウィンドウ トリガーを使用して、連続したデータに対するタスクをスケジュール設定して実行する

連続したチャンクでデータを処理する必要があるタスク、プロセス、またはジョブを定期的に実行するには、**スライディング ウィンドウ** トリガーを使用してロジック アプリ ワークフローを開始します。 ワークフローを開始するための日付と時刻やタイム ゾーン、およびそのワークフローを反復処理するための繰り返しを設定できます。 何らかの理由で繰り返しが行われなかった場合、このトリガーによってその行われなかった繰り返しが処理されます。 たとえば、データベースとバックアップ ストレージ間でデータを同期するときは、スライディング ウィンドウ トリガーを使用して、ギャップを起こすことなくデータが同期されるようにします。 組み込みのスケジュール トリガーとアクションの詳細については、[Azure Logic Apps を使用した定期的な自動タスク、ワークフローのスケジュールと実行](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)に関するページを参照してください。

このトリガーがサポートするパターンの一部を次に示します。

* すぐに実行して、*n* の秒数、分数、または時間数ごとに繰り返します。

* 特定の日時に開始してから、*n* の秒数、分数、または時間数ごとに実行して繰り返します。 このトリガーを使用すると、過去の開始時刻を指定し、過去のすべての繰り返しを実行することができます。

* 実行する前に、各繰り返しを特定の期間だけ遅らせます。

このトリガーと繰り返しトリガーの違い、または定期的なワークフローのスケジュールの詳細については、「[Schedule and run recurring automated tasks, processes, and workflows with Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)」(Azure Logic Apps を使用した定期的な自動タスク、プロセス、ワークフローのスケジュールと実行) を参照してください。

> [!TIP]
> ロジック アプリをトリガーし、今後 1 回だけ実行する場合は、[ジョブを 1 回だけ実行する方法](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)できます。

* [ロジック アプリ](../logic-apps/logic-apps-overview.md)に関する基本的な知識。 ロジック アプリを初めて使用する場合は、[初めてのロジック アプリを作成する方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="add-sliding-window-trigger"></a>スライディング ウィンドウ トリガーを追加する

1. [Azure Portal](https://portal.azure.com) にサインインします。 空のロジック アプリを作成します。

1. ロジック アプリ デザイナーが表示されたら、検索ボックスにフィルターとして「スライディング ウィンドウ」と入力します。 トリガー一覧から、ロジック アプリ ワークフローの最初の手順としてこのトリガーを選択します。**スライディング ウィンドウ**

   ![[スライディング ウィンドウ] トリガーを選択します。](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. 繰り返しの間隔と頻度を設定します。 この例では、これらのプロパティを設定して、ワークフローを毎週実行します。

   ![間隔と頻度の設定](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | プロパティ | 必須 | JSON での名前 | 種類 | 説明 |
   |----------|----------|-----------|------|-------------|
   | **間隔** | はい | interval | 整数 | ワークフローの実行間隔を、[頻度] に指定された単位に基づいて表す正の整数。 間隔の最小値と最大値は次のとおりです。 <p>- Hour: 1 から 12,000 時間 </br>- Minute: 1 から 72,000 分 </br>- Second: 1 から 9,999,999 秒<p>たとえば間隔が 6 で、頻度が "Hour" である場合は、繰り返しは 6 時間ごとになります。 |
   | **頻度** | はい | frequency | string | 繰り返しの時間の単位: **[秒]** 、 **[分]** 、または **[時間]** |
   ||||||

   ![高度な繰り返しのオプション](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   その他の繰り返しのオプションについては、 **[新しいパラメーターの追加]** 一覧を開きます。 
   選択したオプションはすべて、選択後にトリガーに表示されます。

   | プロパティ | 必須 | JSON での名前 | 種類 | 説明 |
   |----------|----------|-----------|------|-------------|
   | **遅延** | いいえ | delay | string | [ISO 8601 の日時指定](https://en.wikipedia.org/wiki/ISO_8601#Durations)を使用して各繰り返しを遅らせる期間 |
   | **タイム ゾーン** | いいえ | timeZone | string | 開始時刻を指定したときに限り適用されます。このトリガーに [UTC オフセット](https://en.wikipedia.org/wiki/UTC_offset)を指定することはできないためです。 適用するタイム ゾーンを選択してください。 |
   | **開始時刻** | いいえ | startTime | string | 開始日時を次の形式で指定します。 <p>YYYY-MM-DDThh:mm:ss (タイム ゾーンを選択した場合) <p>または <p>YYYY-MM-DDThh:mm:ssZ (タイム ゾーンを選択しなかった場合) <p>たとえば、2017 年 9 月 18 日午後 2:00 にする場合は、"2017-09-18T14:00:00" と指定し、太平洋標準時などのタイム ゾーンを選択します。 または、タイム ゾーンなしで「2017-09-18T14:00:00Z」と指定します。 <p>**注:** この開始時刻は、[UTC の日付と時刻の形式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) (ただし、[UTC オフセット](https://en.wikipedia.org/wiki/UTC_offset)を除く) で [日付と時刻に関する ISO 8601 規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)に従っている必要があります。 タイム ゾーンを選択しない場合は、末尾にスペースを入れず、アルファベットの "Z" を追加してください。 この "Z" は、同等の[航海時間](https://en.wikipedia.org/wiki/Nautical_time)を表します。 <p>単純なスケジュールでは、開始時刻と最初の実行時刻が一致するのに対して、高度な繰り返しでは、トリガーが作動するのは開始時刻以降となります。 "[*開始日時の使用方法を具体的に教えてください*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)" |
   |||||

1. 次は、他のアクションを使用して残りのワークフローを構築します。 追加できるアクションの詳細については、「[Azure Logic Apps のコネクタ](../connectors/apis-list.md)」を参照してください。

## <a name="workflow-definition---sliding-window"></a>ワークフローの定義 - スライディング ウィンドウ

JSON を使用するロジック アプリケーションの基となるワークフロー定義では、選択したオプションを使用してスライディング ウィンドウ トリガーの定義を表示できます。 この定義を表示するには、デザイナーのツールバーの **[コード ビュー]** を選択します。 デザイナーに戻るには、デザイナーのツールバーの **[デザイナー]** を選択します。

この例は、スライディング ウィンドウ トリガーの定義が基となるワークフロー定義でどのように見えるかを示しています。ここで、各繰り返しの遅延は、1 時間の繰り返しに対して 5 秒です。

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>次の手順

* [ワークフロー内の次のアクションの遅延](../connectors/connectors-native-delay.md)
* [Logic Apps のコネクタ](../connectors/apis-list.md)