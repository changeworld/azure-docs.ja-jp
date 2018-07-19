---
title: Azure Media Services v3 のよく寄せられる質問 | Microsoft Docs
description: この記事では、Azure Media Services v3 のよく寄せられる質問に対する回答を提供します。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/29/2018
ms.author: juliako
ms.openlocfilehash: 098a34aba8e5ce23f64d4bb07e3b9622aa2adb8e
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110422"
---
# <a name="azure-media-services-v3-preview-frequently-asked-questions"></a>Azure Media Services v3 (プレビュー) のよく寄せられる質問

この記事では、Azure Media Services (AMS) v3 のよく寄せられる質問に対する回答を提供します。

## <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Azure portal を使用して v3 リソースを管理することはできますか?

まだありません。 サポートされている SDK のいずれかを使用することができます。 この文書セットのチュートリアルとサンプルを参照してください。

## <a name="is-there-an-api-for-configuring-media-reserved-units"></a>メディア占有ユニットを構成するための API はありますか?

Media Services チームは現在、v3 の RU を排除しています。 ただし、必要なサービス作業は完了していません。 それまでは、お客様は Azure portal または AMS v2 API を使用して、RU を設定する必要があります ([メディア処理のスケール設定](../previous/media-services-scale-media-processing-overview.md)に関するページを参照)。 

**VideoAnalyzerPreset**、**AudioAnalyzerPreset**、またはその両方を使用する場合は、Media Services アカウントを 10 個の S3 メディア占有ユニットに設定します。

## <a name="does-v3-asset-have-no-assetfile-concept"></a>V3 資産に AssetFile の概念はないのですか?

Storage SDK の依存関係から Media Services を切り離すために、AssetFile は AMS API から削除されました。 現在、Media Services ではなく、Storage でそれに属する情報が保持されます。 

## <a name="where-did-client-side-storage-encryption-go"></a>クライアント側でのストレージ暗号化は利用できなくなったのですか?

今後はサーバー側でのストレージ暗号化 (既定で有効) をお勧めします。詳細については、「[Azure Storage Service Encryption for Data at Rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)」を参照してください。

## <a name="what-is-the-recommended-upload-method"></a>推奨されるアップロード方法は何ですか?

HTTP(s) の取り込みを使用することをお勧めします。 詳細については、[HTTP(s) の取り込み](job-input-from-http-how-to.md)に関するページを参照してください。

## <a name="how-does-pagination-work"></a>改ページはどのように機能しますか?

Media Services では、OData をサポートするリソースに対する $top はサポートされますが、$top に渡される値は 1000 未満である必要があります (改ページのページ サイズなど)。

これにより、$top を使用して、項目の小さなサンプル (最近使った 100 件の項目など) を取得したり、改ページ位置の自動修正を使用して、すべての項目のページングを行ったりすることができます。 

Media Services では、ユーザーが指定したページ サイズでのデータのページングはサポートされません。

詳細については、「[フィルター処理、順序付け、ページングのサポート](assets-concept.md#filtering-ordering-paging)」を参照してください。

## <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Media Services v3 ではエンティティをどのように取得するのですか?

v3 は、**Azure Resource Manager** 上に構築された管理と操作の両方の機能を公開する、統一された API サーフェスに基づいています。 **Azure Resource Manager** に従って、リソース名は常に一意となります。 そのため、リソース名には一意識別子の文字列 (GUID など) を使用できます。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Media Services v3 の概要](media-services-overview.md)
