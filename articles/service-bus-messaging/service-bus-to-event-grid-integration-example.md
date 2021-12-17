---
title: Service Bus のイベントを Event Grid 経由で Azure Logic Apps を使用して処理する
description: この記事では、Service Bus のイベントを Event Grid 経由で Azure Logic Apps を使用して処理する手順について取り上げます。
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 10/04/2021
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 5e5089985b56ad271f3de41fc3c68f091beddffb
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129457209"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-logic-apps"></a>チュートリアル:Azure Event Grid 経由で受信した Azure Service Bus のイベントに Azure Logic Apps を使用して応答する
このチュートリアルでは、Azure Logic Apps を使用して、Azure Event Grid 経由で受信した Azure Service Bus イベントに応答する方法について説明します。 

[!INCLUDE [service-bus-event-grid-prerequisites](./includes/service-bus-event-grid-prerequisites.md)]

## <a name="receive-messages-by-using-logic-apps"></a>Logic Apps を使用してメッセージを受信する
この手順では、Service Bus イベントを Azure Event Grid 経由で受信する Azure ロジック アプリを作成します。 

1. Azure portal で、ロジック アプリを作成します。
    1. **[+ リソースの作成]** 、 **[統合]** 、 **[ロジック アプリ]** の順に選択します。 
    2. Azure **サブスクリプション** を選択します。 
    3. **[リソース グループ]** では **[既存のものを使用]** を選択し、以前に作成済みの、他のリソース (Azure 関数、Service Bus 名前空間など) に使用したリソース グループを選択します。 
    1. **[種類]** では、 **[従量課金]** を選択します。 
    1. ロジック アプリの **名前** を入力します。
    1. ロジック アプリの **[リージョン]** を選択します。 
    1. **[確認および作成]** を選択します。 
    1. **[確認および作成]** ページで **[作成]** を選択してロジック アプリを作成します。 
    1. **[デプロイ完了]** ページで、 **[リソースに移動]** を選択します。 
1. **[Logic Apps デザイナー]** ページの **[テンプレート]** で、 **[空のロジック アプリ]** を選択します。 

