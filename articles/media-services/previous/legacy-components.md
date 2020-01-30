---
title: Azure Media Services のレガシ コンポーネント | Microsoft Docs
description: このトピックでは、Azure Media Services のレガシ コンポーネントについて説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2019
ms.author: juliako
ms.openlocfilehash: 457f5b8eb0a740800f9cc0fa69bd554f0679ae9a
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509174"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services のレガシ コンポーネント

Media Services のコンポーネントは、時間の経過と共に着実に改善され機能が強化されてきました。 その結果、いくつかのレガシ コンポーネントは廃止されています。 次の記事では、レガシ コンポーネントから最新のコンポーネントにアプリケーションを移行する手順を確認できます。
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>レガシ コンポーネントと移行のガイダンスの提供終了予定

お知らせしているように *Windows Azure Media Encoder* (WAME) と *Azure Media Encoder* (AME) のメディア プロセッサは非推奨となっており、 これらのプロセッサは、2020 年 3 月 1 日には廃止される予定です。

* [Windows Azure Media Encoder から Media Encoder Standard に移行する](migrate-windows-azure-media-encoder.md)
* [Azure Media Encoder から Media Encoder Standard に移行する](migrate-azure-media-encoder.md)

また、以下の Media Analytics メディア プロセッサの提供終了もお知らせします。 
 
|メディア プロセッサ名|提供終了日|その他のメモ|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| 2020 年 10 月 1 日|このメディア プロセッサは、[Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) に置き換えられます。 詳細については、「[Media Indexer と Media Indexer 2 から Video Indexer に移行する](migrate-indexer-v1-v2.md)」をご覧ください。|
|[Azure Media Indexer](media-services-index-content.md)|2020 年 10 月 1 日|このメディア プロセッサは、[Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) に置き換えられます。 詳細については、「[Media Indexer と Media Indexer 2 から Video Indexer に移行する](migrate-indexer-v1-v2.md)」をご覧ください

## <a name="next-steps"></a>次のステップ

[Media Services v2 から v3 への移行のガイダンス](../latest/migrate-from-v2-to-v3.md)
