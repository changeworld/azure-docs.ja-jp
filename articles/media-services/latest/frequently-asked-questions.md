---
title: Azure Media Services v3 のよく寄せられる質問 | Microsoft Docs
description: この記事では、Azure Media Services v3 のよく寄せられる質問に対する回答を提供します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/05/2019
ms.author: juliako
ms.openlocfilehash: be4c08bc31c8811655230ab89b48271f4c2b3164
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756582"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Azure Media Services v3 のよく寄せられる質問

この記事では、Azure Media Services (AMS) v3 のよく寄せられる質問に対する回答を提供します。

## <a name="v3-apis"></a>v3 API

### <a name="how-do-i-configure-media-reserved-units"></a>メディア占有ユニットの構成方法を教えてください。

Media Services v3 または Video Indexer によってトリガーされる音声分析と動画分析ジョブでは、お使いのアカウントを 10 個の S3 の MRU でプロビジョニングすることを強くお勧めします。 10 個を超える S3 の MRU が必要な場合は、[Azure portal](https://portal.azure.com/) を使用してサポート チケットを開いてください。

詳細については、「[CLI を使用したメディア処理のスケーリング](media-reserved-units-cli-how-to.md)」を参照してください。

### <a name="what-is-the-recommended-method-to-process-videos"></a>推奨される動画の処理方法は?

動画を指す HTTP(s) URL を使用してジョブを送信することをお勧めします。 詳細については、「[HTTP(s) の取り込み](job-input-from-http-how-to.md)」に関するページを参照してください。 処理する前に入力動画を使用して資産を作成する必要はありません。

### <a name="how-does-pagination-work"></a>改ページはどのように機能しますか?

Media Services では、OData をサポートするリソースに対する $top はサポートされますが、$top に渡される値は 1000 未満である必要があります (改ページのページ サイズなど)。

これにより、$top を使用して、項目の小さなサンプル (最近使った 100 件の項目など) を取得したり、改ページ位置の自動修正を使用して、すべての項目のページングを行ったりすることができます。 

Media Services では、ユーザーが指定したページ サイズでのデータのページングはサポートされません。

詳細については、「[フィルター処理、順序付け、ページングのサポート](entities-overview.md)」を参照してください。

### <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Media Services v3 ではエンティティをどのように取得するのですか?

v3 は、**Azure Resource Manager** 上に構築された管理と操作の両方の機能を公開する、統一された API サーフェスに基づいています。 **Azure Resource Manager** に従って、リソース名は常に一意となります。 そのため、リソースには一意識別子の文字列 (GUID など) を使用できます。

## <a name="live-streaming"></a>ライブ ストリーミング 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>ライブ ストリーミング中に中断 / 動画またはイメージ スレートを挿入するにはどうすればいいですか?

Media Services v3 のライブ エンコードでは、ライブ ストリーミング中の動画またはイメージ スレートの挿入はまだサポートされていません。 

[ライブ オンプレミス エンコーダー](recommended-on-premises-live-encoders.md)を使用すると、ソースの動画を切り替えることができます。 多くのアプリには、Telestream Wirecast、Switcher Studio (iOS 上)、OBS Studio (無料アプリ) など、ソースを切り替える機能が用意されています。

## <a name="media-services-v2-vs-v3"></a>Media Services v2 対 v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Azure portal を使用して v3 リソースを管理することはできますか?

まだありません。 サポートされている SDK のいずれかを使用することができます。 この文書セットのチュートリアルとサンプルを参照してください。

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3 には AssetFile の概念はありますか?

Storage SDK の依存関係から Media Services を切り離すために、AssetFile は AMS API から削除されました。 現在、Media Services ではなく、Storage でそれに属する情報が保持されます。 

詳細については、「[Media Services v3 に移行する](migrate-from-v2-to-v3.md)」を参照してください。

### <a name="where-did-client-side-storage-encryption-go"></a>クライアント側でのストレージ暗号化は利用できなくなったのですか?

現在、サーバー側のストレージ暗号化 (既定でオンになる) の使用が推奨されるようになりました。 詳細については、「[保存データ向け Azure Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)」をご覧ください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Media Services v3 の概要](media-services-overview.md)
