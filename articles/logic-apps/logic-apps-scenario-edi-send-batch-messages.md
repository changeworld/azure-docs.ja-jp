---
title: EDI メッセージをグループまたはコレクションとしてバッチ処理を行う - Azure Logic Apps | Microsoft Docs
description: ロジック アプリでバッチ処理を行う EDI メッセージを送信します
keywords: バッチ, バッチ処理, バッチ エンコード
author: divswa
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: LADocs; estfan; divswa
ms.openlocfilehash: fb15688968cb29039fc669ed6b8685ba64df9e81
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432135"
---
# <a name="send-x12-messages-in-batch-to-trading-partners"></a>X12 メッセージを取引先にバッチで送信する

企業間 (B2B) シナリオでは、パートナーがグループまたはバッチでメッセージを交換することがよくあります。 メッセージをグループまたはバッチで取引先に送信するには、複数の項目を含むバッチを作成し、X12 バッチ アクションを使用してそれらの項目をバッチとして処理します。


X12 メッセージのバッチ処理では、他のメッセージと同様に、バッチ トリガーとアクションを使用します。 また、X12 のバッチの場合、パートナーまたは他の宛先に送られる前に X12 エンコード ステップが実行されます。 バッチ トリガーとアクションの詳細については、[メッセージのバッチ処理](logic-apps-batch-process-send-receive-messages.md)に関するページを参照してください。

