---
title: "Azure Service Bus で Azure Logic Apps 用メッセージングを設定する | Microsoft Docs"
description: "Azure Service Bus を使用してロジック アプリのメッセージを送受信します"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 02/06/2018
ms.author: ladocs
ms.openlocfilehash: e81580db17610adc6be534c9801881f9b68b14fd
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="send-and-receive-messages-with-the-azure-service-bus-connector"></a>Azure Service Bus コネクタを使用してメッセージを送受信する

ロジック アプリのメッセージを送受信するには、[Azure Service Bus](https://azure.microsoft.com/services/service-bus/) に接続します。 キューに送信、トピックに送信、キューから受信、サブスクリプションから受信などのアクションを実行できます。 [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) に関するページと [Logic Apps トリガーの価格設定](../logic-apps/logic-apps-pricing.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

Service Bus コネクタを使用するには、下記の項目が必要です。これらの項目は、互いに認識できるように同じ Azure サブスクリプションに存在する必要があります。

* [Service Bus 名前空間と、キューなどのメッセージング エンティティ](../service-bus-messaging/service-bus-create-namespace-portal.md)
* [ロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-service-bus"></a>Azure Service Bus に接続する

ロジック アプリがすべてのサービスにアクセスできるようにするには、ロジック アプリとサービスの間に "[*接続*](./connectors-overview.md)" を作成する必要があります (まだ作成していない場合)。 この接続により、ロジック アプリによるデータ アクセスが承認されます。 ロジック アプリが Service Bus アカウントにアクセスできるようにするために、アクセス許可を確認します。

1. [Azure Portal](https://portal.azure.com "Azure Portal") にサインインします。 

2. 特定の "メッセージング エンティティ" ではなく、Service Bus "*名前空間*" に移動します。 名前空間ページで **[設定]** の **[共有アクセス ポリシー]** を選択します。 **[要求]** で、その名前空間に対して**管理**アクセス許可が付与されていることを確認します。

   ![Service Bus 名前空間のアクセス許可を管理する](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. 後で手動で接続情報を入力する場合は、Service Bus 名前空間の接続文字列を取得します。 **[RootManageSharedAccessKey]** を選択します。 主キー接続文字列の横にあるコピー ボタンを選択します。 後で使用できるように接続文字列を保存します。

   ![Service Bus 名前空間の接続文字列をコピーする](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > 接続文字列が Service Bus 名前空間に関連付けられているか、特定のエンティティに関連付けられているかを確認するには、`EntityPath` パラメーターの接続文字列を確認します。 このパラメーターがある場合、接続文字列は特定のエンティティを対象としています。これは、ロジック アプリで使用するのに適切な文字列ではありません。

## <a name="trigger-workflow-when-your-service-bus-gets-new-messages"></a>Service Bus が新しいメッセージを受信したときにワークフローをトリガーする

"[*トリガー*](../logic-apps/logic-apps-overview.md#logic-app-concepts)" は、ロジック アプリのワークフローを開始するイベントです。 新しいメッセージが Service Bus に送信されたときにワークフローを開始するには、次の手順に従って、これらのメッセージを検出するトリガーを追加します。

1. [Azure Portal](https://portal.azure.com "Azure Portal") で、既存のロジック アプリに移動するか、空のロジック アプリを作成します。

2. Logic Apps デザイナーで、検索ボックスにフィルターとして「サービス バス」と入力します。 **Service Bus** コネクタを選択します。 

   ![Service Bus コネクタを選択する](./media/connectors-create-api-azure-service-bus/select-service-bus-connector.png) 

3. 使用するトリガーを選択します。 たとえば、新しい項目が Service Bus キューに送信されたときにロジック アプリを実行するには、**[Service Bus - メッセージがキューに着信したとき (オート コンプリート)]** トリガーを選択します。

   ![Service Bus トリガーを選択する](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   1. Service Bus 名前空間への接続がまだない場合は、この接続を作成するように求められます。 接続に名前を付け、使用する Service Bus 名前空間を選択します。

      ![Service Bus 接続を作成する](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      または、手動で接続文字列を入力する場合は、**[接続情報を手動で入力する]** を選択します。 
      [接続文字列を検索する方法](#permissions-connection-string)に関するセクションを参照してください。

   2. 使用する Service Bus ポリシーを選択し、**[作成]** を選択します。

      ![Service Bus 接続を作成する (パート 2)](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

4. 使用する Service Bus キューを選択し、キューを確認する間隔と頻度を設定します。

   ![Service Bus キューを選択し、ポーリング間隔を設定する](./media/connectors-create-api-azure-service-bus/select-service-bus-queue.png)

5. ロジック アプリを保存し、 デザイナーのツール バーで、**[保存]** を選択します。

ロジック アプリが選択されたキューを確認して新しいメッセージを検出すると、トリガーによって、検出されたメッセージに対してロジック アプリ内でアクションが実行されます。

## <a name="send-messages-from-your-logic-app-to-your-service-bus"></a>ロジック アプリから、Service Bus にメッセージを送信する

"[*アクション*](../logic-apps/logic-apps-overview.md#logic-app-concepts)" は、ロジック アプリのワークフローによって実行されるタスクです。 ロジック アプリにトリガーを追加した後は、そのトリガーによって生成されたデータに対する操作を実行するアクションを追加できます。 ロジック アプリから Service Bus メッセージング エンティティにメッセージを送信するには、次の手順を実行します。

1. Logic Apps デザイナーのトリガーで、**[+ 新しいステップ]** > **[アクションの追加]** の順に選択します。

2. 検索ボックスに、フィルターとして「サービス バス」と入力します。 **Service Bus** コネクタを選択します。

   ![Service Bus コネクタを選択する](./media/connectors-create-api-azure-service-bus/select-service-bus-connector-for-action.png) 

3. **[Service Bus - メッセージの送信]** アクションを選択します。

   ![[Service Bus - メッセージの送信] アクションを選択する](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png)

4. メッセージの送信先のメッセージング エンティティ (キューまたはトピック名) を選択します。 次に、メッセージの内容とその他の詳細を入力します。

   ![メッセージング エンティティを選択し、メッセージの詳細を入力する](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)    

5. ロジック アプリを保存し、 

これで、ロジック アプリからメッセージを送信するアクションの設定が完了しました。 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

Swagger ファイルで定義されているトリガーとアクションおよび制限事項の詳細については、[コネクタの詳細](/connectors/servicebus/)に関するページを参照してください。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](http://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* [Azure Logic Apps の他のコネクタ](../connectors/apis-list.md)の詳細情報