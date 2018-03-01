---
title: "Azure Event Hubs で Azure Logic Apps 用イベント監視を設定する | Microsoft Docs"
description: "データ ストリームを監視し、Azure Event Hubs を使用してロジック アプリのイベントを送受信します"
services: logic-apps
keywords: "データ ストリーム, イベント モニター, イベント ハブ"
author: ecfan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 076f7dd11ca8c153046727861ecb755e88f32b01
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>Event Hubs コネクタでのイベントの監視および送受信

ロジック アプリがイベントを検出および送受信できるようにイベント モニターを設定するには、ロジック アプリから [Azure Event Hub](https://azure.microsoft.com/services/event-hubs) に接続します。 [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) に関するページと [Logic Apps コネクタの価格設定](../logic-apps/logic-apps-pricing.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

Event Hubs コネクタを使用するには、以下のものが必要です。

* [Azure Event Hubs 名前空間とイベント ハブ](../event-hubs/event-hubs-create.md)
* [ロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-event-hubs"></a>Azure Event Hubs に接続する

ロジック アプリがすべてのサービスにアクセスできるようにするには、ロジック アプリとサービスの間に "[*接続*](./connectors-overview.md)" を作成する必要があります (まだ作成していない場合)。 この接続により、ロジック アプリによるデータ アクセスが承認されます。 ロジック アプリがイベント ハブにアクセスできるようにするには、アクセス許可を確認し、Event Hubs 名前空間の接続文字列を取得します。

1.  [Azure Portal](https://portal.azure.com "Azure Portal") にサインインします。 

2.  特定のイベント ハブではなく、Event Hubs "*名前空間*" に移動します。 名前空間ページで **[設定]** の **[共有アクセス ポリシー]** を選択します。 **[要求]** で、その名前空間に対して**管理**アクセス許可が付与されていることを確認します。

    ![イベント ハブ名前空間のアクセス許可を管理する](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. 後で手動で接続情報を入力する場合は、Event Hubs 名前空間の接続文字列を取得します。 **[RootManageSharedAccessKey]** を選択します。 主キー接続文字列の横にあるコピー ボタンを選択します。 後で使用できるように接続文字列を保存します。

    ![Event Hubs 名前空間の接続文字列をコピーする](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > 接続文字列が Event Hubs 名前空間に関連付けられているか、特定のイベント ハブに関連付けられているかを確認するには、`EntityPath` パラメーターの接続文字列を確認します。 このパラメーターがある場合、接続文字列は特定のイベント ハブ "エンティティ" を対象としており、ロジック アプリで使用する正しい文字列ではありません。

## <a name="trigger-workflow-when-your-event-hub-gets-new-events"></a>イベント ハブが新しいイベントを受け取ったときにワークフローをトリガーする

"[*トリガー*](../logic-apps/logic-apps-overview.md#logic-app-concepts)" は、ロジック アプリのワークフローを開始するイベントです。 新しいイベントがイベント ハブに送信されたときにワークフローを開始するには、次の手順に従って、このイベントを検出するトリガーを追加します。

1. [Azure Portal](https://portal.azure.com "Azure Portal") で、既存のロジック アプリに移動するか、空のロジック アプリを作成します。

2. Logic Apps デザイナーで、検索ボックスにフィルターとして「イベント ハブ」と入力します。 **[When events are available in Event Hub (イベント ハブでイベントを使用できるとき)]** トリガーを選択します

   !["イベント ハブが新しいイベントを受け取るとき" のトリガーを選択する](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

   1. Event Hubs 名前空間への接続がまだない場合は、この接続を作成するように求められます。 接続に名前を付け、使用する Event Hubs 名前空間を選択します。

      ![イベント ハブ接続を作成する](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

      または、手動で接続文字列を入力する場合は、**[接続情報を手動で入力する]** を選択します。 
      [接続文字列を検索する方法](#permissions-connection-string)に関するセクションを参照してください。

   2. 使用する Event Hubs ポリシーを選択し、**[作成]** を選択します。

      ![イベント ハブ接続を作成する (パート 2)](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. 監視するイベント ハブを選択し、イベント ハブを確認する間隔と頻度を設定します。

    ![イベント ハブまたはコンシューマー グループを指定する](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

    > [!TIP]
    > オプションでイベントを読み取るためのコンシューマー グループを選択するには、**[詳細オプションのの表示]** を選択します。

4. ロジック アプリを保存し、 デザイナーのツール バーで、**[保存]** を選択します。

ロジック アプリが選択されたイベント ハブを確認して新しいイベントを検出すると、トリガーによって、検出されたイベントに対してロジック アプリ内でアクションが実行されます。

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>ロジック アプリからイベント ハブにイベントを送信する

"[*アクション*](../logic-apps/logic-apps-overview.md#logic-app-concepts)" は、ロジック アプリのワークフローによって実行されるタスクです。 ロジック アプリにトリガーを追加した後は、そのトリガーによって生成されたデータに対する操作を実行するアクションを追加できます。 ロジック アプリからイベント ハブにイベントを送信するには、以下の操作を行います。

1. Logic Apps デザイナーのトリガーで、**[新しいステップ]** > **[アクションの追加]** の順に選択します。

2. 検索ボックスに、フィルターとして「イベント ハブ」と入力します。
**[Event Hubs - 送信イベント]** アクションを選択します

   ![[Event Hubs - 送信イベント] を選択する](./media/connectors-create-api-azure-event-hubs/select-event-hubs-send-event-action.png)

3. イベントの送信先のイベント ハブを選択します。 次に、イベントの内容とその他の詳細を入力します。

   ![イベント ハブ名を選択し、イベントの内容を入力する](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

4. ロジック アプリを保存し、

これで、ロジック アプリからイベントを送信するアクションの設定が完了しました。 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

Swagger ファイルで定義されているトリガーとアクションおよび制限事項の詳細については、[コネクタの詳細](/connectors/eventhubs/)に関するページを参照してください。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](http://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* [Azure Logic Apps の他のコネクタ](../connectors/apis-list.md)の詳細情報