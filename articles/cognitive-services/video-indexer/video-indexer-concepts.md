---
title: Azure Video Indexer の概念 | Microsoft Docs
description: このトピックでは、Video Indexer サービスのいくつかの概念について説明します。
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 224c8b05027f51fb99c8d58be34c3604032c0f77
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399497"
---
# <a name="video-indexer-concepts"></a>Video Indexer の概念
 
このトピックでは、Video Indexer サービスのいくつかの概念について説明します。
    
## <a name="summarized-insights"></a>要約された分析情報

要約された分析情報には、データ (顔、キーワード、感情) の集約されたビューが含まれます。 たとえば、数千もの時間範囲を一つ一つ確認してどの顔が出現するかを調べなくても、要約された分析情報に、すべての顔と、それぞれの顔が出現する時間範囲、その顔が表示される時間の割合 (%) が表示されます。

## <a name="topicskeywords"></a>トピック/キーワード

トピック/キーワードは、Video Indexer がテキストから抽出するキー フレーズのリストに含まれます。 たとえば、Scott Guthrie のビデオには、セキュリティ、Azure、Microsoft Cloud、収益などのトピック/キーワードが含まれる可能性があります。

## <a name="sentiments"></a>センチメント

Video Indexer がトランスクリプトを分析するときは、センチメントも検出します。 たとえば、"これは非常に魅力的なイベントだ" は肯定的なセンチメントです。

## <a name="time-range-vs-adjusted-time-range"></a>時間範囲と調整された時間範囲

TimeRange は、元のビデオでの時間範囲です。 AdjustedTimeRange は、現在のプレイリストを基準とした時間範囲です。 複数のビデオの複数の行からプレイリストを作成できるため、1 時間のビデオのうち 1 行だけ (たとえば 10:00-10:15) を使用する場合もあります。 その場合、時間範囲が 10:00-10:15 だが、adjustedTimeRange は 00:00-00:15 である 1 行を含むプレイリストが作成されます。
 
## <a name="blocks"></a>Blocks

ブロックは、データ内の移動を簡単にするために使用されます。 たとえば、話者が変わったり、長い休止があるときにブロックが分割されます。

## <a name="next-steps"></a>次の手順

作業の開始方法の詳細については、「[サインアップして最初のビデオをアップロードする方法](video-indexer-get-started.md)」を参照してください。

## <a name="see-also"></a>関連項目

[Video Indexer の概要](video-indexer-overview.md)