このトピックでは、次のタスクを実行して X12 メッセージをバッチとして処理する方法を示します。
* [項目を受け取ってバッチを作成するロジック アプリを作成する](#receiver)。 この "受信" ロジック アプリは、次のアクションを実行します。
 
   * バッチ名と、項目をバッチとしてリリースする前に満たす必要があるリリース条件を指定します。

   * 指定した X12 契約またはパートナー ID を使用して、バッチ内の項目を処理またはエンコードします。

* [項目をバッチに送信するロジック アプリを作成する](#sender)。 この "送信" ロジック アプリは、バッチ処理のために項目を送信する場所を指定します。この場所は、既存の受信ロジック アプリである必要があります。


## <a name="prerequisites"></a>前提条件

この例に従うには、次の項目が必要です。

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントで作業を開始](https://azure.microsoft.com/free/)できます。 また、[従量課金制サブスクリプションにサインアップ](https://azure.microsoft.com/pricing/purchase-options/)することもできます。

* 既に定義され、Azure サブスクリプションに関連付けられている[統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)

* 統合アカウントで既に定義されている 2 つ以上の[パートナー](logic-apps-enterprise-integration-partners.md)。 各パートナーのプロパティで X12 (Standard Carrier Alpha Code) 修飾子がビジネス ID として使用されていることを確認します。

* 統合アカウントで既に定義されている [X12 契約](logic-apps-enterprise-integration-x12.md)

<a name="receiver"></a>

## <a name="create-a-logic-app-that-receives-x12-messages-and-creates-a-batch"></a>X12 メッセージを受け取ってバッチを作成するロジック アプリを作成する

メッセージをバッチに送信する前に、まず**バッチ** トリガーを使用して "受信" ロジック アプリを作成する必要があります。 こうすることで、送信ロジック アプリを作成するときにこの受信ロジック アプリを選択できます。 受信ロジック アプリに対し、バッチ名、リリース条件、X12 契約、およびその他の設定を指定します。 


1. [Azure Portal](https://portal.azure.com) で、"BatchX12Messages" という名前のロジック アプリを作成します。

1. Logic Apps デザイナーで、ロジック アプリのワークフローを開始する**バッチ** トリガーを追加します。 検索ボックスに、フィルターとして「batch」と入力します。 **[バッチ - メッセージのバッチ処理]** というトリガーを選択します。

   ![バッチ トリガーを追加する](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

1. バッチの名前およびバッチをリリースするための条件を次のように指定します。

   * **[バッチ名]**: バッチの識別に使用する名前 (この例では "TestBatch")。

   * **[リリース条件]**: バッチ リリース条件。メッセージ数、スケジュール、またはその両方を基準にすることができます。
   
     ![バッチ トリガーの詳細を指定する](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **[メッセージ数]**: リリースして処理するまでにバッチとして保持するメッセージの数 (この例では "5")。

     ![バッチ トリガーの詳細を指定する](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **[スケジュール]**: 処理のバッチ リリース スケジュール (この例では "10 分ごと")。

     ![バッチ トリガーの詳細を指定する](./media/logic-apps-scenario-EDI-send-batch-messages/receive-batch-schedule-based.png)


1. グループ化またはバッチ処理されたメッセージをエンコードする別のアクションを追加し、X12 のバッチ処理されたメッセージを作成します。 

   a. **[+ 新しいステップ]** > **[アクションの追加]** の順に選択します。

   b. 検索ボックスにフィルターとして「X12 batch」と入力し、**[X12 - Batch Encode]\(X12 - バッチ エンコード\)** アクションを選択します。 X12 エンコード コネクタと同様に、バッチ エンコード アクションには複数のバリエーションがあります。 いずれかのアクションを選択することができます。

   ![X12 バッチ エンコード アクションを選択する](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)
   
1. 今追加したアクションのプロパティを設定します。

   * **[X12 契約の名前]** ボックスで、ドロップダウン リストから契約を選択します。 リストが空の場合は、統合アカウントへの接続が作成されていることを確認してください。

   * **[BatchName]** ボックスで、動的コンテンツ リストから **[バッチ名]** フィールドを選択します。
   
   * **[PartitionName]** ボックスで、動的コンテンツ リストから **[パーティション名]** フィールドを選択します。

   * **[Items]** ボックスで、動的コンテンツ リストから **[バッチ処理されたアイテム]** フィールドを選択します。

   ![バッチ エンコード アクションの詳細](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

1. テスト目的のために、バッチ処理されたメッセージを [RequestBin サービス](https://requestbin.fullcontact.com/)に送信するための HTTP アクションを追加します。 

   1. 検索ボックスに、フィルターとして「HTTP」と入力します。 **[HTTP - HTTP]** アクションを選択します
    
      ![HTTP アクションを選択する](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action.png)

   1. **[メソッド]** の一覧で **[POST]** を選択します。 要求ビンの URI を生成し、その URI を **[Uri]** ボックスに入力します。 **[本文]** ボックスで、動的一覧を開き、**[契約名によるバッチ エンコード]** セクションの **[本文]** フィールドを選択します。 **[本文]** が表示されない場合は、**[契約名によるバッチ エンコード]** の横の **[See more]\(さらに表示\)** を選択してください。

      ![HTTP アクションの詳細を指定する](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action-details.png)

1.  これで受信ロジック アプリが作成されたので、このロジック アプリを保存します。

    ![ロジック アプリを保存する](./media/logic-apps-scenario-EDI-send-batch-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > パーティションには 5,000 メッセージまたは 80 MB という制限があります。 いずれかの条件を満たすと、ユーザー定義の条件を満たしていない場合でも、バッチがリリースされる可能性があります。

<a name="sender"></a>

## <a name="create-a-logic-app-that-sends-x12-messages-to-a-batch"></a>X12 メッセージをバッチに送信するロジック アプリを作成する

ここでは受信ロジック アプリで定義されたバッチに項目を送信するロジック アプリを 1 つ以上作成します。 送信アプリには、受信ロジック アプリ、バッチ名、メッセージ コンテンツ、およびその他の設定を指定します。 一意のパーティション キーを指定してバッチをサブセットに分割し、そのキーを持つ項目を収集するように設定することもできます。

送信ロジック アプリでは項目を送信する場所を指定する必要がありますが、受信ロジック アプリに送信アプリの情報は不要です。


1. "X12MessageSender" という名前の別のロジック アプリを作成します。 **[要求と応答 - 要求]** トリガーをロジック アプリに追加します 
   
   ![要求トリガーを追加する](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

1. メッセージをバッチに送信する新しいステップを追加します。

   1. **[+ 新しいステップ]** > **[アクションの追加]** の順に選択します。

   1. 検索ボックスに、フィルターとして「batch」と入力します。 

1. アクション **[バッチ処理するメッセージの送信 – バッチ トリガーを含む Logic Apps ワークフローを選択します]** を選択します。

   ![[バッチ処理するメッセージの送信] を選択する](./media/logic-apps-scenario-EDI-send-batch-messages/send-messages-batch-action.png)

1. ここで、前に作成した、アクションとして表示されている "BatchX12Messages" ロジック アプリを選択します。

   !["バッチ受信" ロジック アプリを選択する](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-receiver.png)

   > [!NOTE]
   > 一覧にはバッチ トリガーがあるその他のロジック アプリも表示されます。

1. バッチのプロパティを設定します。

   * **[バッチ名]**: 受信ロジック アプリによって定義されたバッチ名 (この例では "TestBatch" で実行時に検証されます)。

     > [!IMPORTANT]
     > 受信ロジック アプリで指定されたバッチ名と一致している必要があるため、バッチ名は変更しないように注意してください。
     > バッチ名を変更すると、送信ロジック アプリが失敗します。

   * **[メッセージのコンテンツ]**: バッチに送信するメッセージのコンテンツ
   
   ![バッチのプロパティを設定する](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-properties.png)

1. ロジック アプリを保存し、 送信ロジック アプリは次の例のようになります。

   ![送信ロジック アプリを保存する](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-finished.png)

## <a name="test-your-logic-apps"></a>ロジック アプリをテストする

バッチ処理ソリューションをテストするには、[Postman](https://www.getpostman.com/postman) または類似のツールを使用して、X12 メッセージを送信ロジック アプリに送信します。 間もなく、パーティション キーがすべて同じ X12 メッセージを、5 つの項目を含むバッチとして、または 10 分ごとに、要求ビンで受信するようになります。

## <a name="next-steps"></a>次の手順

* [メッセージをバッチとして処理する](logic-apps-batch-process-send-receive-messages.md) 
* [Azure Logic Apps と関数を使用して Visual Studio でサーバーレス アプリを構築する](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [ロジック アプリの例外処理とエラーのログ記録](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
