---
title: Azure Video Indexer を使用して既存のビデオからビデオの分析情報を作成する | Microsoft Docs
description: このトピックでは、他のビデオに基づいてビデオの分析情報を作成し、公開する方法を示します。
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: 161a47f72a0f8038a515c09f25ec2a8e8a520547
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390842"
---
# <a name="create-highlights-from-existing-videos"></a>既存のビデオからハイライトを作成する

このトピックでは、他のビデオに基づいてビデオの分析情報を作成し、公開する方法を示します。

1. [Video Indexer](https://api-portal.videoindexer.ai/) アカウントにサインインします。
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
