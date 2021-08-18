---
title: Azure Video Analyzer for Media (旧称 Video Indexer) の分析情報を表示および編集する - Azure
titlesuffix: Azure Video Analyzer for Media
description: この記事では、Azure Video Analyzer for Media (旧称 Video Indexer) の分析情報を表示および編集する方法について説明します。
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 7cbe14a10f18f44831ec360059ae608073ad3a99
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2021
ms.locfileid: "112115228"
---
# <a name="view-and-edit-video-analyzer-for-media-insights"></a>Video Analyzer for Media の分析情報を表示および編集する

このトピックでは、Azure Video Analyzer for Media (旧称 Video Indexer) の分析情報を表示および編集する方法について説明します。

1. [Video Analyzer for Media](https://www.videoindexer.ai/) Web サイトに移動して、サインインします。
2. Video Analyzer for Media の分析情報を作成するビデオを見つけます。 詳細については、「[ビデオ内の正確な場面を見つける](video-indexer-search.md)」を参照してください。
3. **[再生]** を押します。

    ビデオの要約された分析情報がページに表示されます。 

    ![洞察](./media/video-indexer-view-edit/video-indexer-summarized-insights.png)

4. ビデオの要約された分析情報を調べます。 

    要約された分析情報には、データ (顔、キーワード、センチメント) の集約されたビューが表示されます。 たとえば、人物の顔と、それぞれの顔が出現する時間範囲、その顔が表示される時間の割合 (%) を確認できます。

    プレーヤーと分析情報は同期されます。 たとえば、キーワードやトランスクリプトの行をクリックすると、ビデオのその場面がプレーヤーで表示されます。 アプリケーション内で、プレーヤー/分析情報の表示と同期を実現できます。 詳細については、[アプリケーションへの Azure Indexer ウィジェットの埋め込み](video-indexer-embed-widgets.md)に関する記事を参照してください。 

## <a name="next-steps"></a>次のステップ

[ビデオの詳細な分析情報を使用する](use-editor-create-project.md)

## <a name="see-also"></a>関連項目

[Video Analyzer for Media の概要](video-indexer-overview.md)

