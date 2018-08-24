---
title: Video Indexer の分析情報を表示および編集する | Microsoft Docs
description: このトピックでは、Video Indexer の分析情報を表示および編集する方法を示します。
services: cognitive services
documentationcenter: ''
author: juliako
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 797c09d72402cfc1ee2524e7792cc1310a53fb1e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399479"
---
# <a name="view-and-edit-video-indexer-insights"></a>Video Indexer の分析情報の表示と編集

このトピックでは、ビデオの Video Indexer 分析情報を表示および編集する方法を示します。

1. [Video Indexer](https://api-portal.videoindexer.ai/) アカウントにサインインします。
2. Video Indexer の分析情報を作成するビデオを見つけます。 詳細については、「[ビデオ内の正確な場面を見つける](video-indexer-search.md)」を参照してください。
3. **[再生]** を押します。

    ビデオの要約された分析情報がページに表示されます。 

    ![洞察](./media/video-indexer-view-edit/video-indexer-summarized-insights.png)

4. ビデオの要約された分析情報を調べます。 

    要約された分析情報には、データ (顔、キーワード、センチメント) の集約されたビューが表示されます。 たとえば、人物の顔と、それぞれの顔が出現する時間範囲、その顔が表示される時間の割合 (%) を確認できます。

    プレーヤーと分析情報は同期されます。 たとえば、キーワードやトランスクリプトの行をクリックすると、ビデオのその場面がプレーヤーで表示されます。 アプリケーション内で、プレーヤー/分析情報の表示と同期を実現できます。 詳細については、[アプリケーションへの Azure Indexer ウィジェットの埋め込み](video-indexer-embed-widgets.md)に関する記事を参照してください。 

3. Video Indexer の分析情報を編集します。

    ビデオの下にある [編集] を押します。 ビデオの完全な内訳を示したページが表示されます。 内訳はブロックに分割されています。 ブロックは、データ内の移動を簡単にするために使用されています。 たとえば、話者が変わったり、長い休止があるときにブロックが分割されます。 必要な行のみを含む独自のプレイリストを作成できます。 ソース ビデオの特定の部分のみを表示するには、トピック/キーワード、センチメント、人物、話者でフィルター処理することができます。 ビデオのトランスクリプトや OCR のみを表示することもできます。  

    ![洞察](./media/video-indexer-view-edit/video-indexer-create-new-playlist.png)

## <a name="next-steps"></a>次の手順

[他のビデオに基づいて独自の Video Indexer 分析情報を作成する方法を学習します](video-indexer-create-new.md)。

## <a name="see-also"></a>関連項目

[Video Indexer の概要](video-indexer-overview.md)

