---
title: Azure Media Services のレガシ コンポーネント | Microsoft Docs
description: このトピックでは、Azure Media Services のレガシ コンポーネントについて説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: d85cbeb99264b5b730fe585fd39f658e6448467f
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515765"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services のレガシ コンポーネント

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

徐々に Media Service コンポーネントを機能強化し、レガシ コンポーネントを廃止しています。 この記事は、レガシ コンポーネントから現行コンポーネントにアプリケーションを移行する際に役立ちます。
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>レガシ コンポーネントと移行のガイダンスの提供終了予定

*Windows Azure Media Encoder* (WAME) と *Azure Media Encoder* (AME) のメディア プロセッサは非推奨となっています。

* [Windows Azure Media Encoder から Media Encoder Standard に移行する](migrate-windows-azure-media-encoder.md)
* [Azure Media Encoder から Media Encoder Standard に移行する](migrate-azure-media-encoder.md)

次の Media Analytics メディア プロセッサは非推奨になっているか、間もなく非推奨になります。

  
 
| **メディア プロセッサ名** | **提供終了日** | **補足メモ** |
| --- | --- | ---|
| Azure Media Indexer 2 | 2020 年 1 月 1 日 | このメディア プロセッサは、[Media Services v3 AudioAnalyzerPreset Basic モード](../latest/analyzing-video-audio-files-concept.md)に置き換えられます。 詳細については、「[Media Indexer と Media Indexer 2 から Video Indexer に移行する](migrate-indexer-v1-v2.md)」をご覧ください。 |
| Azure Media Indexer | 2023 年 3 月 1 日 | このメディア プロセッサは、[Media Services v3 AudioAnalyzerPreset Basic モード](../latest/analyzing-video-audio-files-concept.md)に置き換えられます。 詳細については、「[Media Indexer と Media Indexer 2 から Video Indexer に移行する](migrate-indexer-v1-v2.md)」をご覧ください。 |
| モーション検出 | 2020 年 6 月 1 日|現時点では置換する計画はありません。 |
| ビデオ要約 |2020 年 6 月 1 日|現時点では置換する計画はありません。|
| ビデオ光学式文字認識 | 2020 年 6 月 1 日 |このメディア プロセッサは、[Azure Media Services Video Indexer](../video-indexer/index.yml) に置き換えられます。 また、[Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md) の使用も検討してください。 <br/>「[Azure Media Services v3 プリセットと Video Indexer の比較](../video-indexer/compare-video-indexer-with-media-services-presets.md)」をご覧ください。 |
| Face Detector | 2020 年 6 月 1 日 | このメディア プロセッサは、[Azure Media Services Video Indexer](../video-indexer/index.yml) に置き換えられます。 また、[Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md) の使用も検討してください。 <br/>「[Azure Media Services v3 プリセットと Video Indexer の比較](../video-indexer/compare-video-indexer-with-media-services-presets.md)」をご覧ください。 |
| Content Moderator | 2020 年 6 月 1 日 |このメディア プロセッサは、[Azure Media Services Video Indexer](../video-indexer/index.yml) に置き換えられます。 また、[Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md) の使用も検討してください。 <br/>「[Azure Media Services v3 プリセットと Video Indexer の比較](../video-indexer/compare-video-indexer-with-media-services-presets.md)」をご覧ください。 |

## <a name="next-steps"></a>次のステップ

[Media Services v2 から v3 への移行のガイダンス](../latest/migrate-from-v2-to-v3.md)
