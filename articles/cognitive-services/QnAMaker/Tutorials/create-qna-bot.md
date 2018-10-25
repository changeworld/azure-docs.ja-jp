---
title: Azure Bot Service による QnA ボット - QnA Maker
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、Azure portal で Azure Bot Service v3 を使用して QnA ボットを構築する手順について説明します。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 30400b04ec08d936242b022f10cf1485e009e6d2
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647325"
---
# <a name="create-a-qna-bot-with-azure-bot-service-v3"></a>Azure Bot Service v3 による QnA ボットの作成
このチュートリアルでは、Azure portal で Azure Bot Service v3 を使用して QnA ボットを構築する手順について説明します。

## <a name="prerequisite"></a>前提条件
構築する前に、「[ナレッジ ベースの作成](../How-To/create-knowledge-base.md)」の手順に従って、質問と回答が含まれる QnA Maker サービスを作成します。

このボットでは、QnAMakerDialog を介して、作成されたナレッジ ベースの質問に応答します。

## <a name="create-a-qna-bot"></a>QnA ボットの作成
1. [Azure portal](https://portal.azure.com) のメニュー ブレードで、新しいリソースの **[作成]** を選択し、**[すべて表示]** を選択します。

    ![ボット サービスの作成](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. 検索ボックスで、「**Web アプリ ボット**」を検索します。

    ![ボット サービスの選択](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. **[ボット サービス] ブレード**で、必要な情報を指定します。

    - **[アプリ名]** にボットの名前を設定します。 この名前は、ボットがクラウドにデプロイされるときに、サブドメインとして使用されます (mynotesbot.azurewebsites.net など)。
    - [サブスクリプション]、[リソース グループ]、[App Service プラン]、[場所] を選択します。

4. SDK v4 を使用して QnA ボットを作成する方法については、[QnA v4 bot template](https://aka.ms/qna-bot-v4)\(QnA v4 ボット テンプレート\) を参照してください。 v3 テンプレートを使用するには、SDK バージョンは **SDK v3** を、SDK 言語は **C#** または **Node.js** を選択します。

    ![ボット SDK 設定](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. ボット テンプレートのフィールドで **[Question and Answer]\(質問と回答\)** テンプレートを選択し、**[選択]** を選択してテンプレート設定を保存します。

    ![ボット サービスの選択](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. 設定を確認して **[作成]** を選択します。 これによって、QnAMakerDialog を使用したボット サービスが作成され、Azure にデプロイされます。

    ![ボット サービスの選択](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. ボット サービスがデプロイされたことを確認します。

    - **[通知]** (Azure portal の上端にあるベル アイコン) を選択します。 通知は、**[デプロイが開始されました]** から **[デプロイメントに成功しました]** に変わります。
    - 通知が **[デプロイメントに成功しました]** に変わったら、その通知で **[リソースに移動]** を選択します。

## <a name="chat-with-the-bot"></a>ボットとチャットする
**[リソースに移動]** を選択すると、ボットのリソース ブレードに移動します。

ボットが登録されたら、**[Test in Web Chat]\(Web チャットでのテスト\)** をクリックして、Web チャット ウィンドウを開きます。 Web チャットに「hello」と入力します。

![QnA ボットの Web チャット](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

ボットは、"Please set QnAKnowledgebaseId and QnASubscriptionKey in App Settings.  Learn how to get them at https://aka.ms/qnaabssetup" と応答します。 この応答は、QnA ボットがメッセージを受信したが、QnA Maker のナレッジ ベースはまだボットに関連付けられていないことを裏付けています。 次の手順では、これを実行します。

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>QnA Maker ナレッジ ベースをボットに接続する

1. **[アプリケーション設定]** を開き、**QnAKnowledgebaseId**、**QnAAuthKey**、および **QnAEndpointHostName** の各フィールドを編集して、QnA Maker ナレッジ ベースの値を格納します。

    ![アプリケーション設定](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

2. https://qnamaker.ai で、ナレッジ ベースの [設定] タブから、ナレッジ ベース ID、ホスト URL、およびエンドポイント キーを取得します。
    - [QnA Maker](https://qnamaker.ai) にサインインする
    - ナレッジ ベースに移動する
    - **[設定]** タブをクリックする
    - まだの場合は、ナレッジ ベースを**発行**します。

    ![QnA Maker の値](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> ナレッジ ベースのプレビュー バージョンを QnA ボットと接続する場合は、**Ocp-Apim-Subscription-Key** の値を **QnAAuthKey** に設定します。 **QnAEndpointHostName** は空のままにします。

## <a name="test-the-bot"></a>ボットをテストする
Azure portal で、**[Test in Web Chat]\(Web チャットでのテスト\)** をクリックしてボットをテストします。 

![QnA Maker ボット](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

これで、QnA ボットがナレッジ ベースから回答するようになりました。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker と LUIS を統合する](./integrate-qnamaker-luis.md)

## <a name="see-also"></a>関連項目

- [ナレッジ ベースを管理する](https://qnamaker.ai)
- [さまざまなチャネルでボットを有効にする](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
