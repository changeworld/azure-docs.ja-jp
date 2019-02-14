---
title: ナレッジ ベースの移行 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker で作成したナレッジ ベースを新しいナレッジ ベースに移動します。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/06/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 73f355a6e8c9373a5c31dd7cfebd4455aa324302
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55809744"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>エクスポート/インポートを使用したナレッジ ベースの移行

ナレッジ ベースを移行するには、一方のナレッジ ベースからエクスポートしてから他方のナレッジ ベースにインポートする必要があります。 

## <a name="prerequisites"></a>前提条件

* 始める前に、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
* 新しい [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)をセットアップします

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>QnA Maker からのナレッジ ベースの移行
1. [QnA Maker ポータル](https://qnamaker.ai)にサインインします。
1. 移行するナレッジ ベースを選択します。

1. **[設定]** ページで **[Download knowledge base]\(ナレッジ ベースのダウンロード\)** をクリックして、ナレッジ ベースのコンテンツを含む .tsv ファイルをダウンロードします。質問、回答、メタデータ、データ ソース名などは、このファイルから抽出されました。

1. 上部のメニューから **[Create a knowledge base]\(ナレッジ ベースの作成\)** を選択し、空のナレッジ ベースを作成します。 

    ![データ ソースの設定](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - サービスに**名前**を付けます。 名前は重複しても構いません。特殊文字も使用できます。

1. **作成**を選択します。

    ![KB の作成](../media/qnamaker-how-to-create-kb/create-kb.png)

1. この新しいナレッジ ベースで、**[設定]** タブを開いて、**[Import knowledge base]\(ナレッジ ベースのインポート\)** を選択します。 これにより質問、回答、およびメタデータがインポートされ、抽出元のデータ ソースの名前は保持されます。

   ![ナレッジ ベースのインポート](../media/qnamaker-how-to-migrate-kb/Import.png)

1. テスト パネルを使用して、新しいナレッジ ベースを**テスト**します。 [ナレッジ ベースのテスト](../How-To/test-knowledge-base.md)方法を確認してください。
1. ナレッジ ベースを**公開**します。 [ナレッジ ベースの公開](../How-To/publish-knowledge-base.md)方法を確認してください。
1. お使いのアプリケーションまたはボット コードで、エンドポイントを使用します。 ここで、[QnA ボットの作成](../Tutorials/create-qna-bot.md)方法を参照してください。

    ![QnA Maker の値](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

    この時点で、ナレッジ ベースのすべてのコンテンツ、つまり、質問、回答、メタデータと、ソース ファイルの名前、URL が新しいナレッジ ベースにインポートされています。 

## <a name="chat-logs-and-alterations"></a>チャットログと変更
変更 (シノニム) は自動的にはインポートされません。 古いナレッジから変更をエクスポートするには [V2 API](https://aka.ms/qnamaker-v2-apis) を使用し、新しいナレッジ ベースに変更を移動するには [V4 API](https://aka.ms/qnamaker-v4-apis) を使用します。

チャットログを移行することはできません。新しいナレッジ ベースでは Application Insights を使ってチャットログが格納されているためです。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースを編集する](../How-To/edit-knowledge-base.md)
