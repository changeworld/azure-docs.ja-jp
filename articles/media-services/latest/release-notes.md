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
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: db68f979239a5783338d99360209ae231a75c936
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945037"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure Media Services v3 リリース ノート 

常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

* 最新のリリース
* 既知の問題
* バグの修正
* 非推奨の機能
* 変更の計画

## <a name="october-2018---ga"></a>2018 年 10 月 - GA

このセクションでは、Azure Media Services (AMS) の 10 月の更新について説明します。

### <a name="rest-v3-ga-release"></a>REST v3 GA リリース

[REST v3 GA リリース](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)には、ライブの追加の API、アカウント/アセット レベルのマニフェスト フィルター、および DRM サポートが含まれています。

#### <a name="azure-resource-management"></a>Azure Resource Management 

Azure Resource Management のサポートにより、管理と操作の API が統一されます (現在はすべて一か所にあります)。

このリリース移行では、Resource Manager テンプレートを使用して、ライブ イベントを作成できます。

#### <a name="improvement-of-asset-operations"></a>アセット操作の向上 

次の機能強化が導入されています。

- HTTP(s) URL または Azure Blob Storage SAS URL からの取り込み。
- アセットで独自のコンテナー名の指定。 
- Azure Functions を使用してカスタム ワークフローを作成するための簡単な出力サポート。

#### <a name="new-transform-object"></a>新しい変換オブジェクト

新しい**変換**オブジェクトによってエンコード モデルが簡略化されます。 この新しいオブジェクトを使用すると、Resource Manager テンプレートおよびプリセットの作成と共有が簡単になります。 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Azure Active Directory 認証と RBAC

Azure AD Authentication とロールベースのアクセス制御 (RBAC) によって、Azure AD のロールまたはユーザーごとのセキュアな変換、LiveEvent、コンテンツ キー ポリシー、またはアセットが実現します。

#### <a name="client-sdks"></a>クライアント SDK  

Media Services v3 でサポートされる言語: .NET Core、Java、Node.js、Ruby、Typescript、Python、Go。

#### <a name="live-encoding-updates"></a>ライブ エンコードの更新

ライブ エンコードには次の更新が導入されています。

- ライブの新しい低待機時間モード (エンド ツー エンドで 10 秒)。
- RTMP サポートの強化 (安定性の向上およびソース エンコーダー サポートの強化)。
- RTMPS のセキュアな取り込み。

    LiveEvent を作成すると、4 つの取り込み URL を取得します。 4 つの取り込み URL はほとんど同じで。ストリーミング トークン (AppId) は同じですが、ポート番号の部分のみが異なります。 URL のうち 2 つは RTMPS のプライマリとバックアップです。 
- 24 時間のコード変換サポート。 
- SCTE35 経由の RTMP での広告シグナル サポートの強化。

#### <a name="improved-event-grid-support"></a>Event Grid サポートの強化

Event Grid のサポートが次のように強化されています。

- Logic Apps および Azure Functions を使用した開発が容易になる Azure EventGrid の統合。 
- エンコーディングやライブ チャンネルなどでイベントをサブスクライブ。

### <a name="cmaf-support"></a>CMAF のサポート

CMAF と 'cbcs' 暗号化のサポート。Apple HLS (iOS 11+) および、CMAF をサポートする MPEG-DASH プレーヤーに対応します。

### <a name="video-indexer"></a>Video Indexer

Video Indexer GA のリリースは 8 月に発表されました。 現在サポートされている機能に関する新しい情報については、「[Video Indexer とは](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)」をご覧ください。 

### <a name="plans-for-changes"></a>変更の計画

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
すべての機能 (ライブ、コンテンツ キー ポリシー、アカウント/アセット フィルター、ストリーミング ポリシーなど) の操作を含む Azure CLI 2.0 モジュールは近日公開されます。 

### <a name="known-issues"></a>既知の問題

アセットまたはアカウント フィルターのプレビュー API を使用していた顧客のみが、次の問題によって影響を受けます。

Media Services v3 CLI または API を使用して 9 月 28 日から 10 月 12 日までにアセットまたはアカウント フィルターを作成した場合、バージョンが競合するため、すべてのアセットとアカウント フィルターを削除してから再作成する必要があります。 

## <a name="may-2018---preview"></a>2018 年 5 月 - プレビュー

### <a name="net-sdk"></a>.Net SDK

.Net SDK には次の機能があります。

* **Transforms** と **Job**: メディア コンテンツをエンコードまたは分析します。 例については、「[ファイルのストリーミング](stream-files-tutorial-with-api.md)」と「[分析](analyze-videos-tutorial-with-api.md)」を参照してください。
* **StreamingLocator**: コンテンツのエンドユーザー デバイスへの公開とストリーミングを行います。
* **StreamingPolicy** と **ContentKeyPolicy**: コンテンツ配信時のキーの配信とコンテンツの保護 (DRM) を構成します。
* **LiveEvent** と **LiveOutput**: ライブ ストリーミング コンテンツの取り込みとアーカイブを構成します。
* **Asset**: メディア コンテンツの Azure Storage への格納と発行を行います。 
* **StreamingEndpoints**: ダイナミック パッケージ、暗号化、およびライブとオンデマンドの両方のメディア コンテンツの構成とストリーミングを行います。

### <a name="known-issues"></a>既知の問題

* ジョブを送信するときに、Azure Blob storage 内にあるファイルへの HTTPS URL、SAS の URL またはパスを使用して、ソース ビデオを取り込むように指定できます。 現時点では、AMS v3 には、HTTPS URL 経由でチャンク転送エンコード処理がサポートされていません。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [概要](media-services-overview.md)
