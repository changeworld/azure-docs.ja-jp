---
title: Azure Media Services v3 リリース ノート | Microsoft Docs
description: 常に最新の開発情報を把握していただけるよう、この記事では Azure Media Services v3 の最新の更新情報を提供します。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: fc6c5ba6cd97c261dd44eade33bf21e8d1b74bf0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782641"
---
# <a name="azure-media-services-v3-preview-release-notes"></a>Azure Media Services v3 (プレビュー) リリース ノート 

常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

* 最新のリリース
* 既知の問題
* バグの修正
* 非推奨になった機能
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

既知の問題:

ソース コンテンツを指す HTTPS URL (JobInputHttp) を使用してジョブを送信する場合は、HTTP サーバーが "HEAD" 要求をサポートしていることを確認してください。 それ以外の場合、ジョブは拒否されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [概要](media-services-overview.md)
