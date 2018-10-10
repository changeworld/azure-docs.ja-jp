---
title: ナレッジ ベースの作成 - QnA Maker
titleSuffix: Azure Cognitive Services
description: ボットにおしゃべりを追加すると、ボットがより会話的で魅力的なものになります。 QnA Maker を使用すると、定義済みの最上位のおしゃべりのセットを、KB に簡単に追加することができます。 これを使ってボットのおしゃべりの作成を始めれば、最初から作成するよりもコストと時間を節約することができます。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 274c2289c75f44c5a1c8dd3799612a23f46a6d67
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037696"
---
# <a name="create-a-knowledge-base"></a>ナレッジ ベースの作成

QnA Maker では、ナレッジ ベースを作成するときに既存のデータ ソースを簡単に追加できます。 次のドキュメントの種類から、新しい QnA Maker ナレッジ ベースを作成できます。

<!-- added for scanability -->
* FAQ ページ
* 製品マニュアル
* 構造化ドキュメント

## <a name="steps"></a>手順

1. Azure 資格情報で [QnA Maker ポータル](https://qnamaker.ai)にサインインし、**[新しいサービスの作成]** を選択します。

    ![KB の作成 ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. QnA Maker サービスをまだ作成していない場合、**[Create a QnA service]\(QnA サービスの作成\)** を選択します。 

3. QnA Maker ポータルの**手順 2** のリストから、QnA Maker サービスと関連付けられた Azure テナント、Azure サブスクリプション名、および Azure リソース名を選択します。 ナレッジ ベースをホストする Azure QnA Maker サービスを選択します。

    ![QnA サービスのセットアップ](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

4. ナレッジ ベースの名前と、新しいナレッジ ベースのデータ ソースを入力します。

    ![データ ソースの設定](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - サービスに**名前**を付けます。 重複した名前と特殊文字がサポートされています。
    - 抽出するデータの URL を追加します。 サポートされているソースの種類については[こちら](../Concepts/data-sources-supported.md)をご覧ください。
    - 抽出するデータのファイルをアップロードします。 追加できるドキュメントの数については、[価格情報](https://aka.ms/qnamaker-pricing)をご覧ください。
    - QnA を手動で追加する場合、上の図に示した**手順 4** は省略できます。

5. **おしゃべり**を KB に追加します。 3 つの事前定義済みパーソナリティのいずれかを選択して、おしゃべりのサポートをボットに追加します。 

    <!-- TBD: add back in when chit chat how-to is merged
    ![Add chit-chat to KB ](../media/qnamaker-how-to-chitchat/create-kb-chit-chat.png)
    -->

6. **[Create your KB]\(KB の作成\)** を選択します。

    ![KB の作成](../media/qnamaker-how-to-create-kb/create-kb.png)

7. データが抽出されるまで数分かかります。

    ![抽出](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

8. ナレッジ ベースが正常に作成されると、**ナレッジ ベース** ページにリダイレクトされます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Add chit-chat personal](./chit-chat-knowledge-base.md)\(おしゃべり個人の追加\)
