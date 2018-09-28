---
title: Video Indexer の概念
titlesuffix: Azure Cognitive Services
description: このトピックでは、Video Indexer サービスのいくつかの概念について説明します。
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: 740f13e90397650ed9274937b16254e46c6deced
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984123"
---
# <a name="video-indexer-concepts"></a>Video Indexer の概念
 
この記事では、Video Indexer サービスのいくつかの概念について説明します。
    
## <a name="summarized-insights"></a>要約された分析情報

要約された分析情報には、データ (顔、トピック、感情) の集約されたビューが含まれます。 たとえば、数千もの時間範囲を一つ一つ確認してどの顔が出現するかを調べなくても、要約された分析情報に、すべての顔と、それぞれの顔が出現する時間範囲、その顔が表示される時間の割合 (%) が表示されます。

## <a name="time-range-vs-adjusted-time-range"></a>時間範囲と調整された時間範囲

TimeRange は、元のビデオでの時間範囲です。 AdjustedTimeRange は、現在のプレイリストを基準とした時間範囲です。 複数のビデオの複数の行からプレイリストを作成できるため、1 時間のビデオのうち 1 行だけ (たとえば 10:00-10:15) を使用する場合もあります。 その場合、時間の範囲は 10:00-10:15 であっても、adjustedTimeRange が 00:00-00:15 である 1 行を含むプレイリストが作成されます。
 
## <a name="blocks"></a>Blocks

ブロックは、データ内の移動を簡単にするために使用されます。 たとえば、話者が変わったり、長い休止があるときにブロックが分割されます。

## <a name="next-steps"></a>次の手順

作業の開始方法の詳細については、「[サインアップして最初のビデオをアップロードする方法](video-indexer-get-started.md)」を参照してください。

## <a name="see-also"></a>関連項目

[Video Indexer の概要](video-indexer-overview.md)
