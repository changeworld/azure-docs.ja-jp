---
title: ナレッジ ベースの作成方法 - QnA Maker - Azure Cognitive Services | Microsoft Docs
description: ナレッジ ベースの作成方法
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 93b64948ecc52feeb0f862f2b76ea898dce2333a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "35378697"
---
# <a name="create-a-knowledge-base"></a>ナレッジ ベースの作成

QnA Maker では、既存のデータ ソースをオンボードし、ナレッジ ベースを作成することが非常に簡単です。 FAQ ページ、製品マニュアル、構造化されたドキュメントから新しい QnA Maker ナレッジ ベースを作成したり、編集者としてそれらを追加したりできます。

## <a name="steps"></a>手順

1. 最初に Azure 資格情報で [QnA Maker ポータル](https://qnamaker.ai)にサインインし、**[新しいサービスの作成]** をクリックします。

    ![KB を作成する ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. QnA Maker サービスをまだ作成していない場合、**[Create a QnA service]\(QnA サービスの作成\)** を選択します。 作成している場合、手順 2 のドロップダウンから QnA Maker サービスを選択します。 ナレッジ ベースをホストする QnA Maker サービスを選択します。

    ![QnA サービスを設定する](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

3. ナレッジ ベースを作成する目的で次の情報を入力します。

    ![データ ソースを設定する](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - サービスに**名前**を付けます。 名前は重複しても構いません。特殊文字も使用できます。
    - URL を貼り付けます。この URL が抽出元になります。 サポートされているソースの種類については[こちら](../Concepts/data-sources-supported.md)をご覧ください。
    - あるいは、データを抽出するファイルをアップロードします。 追加できるドキュメントの数については、[価格情報](https://aka.ms/qnamaker-pricing
)をご覧ください。
    - QnA をマニュアルで追加する場合、ファイルのリンク設定は省略できます。

4. **[作成]** を選択します。

    ![KB を作成する](../media/qnamaker-how-to-create-kb/create-kb.png)

5. データが抽出されるまで数分かかります。

    ![抽出](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

6. ナレッジ ベースが正常に作成されると、**ナレッジ ベース**ページにリダイレクトされます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースをインポートする](../Tutorials/migrate-knowledge-base.md)
