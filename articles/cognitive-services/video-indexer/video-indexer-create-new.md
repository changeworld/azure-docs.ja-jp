---
title: 'チュートリアル: 既存のビデオからビデオの分析情報を作成する'
titlesuffix: Azure Cognitive Services
description: このトピックでは、既存のビデオ ファイルに基づいてビデオの分析情報を作成し、公開する方法を示します。
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: tutorial
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: 3a3e5c62d9eb96563d395106db62cc91a8307658
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983919"
---
# <a name="tutorial-create-highlights-from-existing-videos"></a>チュートリアル: 既存のビデオからハイライトを作成する

このトピックでは、他のビデオに基づいてビデオの分析情報を作成し、公開する方法を示します。

1. [Video Indexer](https://www.videoindexer.ai/) Web サイトに移動してサインインします。
2. ビデオの分析情報を作成するビデオを見つけます。
3. **[再生]** を押します。

    ビデオの要約された分析情報がページに表示されます。 

    ![洞察](./media/video-indexer-create-new/video-indexer-summarized-insights.png)

3. **[編集]** ボタンを押します。

    このページには、ビデオの完全な内訳が表示されます。 内訳はブロックに分割されています。 ブロックは、データ内の移動を簡単にするために使用されています。 たとえば、話者が変わったり、長い休止があるときにブロックが分割されます。 必要な行のみを含む独自のプレイリストを作成できます。 ソース ビデオの特定の部分のみを表示するには、トピック/キーワード、センチメント、人物、話者でフィルター処理することができます。 ビデオのトランスクリプトや OCR のみを表示することもできます。    

    ![洞察](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)

4. プレイリストを作成します。

    プレイリストの行の追加/削除を行うには、**+**/**-** を押します。

5. プレイリストをプレビューします。

    プレイリストの作成が完了したら、**[プレビュー]** を押します。
6. プレイリストを公開します。

    プレイリストをプレビューしたら、公開できます。

    公開したプレイリストは、ビデオの分析情報のリストに追加されます。


## <a name="next-steps"></a>次の手順 

新しいプレイリストを作成したら、次のいずれかのトピックの説明に従ってプレイリストの処理を続行できます。 

- [Video Indexer REST API を使用してコンテンツを処理する](video-indexer-use-apis.md)
- [アプリケーションにビジュアル ウィジェットを埋め込む](video-indexer-embed-widgets.md)

## <a name="see-also"></a>関連項目

[Video Indexer の概要](video-indexer-overview.md) 
