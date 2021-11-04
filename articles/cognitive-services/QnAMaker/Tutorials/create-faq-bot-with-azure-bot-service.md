---
title: チュートリアル:Azure Bot Service を使用した FAQ ボットの作成
description: このチュートリアルでは、QnA Maker と Azure Bot Service を使用し、コードなしの FAQ ボットを作成します。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 08/31/2020
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1d0e48da09cccbed74d3a4d8bfc0e89907a336a3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020485"
---
# <a name="tutorial-create-a-faq-bot-with-azure-bot-service"></a>チュートリアル: Azure Bot Service で FAQ ボットを作成する
QnA Maker と Azure [Bot Service](https://azure.microsoft.com/services/bot-service/) を使用して FAQ ボットをコードなしで作成します。

このチュートリアルでは、以下の内容を学習します。

<!-- green checkmark -->
> [!div class="checklist"]
> * Azure Bot Service に QnA Maker ナレッジ ベースをリンクする
> * ボットをデプロイする
> * Web チャットでボットとチャットする
> * サポートされているチャネルでボットを照らす

## <a name="create-and-publish-a-knowledge-base"></a>ナレッジ ベースの作成と公開

[クイックスタート](../Quickstarts/create-publish-knowledge-base.md)に従ってナレッジ ベースを作成します。 ナレッジ ベースが正常に発行されると、下のページが表示されます。

> [!div class="mx-imgBorder"]
> ![正常な発行のスクリーンショット](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)


## <a name="create-a-bot"></a>ボットの作成

発行後は、 **[発行]** ページからボットを作成できます。

* 複数のボットを迅速に作成し、個々のボットはリージョンや価格プランが異なっていてもすべて同じナレッジ ベースを指すことができます。
* ナレッジ ベースのボットを 1 つだけにする場合は、 **[View all your bots on the Azure portal]\(Azure portal 上で自分のボットをすべて表示する\)** リンクを使用して、ご自分の現在のボットの一覧を表示します。

ナレッジ ベースに変更を加えて再発行しても、ボットに対して追加のアクションを実行する必要はありません。 ナレッジ ベースと連携するように既に構成されており、ナレッジ ベースに対する今後のすべての変更に対応します。 ナレッジ ベースを発行するたびに、それに接続されているすべてのボットが自動的に更新されます。

1. QnA Maker ポータルの **[発行]** ページで、 **[Create bot]\(ボットの作成\)** を選択します。 このボタンは、ナレッジ ベースの発行後にのみ表示されます。

    > [!div class="mx-imgBorder"]
    > ![ボットの作成のスクリーンショット](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Azure portal の新しいブラウザー タブが開き、Azure Bot Service の作成ページが表示されます。 Azure Bot Service を構成します。 ボットと QnA Maker は、Web App Service プランを共有できますが、Web アプリを共有することはできません。 そのため、ボットの **アプリ名** は、QnA Maker サービスのアプリ名とは異なる必要があります。

    * **すべきこと**
        * ボット ハンドルが一意でない場合に、ボット ハンドルを変更する。
        * SDK 言語を選択する。 ボットの作成後、ローカル開発環境にコードをダウンロードして、開発プロセスを続行できます。
    * **やってはいけないこと**
        * ボットを作成するときに、Azure portal 上で次の設定を変更する。 これらはご自分の既存のナレッジ ベースのために事前に設定されています。
           * QnA 認証キー
           * App Service プランと場所


1. ボットが作成された後、 **[Bot service]\(ボット サービス\)** リソースを開きます。
1. **[Bot Management]\(ボット管理\)** で、 **[Test in Web Chat]\(Web チャットでのテスト\)** を選択します。
1. チャット プロンプトの **[Type your message]\(メッセージを入力\)** で、以下のように入力します。

    `Azure services?`

    チャット ボットが、ナレッジ ベースからの回答で応答します。

    > [!div class="mx-imgBorder"]
    > ![応答を返すボットのスクリーンショット](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)


1. 追加の[サポートされているチャネル](/azure/bot-service/bot-service-manage-channels)でボットを照らします。
    
## <a name="integrate-the-bot-with-channels"></a>ボットをチャネルと統合する

作成したボット サービス リソースの **[Channels]\(チャネル\)** をクリックします。 追加の[サポートされているチャネル](/azure/bot-service/bot-service-manage-channels)でボットを照らすことができます。

   >[!div class="mx-imgBorder"]
   >![Teams との統合のスクリーンショット](../media/qnamaker-tutorial-updates/connect-with-teams.png)
