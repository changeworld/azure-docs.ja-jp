---
title: Azure Media Services のレガシ コンポーネント | Microsoft Docs
description: このトピックでは、Azure Media Services のレガシ コンポーネントについて説明します。
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.openlocfilehash: d86a77e724bffeaea6cb39ffc1a20e8737ef6d81
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835540"
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
| Azure Media Indexer | 2020 年 1 月 1 日 | このメディア プロセッサは、[Media Services v3 AudioAnalyzerPreset Basic モード](../latest/analyze-video-audio-files-concept.md)に置き換えられます。 詳細については、[Azure Media Indexer 2 から Azure Video Analyzer for Media (旧称 Video Indexer) への移行](migrate-indexer-v1-v2.md)に関する記事を参照してください。 |
| Azure Media Indexer 2 | 2023 年 3 月 1 日 | このメディア プロセッサは、[Media Services v3 AudioAnalyzerPreset Basic モード](../latest/analyze-video-audio-files-concept.md)に置き換えられます。 詳細については、[Azure Media Indexer 2 から Azure Video Analyzer for Media (旧称 Video Indexer) への移行](migrate-indexer-v1-v2.md)に関する記事を参照してください。 |
| モーション検出 | 2020 年 6 月 1 日|現時点では置換する計画はありません。 |
| ビデオ要約 |2020 年 6 月 1 日|現時点では置換する計画はありません。|
| ビデオ光学式文字認識 | 2020 年 6 月 1 日 |このメディア プロセッサは、Azure Video Analyzer for Media に置き換えられました。 また、[Azure Media Services v3 API](../latest/analyze-video-audio-files-concept.md) の使用も検討してください。 <br/>「[Azure Media Services v3 プリセットと Video Analyzer for Media を比較する](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md)」を参照してください。 |
| Face Detector | 2020 年 6 月 1 日 | このメディア プロセッサは、Azure Video Analyzer for Media に置き換えられました。 また、[Azure Media Services v3 API](../latest/analyze-video-audio-files-concept.md) の使用も検討してください。 <br/>「[Azure Media Services v3 プリセットと Video Analyzer for Media を比較する](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md)」を参照してください。 |
| Content Moderator | 2020 年 6 月 1 日 |このメディア プロセッサは、Azure Video Analyzer for Media に置き換えられました。 また、[Azure Media Services v3 API](../latest/analyze-video-audio-files-concept.md) の使用も検討してください。 <br/>「[Azure Media Services v3 プリセットと Video Analyzer for Media を比較する](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md)」を参照してください。 |
| メディア エンコーダー Premium ワークフロー | 2024 年 2 月 29 日 | AMS v2 API では、Premium Encoder がサポートされなくなりました。 以前に HEVC エンコードにワークフロー ベースの Premium Encoder を使用していた場合は、HEVC エンコードのサポートを含む[新しい v3 Standard Encoder](../latest/encode-media-encoder-standard-formats-reference.md) に移行する必要があります。 <br/> Premium Encoder の高度なワークフロー機能が必要な場合は、[Imagine Communications](https://imaginecommunications.com/)、[Telestream](https://telestream.net)、[Bitmovin](https://bitmovin.com) から Azure の高度なエンコード パートナーの使用を開始することをお勧めします。 |

## <a name="next-steps"></a>次のステップ

[Media Services v2 から v3 への移行のガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)