### <a name="add-a-step-receive-messages-from-service-bus-via-event-grid"></a>Event Grid 経由で Service Bus からメッセージを受信するステップを追加する
1. デザイナーで、次の手順を実行します。
    1. **Event Grid** を検索します。 
    2. **[リソース イベントが発生したとき - Azure Event Grid]** を選択します。 

        ![Logic Apps デザイナー - Event Grid トリガーの選択](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. **[サインイン]** を選択して、Azure 資格情報を入力し、 **[Allow Access]\(アクセスを許可する\)** を選択します。 
5. **[When a resource event occurs]\(リソース イベントが発生したとき\)** ページで、次の手順を実行します。
    1. Azure サブスクリプションを選択します。 
    2. **[リソースの種類]** で、 **[Microsoft.ServiceBus.Namespaces]** を選択します。 
    3. **[リソース名]** で、Service Bus 名前空間を選択します。 
    4. **[新しいパラメーターの追加]** を選択し、 **[サフィックス フィルター]** を選択してから、フォーカスをドロップダウン リストの外に移動します。
    
        :::image type="content" source="./media/service-bus-to-event-grid-integration-example/add-new-parameter-suffix-filter.png" alt-text="サフィックス フィルターの追加を示す画像。":::
    1. **[サフィックス フィルター]** で、Service Bus トピックのサブスクリプションの名前を入力します。 
        ![Logic Apps デザイナー - イベントの構成](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
1. デザイナーで **[+ 新しいステップ]** を選択し、次の手順を実行します。
    1. **Service Bus** を検索します。
    2. 一覧から **[Service Bus]** を選択します。 
    3. **[アクション]** 一覧から **[メッセージを取得します]** を選択します。 
    4. **[トピック サブスクリプションからメッセージを取得する (ピークロック)]** を選択します。 

        ![Logic Apps デザイナー - メッセージの取得アクション](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. **接続名** を入力します。 次に例を示します。「**Get messages from the topic subscription**」。そして、Service Bus 名前空間を選択します。 

        ![Logic Apps デザイナー - Service Bus 名前空間の選択](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. **RootManageSharedAccessKey** を選び、 **[作成]** を選択します。

        ![Logic Apps デザイナー - 共有アクセス キーの選択](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. **トピック** と **サブスクリプション** を選択します。 
    
        ![トピックとサブスクリプションを選択する場所を示すスクリーンショット。](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)

### <a name="add-a-step-to-process-and-complete-received-messages"></a>受信したメッセージを処理して完了するステップを追加する
このステップでは、受信したメッセージをメールで送信し、メッセージを完了するステップを追加します。 実際のシナリオでは、メッセージを完了する前にロジック アプリでメッセージを処理します。

#### <a name="add-a-foreach-loop"></a>foreach ループを追加する
1. **[+ New step (+ 新しいステップ)]** を選択します。
1. **[コントロール]** を選択して選択します。  

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/select-control.png" alt-text="コントロール カテゴリの選択を示す画像":::
1. **[アクション]** の一覧で **[For each]** を選択します。

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/select-for-each.png" alt-text="[For each] コントロールの選択を示す画像":::    
1. **[以前の手順から出力を選択]** (必要に応じてテキスト ボックス内をクリック)では、 **[Get messages from a topic subscription (peek-lock)]\(トピック サブスクリプションからメッセージを取得 (peek-lock)\)** の下の **[本文]** を選択します。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/select-input-for-each.png" alt-text="For each への入力の選択を示す画像":::    

#### <a name="add-a-step-inside-the-foreach-loop-to-send-an-email-with-the-message-body"></a>メッセージ本文を含む電子メールを送信するステップを For each ループ内に追加する

1. **For each** ループ内で、 **[アクションの追加]** を選択します。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/select-add-action.png" alt-text="For each ループ内の [アクションの追加] ボタンの選択を示す画像":::        
1. **[コネクタとアクションを検索する]** ボックスで、「**Office 365**」と入力します。 
1. 検索結果で、「**Office 365 Outlook**」を選択します。 
1. アクションの一覧で、 **[メールの送信 (V2)]** を選択します。 
1. **[メールの送信 (V2)]** ウィンドウで、次の手順に従います。 
1. **[本文]** のテキスト ボックス内を選択し、次の手順に従います。
    1. **[宛先]** にはメール アドレスを入力します。 
    1. **[件名]** には、「**Message received from Service Bus topic's subscription**」と入力します。  
    1. **[式]** に切り替えます。
    1. 次の式を入力します。
    
        ```
        base64ToString(items('For_each')?['ContentData'])
        ``` 
    1. **[OK]** を選択します。 
    
        :::image type="content" source="./media/service-bus-to-event-grid-integration-example/specify-expression-email.png" alt-text="[メールの送信] アクティビティの本文の式を示す画像。":::

#### <a name="add-another-action-in-the-foreach-loop-to-complete-the-message"></a>For each ループに別のアクションを追加してメッセージを完了する         
1. **For each** ループ内で、 **[アクションの追加]** を選択します。 
    1. **[最近使った項目]** 一覧から **[Service Bus]** を選択します。
    2. アクションの一覧から **[トピック サブスクリプション内のメッセージを完了する]** を選択します。 
    3. Service Bus **トピック** を選択します。
    4. トピックに対する **サブスクリプション** を選択します。
    5. **[メッセージのロック トークン]** で、 **[動的なコンテンツ]** から **[ロック トークン]** を選択します。 

        ![Logic Apps デザイナー - メッセージを完成させる](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Logic Apps デザイナーのツールバーの **[保存]** を選択して、ロジック アプリを保存します。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/save-logic-app.png" alt-text="ロジック アプリを保存する":::

## <a name="test-the-app"></a>アプリをテストする
1. まだテスト メッセージをトピックに送信していない場合は、「[Service Bus トピックにメッセージを送信する](#send-messages-to-the-service-bus-topic)」セクションの手順に従って、トピックにメッセージを送信します。 
1. ロジック アプリの **[概要]** ページに切り替えてから、一番下のペインで **[実行の履歴]** タブに切り替えます。 トピックに送信されたロジック アプリの実行のメッセージが表示されます。 ロジック アプリの実行が確認できるまでに数分かかる場合があります。 ツール バーの **[Refresh]\(最新の情報に更新\)** を選択して、ページを最新の情報に更新してください。 

    ![Logic Apps デザイナー - ロジック アプリの実行](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)
1. ロジック アプリの実行を選択して詳細を確認します。 for ループで 5 つのメッセージが処理されていることに注目してください。 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/logic-app-run-details.png" alt-text="ロジック アプリの実行の詳細"::: 
2. ロジック アプリによって受信された各メッセージの電子メールを受け取ります。    

## <a name="troubleshoot"></a>トラブルシューティング
しばらく待って最新の情報に更新しても呼び出しが表示されない場合は、次の手順に従います。 

1. メッセージが Service Bus トピックに到達したことを確認します。 **[Service Bus トピック]** ページの **受信メッセージ** カウンターに注目してください。 この場合、**MessageSender** アプリケーションを 2 回実行したので、10 件のメッセージが表示されています (各実行につき 5 件のメッセージ)。

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="[Service Bus トピック] ページ - 受信メッセージ":::    
1. Service Bus サブスクリプションに **アクティブなメッセージがない** ことを確認します。 
    このページにイベントが 1 つも表示されない場合、 **[Service Bus Subscription]\(Service Bus サブスクリプション\)** ページに **[アクティブなメッセージ数]** が表示されていないことを確認してください。 このカウンターの数値がゼロより大きい場合は、なんらかの理由により、サブスクリプションのメッセージがハンドラー関数 (イベント サブスクリプション ハンドラー) に転送されていません。 イベント サブスクリプションが適切に設定されていることを確認してください。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="Service Bus サブスクリプション側におけるアクティブなメッセージ数":::    
1. また、Service Bus 名前空間の **[イベント]** ページに **配信済みイベント** が表示されます。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="[イベント] ページ - 配信済みイベント" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. イベントが配信されたことは、 **[イベント サブスクリプション]** ページでも確認できます。 このページには、 **[イベント]** ページでイベント サブスクリプションを選択することによってアクセスできます。 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="[イベント サブスクリプション] ページ - 配信済みイベント":::
## <a name="next-steps"></a>次のステップ

* [Azure Event Grid](../event-grid/index.yml) について学習します。
* [Azure Functions](../azure-functions/index.yml) について学習します。
* [Azure App Service の Logic Apps 機能](../logic-apps/index.yml)について学習します。
* Azure Service Bus の詳細については、[こちら](/azure/service-bus/)を参照してください。


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png