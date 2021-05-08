---
title: コンテンツ保護の移行ガイダンス
description: この記事では、Azure Media Services v2 から v3 への移行に役立つコンテンツ保護のシナリオベースのガイダンスを提供します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 04/05/2021
ms.author: inhenkel
ms.openlocfilehash: a481759da3f1e7d67accdca7b4322db53abbcb0c
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490949"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>コンテンツ保護のシナリオベースの移行ガイダンス

![移行ガイドのロゴ](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![移行の手順 2](./media/migration-guide/steps-4.svg)

この記事では、v2 API から新しい Azure Media Services v3 API にコンテンツ保護を移行するユースケースに関する詳細とガイダンスを提供します。

## <a name="protect-content-in-v3-api"></a>v3 API でのコンテンツの保護

新しい v3 API での[マルチキー](architecture-design-multi-drm-system.md)機能のサポートを使用します。

具体的な手順については、この記事の最後に記載されている「コンテンツ保護の概念、チュートリアル、およびハウツー ガイド」をご覧ください。

> [!NOTE]
> この記事の残りの部分では、v2 コンテンツ保護を .NET で v3 に移行する方法について説明します。  別の言語または方法の手順やサンプル コードが必要な場合は、このページの GitHub の問題を作成してください。

## <a name="v3-visibility-of-v2-assets-streaminglocators-and-properties"></a>v2 資産、StreamingLocators、およびプロパティの v3 可視性

v2 API では、`Assets`、`StreamingLocators`、および `ContentKeys` を使用して、ストリーミング コンテンツが保護されていました。 v3 API に移行すると、v2 API の `Assets`、`StreamingLocators`、および `ContentKeys` がすべて v3 API に自動的に公開され、そのすべてのデータにアクセスできるようになります。

v2 で作成された v2 エンティティのプロパティを、v3 API を使用して *更新* することはできません。

v2 エンティティに格納されているコンテンツを更新、変更、または修正する必要がある場合は、v2 API を使用して更新するか、新しい v3 API エンティティを作成してそれらを移行してください。

## <a name="asset-identifier-differences"></a>資産識別子の違い

移行するには、v2 資産からプロパティまたはコンテンツ キーにアクセスする必要があります。  v2 API では `AssetId` をプライマリ ID キーとして使用しますが、新しい v3 API ではエンティティの *Azure Resource Management 名* をプライマリ ID として使用することを理解するのが重要です。  (v2 `Asset.Name` プロパティは一意の識別子として使用されません。) v3 API を使用すると、v2 資産名は `Asset.Description` として表示されるようになりました。

たとえば、以前に ID が `nb:cid:UUID:8cb39104-122c-496e-9ac5-7f9e2c2547b8` の v2 資産がある場合、識別子は GUID `8cb39104-122c-496e-9ac5-7f9e2c2547b8` の末尾になります。 これは、v3 API を使用して v2 資産を一覧表示するときに表示されます。

v2 API を使用して作成および発行されたすべての資産には、v3 API では `ContentKeyPolicy` と `ContentKey` の両方が存在し、`StreamingPolicy` の既定のコンテンツ キー ポリシーは使用されません。

詳細については、[コンテンツ キー ポリシー](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept)のドキュメントと [ストリーミング ポリシー](https://docs.microsoft.com/azure/media-services/latest/stream-streaming-policy-concept)のドキュメントをご覧ください。

## <a name="use-azure-media-services-explorer-amse-v2-and-amse-v3-tools-side-by-side"></a>Azure Media Services Explorer (AMSE) v2 と AMSE v3 ツールをサイドバイサイドで使用する

[v2 Azure Media Services Explorer ツール](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)と [v3 Azure Media Services Explorer ツール](https://github.com/Azure/Azure-Media-Services-Explorer)を一緒に使用すると、v2 API を使用して作成および公開された資産のデータを横並びで比較することができます。 プロパティはすべて表示されるはずですが、場所は変わります。

## <a name="use-the-net-content-protection-migration-sample"></a>.NET コンテンツ保護移行のサンプルを使用する

Media Services コード サンプルでは、ContentProtection の下にある [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration) を使用して、資産識別子の違いを比較するコード サンプルを見つけることができます。

## <a name="list-the-streaming-locators"></a>ストリーミング ロケーターを一覧表示する

v2 API で作成された資産に関連付けられた `StreamingLocators` は、Asset エンティティで新しい v3 メソッド [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) を使用してクエリできます。  また、[ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true) の .NET クライアント SDK バージョンを参照します。

`ListStreamingLocators` メソッドの結果、`StreamingPolicyName` と共にロケーターの `Name` と `StreamingLocatorId` が提供されます。

## <a name="find-the-content-keys"></a>コンテンツ キーを検索する

`StreamingLocators` ととも使用される `ContentKeys` を検索するには、[StreamingLocator.ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true) メソッドを呼び出すことができます。  

v3 API でのコンテンツ保護の詳細については、「[Media Services 動的暗号化を使用してコンテンツを保護する](https://docs.microsoft.com/azure/media-services/latest/drm-content-protection-concept)」の記事を参照してください。

## <a name="change-the-v2-contentkeypolicy-keeping-the-same-contentkey"></a>同じ ContentKey を維持したまま v2 ContentKeyPolicy を変更する

まず、v2 SDK を使用して、資産で発行の取り消し (すべてのストリーミング ロケーターを削除) をする必要があります。 次に手順を示します。

1. ロケーターを削除します。
1. `ContentKeyAuthorizationPolicy` のリンクを解除します。
1. `AssetDeliveryPolicy` のリンクを解除します。
1. `ContentKey` のリンクを解除します。
1. `ContentKey` を削除します。
1. 必要な特定のコンテンツ キー ID とキー値を指定して、v3 `StreamingPolicy` と `ContentKeyPolicy` を使用して、新しい `StreamingLocator` を v3 で作成します。

> [!NOTE]
> v3 API を使用して v2 のロケーターを削除することはできますが、この操作によって、使用されるコンテンツ キーおよびコンテンツ キー ポリシーが (これらが v2 API で作成された場合) 削除されるわけではありません。

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>コンテンツ保護の概念、チュートリアル、およびハウツー ガイド

### <a name="concepts"></a>概念

- [Media Services 動的暗号化を使用してコンテンツを保護する](drm-content-protection-concept.md)
- [アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計](architecture-design-multi-drm-system.md)
- [Media Services v3 と PlayReady ライセンス テンプレート](drm-playready-license-template-concept.md)
- [Widevine ライセンス テンプレートを使用した Media Services v3 の概要](drm-widevine-license-template-concept.md)
- [Apple FairPlay ライセンスの要件と構成](drm-fairplay-license-overview.md)
- [ストリーミング ポリシー](stream-streaming-policy-concept.md)
- [コンテンツ キー ポリシー](drm-content-key-policy-concept.md)

### <a name="tutorials"></a>チュートリアル

[クイック スタート:ポータルを使用してコンテンツを暗号化する](drm-encrypt-content-how-to.md)

### <a name="how-to-guides"></a>ハウツー ガイド

- [既存のポリシーから署名キーを取得する](drm-get-content-key-policy-dotnet-how-to.md)
- [Media Services v3 を使用した iOS 用のオフラインの FairPlay ストリーミング](drm-offline-fairplay-for-ios-concept.md)
- [Media Services v3 を使用した Android 用のオフラインの Widevine ストリーミング](drm-offline-widevine-for-android.md)
- [Media Services v3 を使用した Windows 10 用のオフラインの PlayReady ストリーミング](drm-offline-playready-streaming-for-windows-10.md)

## <a name="samples"></a>サンプル

- [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration)
- [V2 と V3 のコードをコード サンプルで比較する](migrate-v-2-v-3-migration-samples.md)こともできます。

## <a name="tools"></a>ツール

- [v3 Azure Media Services Explorer ツール](https://github.com/Azure/Azure-Media-Services-Explorer)
- [v2 Azure Media Services Explorer ツール](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
