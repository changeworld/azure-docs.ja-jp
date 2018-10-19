---
title: Azure Media Services v3 リリース ノート | Microsoft Docs
description: 常に最新の開発情報を把握していただけるよう、この記事では Azure Media Services v3 の最新の更新情報を提供します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/25/2018
ms.author: juliako
ms.openlocfilehash: ed2550c1df4645933fb968c54ee536995c810136
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219331"
---
# <a name="azure-media-services-v3-preview-release-notes"></a>Azure Media Services v3 (プレビュー) リリース ノート 

常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

* 最新のリリース
* 既知の問題
* バグの修正
* 非推奨の機能
* 変更の計画

## <a name="may-07-2018"></a>2018 年 5 月 7 日

### <a name="net-sdk"></a>.Net SDK

.Net SDK には次の機能があります。

1. **Transforms** と **Job**: メディア コンテンツをエンコードまたは分析します。 例については、「[ファイルのストリーミング](stream-files-tutorial-with-api.md)」と「[分析](analyze-videos-tutorial-with-api.md)」を参照してください。
2. **StreamingLocator**: コンテンツのエンドユーザー デバイスへの公開とストリーミングを行います。
3. **StreamingPolicy** と **ContentKeyPolicy**: コンテンツ配信時のキーの配信とコンテンツの保護 (DRM) を構成します。
4. **LiveEvent** と **LiveOutput**: ライブ ストリーミング コンテンツの取り込みとアーカイブを構成します。
5. **Asset**: メディア コンテンツの Azure Storage への格納と発行を行います。 
6. **StreamingEndpoints**: ダイナミック パッケージ、暗号化、およびライブとオンデマンドの両方のメディア コンテンツの構成とストリーミングを行います。

### <a name="known-issues"></a>既知の問題

* ジョブを送信するときに、Azure Blob storage 内にあるファイルへの HTTPS URL、SAS の URL またはパスを使用して、ソース ビデオを取り込むように指定できます。 現時点では、AMS v3 には、HTTPS URL 経由でチャンク転送エンコード処理がサポートされていません。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [概要](media-services-overview.md)
