---
title: ナレッジ ベースのインポート方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: ナレッジ ベースのインポート方法
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: ce8f98f9bdb37d5f326e942fe5b5e815e5272c56
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "35378702"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>エクスポート/インポートを使用したナレッジ ベースの移行
2018 月 5 月 7 日、\\\build\ カンファレンスで QnA Maker の一般提供が発表されました。 QnA Maker GA により、新しいアーキテクチャが Azure 上に構築されます。 QnA Maker Free プレビューで作成されたナレッジ ベースは、QnA Maker GA. に移行する必要があります。 QnA Maker プレビューは、2018 年 11 月に非推奨となる予定です。 QnA Maker GA での変更の詳細については、QnA Maker GA のお知らせに関する[ブログ記事](https://aka.ms/qnamakerga-blog)をご覧ください。

QnA Maker には、[価格モデル](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/qna-maker/)が導入されました。

前提条件
> [!div class="checklist"]
> * Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
> * 新しい [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)をセットアップします

## <a name="migrate-a-knowledge-base-from-qna-maker-preview-portal"></a>QnA Maker プレビュー ポータルからのナレッジ ベースの移行
1. [QnA Maker プレビュー ポータル](https://aka.ms/qnamaker-old-portal
)に移動し、**[My services]\(マイ サービス\)** をクリックします。
2. 編集アイコンをクリックして、移行するナレッジ ベースを選択します。

    ![ナレッジ ベースの編集](../media/qnamaker-how-to-migrate-kb/preview-editkb.png)

3. **[Download knowledge base]\(ナレッジ ベースのダウンロード\)** をクリックして、ナレッジ ベースのコンテンツを含む .tsv ファイルをダウンロードします。質問、回答、メタデータ、データ ソース名などは、このファイルから抽出されました。

    ![ナレッジ ベースのダウンロード](../media/qnamaker-how-to-migrate-kb/preview-download.png)

4. Azure 資格情報で [QnA Maker ポータル](https://qnamaker.ai)にサインインし、**[新しいサービスの作成]** をクリックします。

    ![KB の作成 ](../media/qnamaker-how-to-create-kb/create-new-service.png)
    
5. QnA Maker サービスをまだ作成していない場合、**[Create a QnA service]\(QnA サービスの作成\)** を選択します。 作成している場合、手順 2. のドロップダウンから QnA Maker サービスを選択します。 ナレッジ ベースをホストする QnA Maker サービスを選択します。

    ![QnA サービスのセットアップ](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

6. 空のナレッジ ベースを作成します 

    ![データ ソースの設定](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - サービスに**名前**を付けます。 名前は重複しても構いません。特殊文字も使用できます。
    - プレビュー ナレッジ ベースのデータを使用するため、ファイルまたは URL のアップロードをスキップします。 ここでは、空のナレッジ ベースを作成します。

7. **[作成]** を選択します。

    ![KB の作成](../media/qnamaker-how-to-create-kb/create-kb.png)

8. この新しいナレッジ ベースで、**[設定]** タブを開いて、**[Import knowledge base]\(ナレッジ ベースのインポート\)** をクリックします。 これにより質問、回答、およびメタデータがインポートされ、抽出元のデータ ソースの名前は保持されます。

   ![ナレッジ ベースのインポート](../media/qnamaker-how-to-migrate-kb/Import.png)

9. テスト パネルを使用して、新しいナレッジ ベースを**テスト**します。 [ナレッジ ベースのテスト](../How-To/test-knowledge-base.md)方法を確認してください。
10. ナレッジ ベースを**公開**します。 [ナレッジ ベースの公開](../How-To/publish-knowledge-base.md)方法を確認してください。
11. お使いのアプリケーションまたはボット コードで、次のエンドポイントを使用します。 ここで、[QnA ボットの作成](../Tutorials/create-qna-bot.md)方法を参照してください。

    ![QnA Maker の値](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

この時点で、ナレッジ ベースのすべてのコンテンツ、つまり、質問、回答、メタデータと、ソース ファイルの名前、URL が新しいナレッジ ベースにインポートされています。 

## <a name="chatlogs-and-alterations"></a>チャットログと変更
変更 (シノニム) は自動的にはインポートされません。 [V2 API](https://aka.ms/qnamaker-v2-apis) を使用してプレビュー スタックから変更をエクスポートし、[V4 API](https://aka.ms/qnamaker-v4-apis) を使用して、新しいスタック内で置き換えます。

チャットログを移行することはできません。新しいスタックでは Application Insights を使ってチャットログが格納されているためです。 ただし、[プレビュー ポータル](https://aka.ms/qnamaker-old-portal)からチャットログをダウンロードすることはできます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースを編集する](../How-To/edit-knowledge-base.md)
