---
title: ナレッジ ベースの作成、トレーニング、発行 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker のナレッジ ベース (KB) は、よくあるご質問や製品マニュアルなど、独自のコンテンツから作成できます。 この例では、BitLocker キー回復に関する単純なよくあるご質問の Web ページから QnA Maker KB を作成します。
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: diberry
ms.openlocfilehash: 4b4f208524f18b98d44dc3d34e05359445fb2f17
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53598312"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>QnA Maker ナレッジ ベースの作成、トレーニング、発行

QnA Maker のナレッジ ベース (KB) は、よくあるご質問や製品マニュアルなど、独自のコンテンツから作成できます。 この例では、BitLocker キー回復に関する単純なよくあるご質問の Web ページから QnA Maker KB を作成します。

## <a name="prerequisite"></a>前提条件

> [!div class="checklist"]
> * Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-qna-maker-knowledge-base"></a>QnA Maker ナレッジ ベースの作成

1. Azure の資格情報で QnAMaker.ai にサインインします。

2. QnA Maker Web サイトで、**[Create a knowledge base]\(ナレッジ ベースの作成)** を選択します。

   ![新規 KB の作成](../media/qna-maker-create-kb.png)

3. 手順 1 として、**[作成]** ページで **[Create a QnA service]\(QnA サービスの作成)** を選択します。 サブスクリプションで QnA Maker サービスを設定するため、[Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) にリダイレクトされます。 Azure portal がタイムアウトした場合は、サイトで **[Try again]\(再試行)** を選択します。 接続されると、Azure ダッシュ ボードが表示されます。

4. Azure で新しい QnA Maker サービスが正常に作成できたら、qnamaker.ai/create に戻ります。 手順 2 として、ドロップダウン リストから QnA サービスを選択します。 新しい QnA サービスを作成した場合は、必ずページを更新します。

   ![QnA サービス KB の選択](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

5. 手順 3 として、KB に **My Sample QnA KB** という名前を付けます。

6. KB にコンテンツを追加するために、3 種類のデータ ソースを選択します。 手順 4 として、**[Populate your KB]\(KB の入力)** の **[URL]** ボックスに [BitLocker Recovery FAQ](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) の URL を追加します。

   ![QnA サービス KB の選択](../media/qnamaker-quickstart-kb/add-datasources.png)

7. 手順 5 として、**[Create your KB]\(KB の作成)** を選択します。

8. KB の作成中に、ポップアップ ウィンドウが表示されます。 抽出プロセスでは、HTML ページを読み取って質問と回答を識別します。これには数分かかります。

9. KB の作成が成功すると、**[Knowledge base]\(ナレッジ ベース)** ページが開きます。 このページで、KB の内容を編集することができます。

10. 右上の **[Add QnA pair]\(QnA ペアの追加)** を選択し、KB の **[Editorial]\(編集)** セクションに新しい行を追加します。 **[質問]** に、**Hi** と入力します。 **[回答]** に、**こんにちは。bitlocker について質問してください。** と入力します。

   ![QnA ペアの追加](../media/qnamaker-quickstart-kb/add-qna-pair.png)

11. 右上の **[Save and train]\(保存してトレーニング\)** を選択し、編集内容を保存して QnA Maker のモデルをトレーニングします。 保存しないと、編集した内容は保持されません。

12. 右上の **[テスト]** を選択し、行った変更が反映したかをテストします。 ボックスに「`hi there`」と入力し、Enter キーを押します。 作成した応答が回答として表示されます。

13. **[検査]** を選択し、詳細に応答を確認します。 テスト ウィンドウは、公開する前に KB への変更をテストする際に使用します。

   ![テスト パネル](../media/qnamaker-quickstart-kb/inspect-panel.png)

14. もう一度 **[テスト]** を選択して、**[テスト]** ポップアップを閉じます。

15. **[編集]** の隣のメニューから、**[発行]** を選択します。 確認のため、ページの **[発行]** を選択します。

16. QnA Maker サービスが正常に発行されました。 お使いのアプリケーションまたはボット コードで、エンドポイントを使用できます。

   ![発行](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースの作成](../How-To/create-knowledge-base.md)
