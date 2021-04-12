---
title: コンテンツ保護の移行ガイダンス
description: この記事では、Azure Media Services v2 から v3 への移行に役立つコンテンツ保護のシナリオベースのガイダンスを提供します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 74f15fc302a8499e41a1413dd8915e6442d4bbe7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064496"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>コンテンツ保護のシナリオベースの移行ガイダンス

![移行ガイドのロゴ](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![移行の手順 2](./media/migration-guide/steps-4.svg)

この記事では、v2 API から新しい Azure Media Services v3 API にコンテンツ保護を移行するユースケースに関する詳細とガイダンスを提供します。

## <a name="protect-content-in-v3-api"></a>v3 API でのコンテンツの保護

新しい v3 API での[マルチキー](architecture-design-multi-drm-system.md)機能のサポートを使用します。

具体的な手順については、下の「コンテンツ保護の概念、チュートリアル、およびハウツー ガイド」を参照してください。

## <a name="visibility-of-v2-assets-streaminglocators-and-properties-in-the-v3-api-for-content-protection-scenarios"></a>コンテンツ保護シナリオにおける v3 API での v2 資産、StreamingLocators、およびプロパティの表示

v3 API への移行中に、v2 資産の一部のプロパティまたはコンテンツ キーにアクセスする必要が生じることがあります。 1 つの重要な違いとしては、v2 API では **AssetId** をプライマリ ID キーとして使用し、新しい v3 API ではエンティティの Azure Resource Management 名をプライマリ ID として使用します。  v2 の **Asset.Name** プロパティは、通常は一意識別子としては使用されないため、v3 に移行するときに、v2 資産名が **Asset.Description** フィールドに表示されることがわかります。

たとえば、ID が **"nb:cid:UUID:8cb39104-122c-496e-9ac5-7f9e2c2547b8"** の v2 資産が以前存在していた場合、v3 API を使用して古い v2 資産を一覧表示すると、名前が末尾の GUID 部分 (この場合は **"8cb39104-122c-496e-9ac5-7f9e2c2547b8"** ) になります。

v2 API で作成された資産に関連付けられた **StreamingLocators** は、Asset エンティティで新しい v3 メソッド [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) を使用してクエリできます。  また、[ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true) の .NET クライアント SDK バージョンを参照します。

**ListStreamingLocators** メソッドの結果、**StreamingPolicyName** と共にロケーターの **Name** と **StreamingLocatorId** が提供されます。

コンテンツ保護のために **StreamingLocators** で使用される **ContentKeys** を検索するには、[StreamingLocator.ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true) メソッドを呼び出すことができます。  

ｖ2 API を使用して作成および発行されたすべての **資産** には、v3 API でそれらに対して定義された[コンテンツ キー ポリシー](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept)とコンテンツ キーの両方が存在し、[ストリーミング ポリシー](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept)の既定のコンテンツ キー ポリシーは使用されません。

v3 API でのコンテンツ保護の詳細については、「[Media Services 動的暗号化を使用してコンテンツを保護する](https://docs.microsoft.com/azure/media-services/latest/drm-content-protection-concept)」の記事を参照してください。

## <a name="how-to-list-your-v2-assets-and-content-protection-settings-using-the-v3-api"></a>v3 API を使用して v2 の資産とコンテンツ保護設定を一覧表示する方法

v2 API では通常、**Assets**、**StreamingLocators**、および **ContentKeys** を使用して、ストリーミング コンテンツを保護します。
ｖ3 API に移行すると、v2 API の Assets、StreamingLocators、および ContentKeys がすべて v3 API に自動的に公開され、そのすべてのデータにアクセスできるようになります。

## <a name="can-i-update-v2-properties-using-the-v3-api"></a>ｖ3 API を使用して v2 のプロパティを更新できますか。

いいえ。 v2 の StreamingLocators、StreamingPolicies、コンテンツ キー ポリシー、およびコンテンツ キーを使用して作成された v2 エンティティのプロパティを、v3 API を使用して更新することはできません。
v2 エンティティに格納されているコンテンツを更新、変更、または修正する必要がある場合は、v2 API を使用して更新するか、新しい v3 API エンティティを作成してそれらを移行する必要があります。

## <a name="how-do-i-change-the-contentkeypolicy-used-for-a-v2-asset-that-is-published-and-keep-the-same-content-key"></a>v2 資産用に使用される発行済みの ContentKeyPolicy を変更し、同じコンテンツ キーを保持するにはどうすればよいですか。

このような状況では、最初に v2 の SDK を使用して資産での公開取り消し (すべてのストリーミング ロケーターの削除) を行い (ロケーターの削除、コンテンツ キー承認ポリシーのリンク解除、資産の配信ポリシーのリンク解除、コンテンツ キーのリンク解除、コンテンツ キーの削除)、ｖ3 の [StreamingPolicy](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept) と [ContentKeyPolicy](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept) を使用して v3 で新しい **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** を作成します。

**[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** を作成するときに必要な特定のコンテンツ キー ID とキー値を指定する必要があります。

v3 API を使用して v2 のロケーターを削除することはできますが、この操作によって、使用されるコンテンツ キーおよびコンテンツ キー ポリシーは (これらが v2 API で作成された場合) 削除されません。  

## <a name="using-amse-v2-and-amse-v3-side-by-side"></a>AMSE v2 と AMSE v3 を並べて使用

ｖ2 から v3 にコンテンツを移行するときは、[v2 Azure Media Services Explorer ツール](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)と [v3 Azure Media Services Explorer ツール](https://github.com/Azure/Azure-Media-Services-Explorer)を一緒にインストールすることをお勧めします。これにより、v2 API を使用して作成および公開された資産のデータを横並びで表示して比較することができます。 プロパティはすべて表示されるはずですが、場所は少し変わります。  


## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>コンテンツ保護の概念、チュートリアル、およびハウツー ガイド

### <a name="concepts"></a>概念

- [Media Services 動的暗号化を使用してコンテンツを保護する](drm-content-protection-concept.md)
- [アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計](architecture-design-multi-drm-system.md)
- [Media Services v3 と PlayReady ライセンス テンプレート](drm-playready-license-template-concept.md)
- [Widevine ライセンス テンプレートを使用した Media Services v3 の概要](drm-widevine-license-template-concept.md)
- [Apple FairPlay ライセンスの要件と構成](drm-fairplay-license-overview.md)
- [ストリーミング ポリシー](streaming-policy-concept.md)
- [コンテンツ キー ポリシー](drm-content-key-policy-concept.md)

### <a name="tutorials"></a>チュートリアル

[クイック スタート:ポータルを使用してコンテンツを暗号化する](drm-encrypt-content-how-to.md)

### <a name="how-to-guides"></a>ハウツー ガイド

- [既存のポリシーから署名キーを取得する](drm-get-content-key-policy-dotnet-how-to.md)
- [Media Services v3 を使用した iOS 用のオフラインの FairPlay ストリーミング](drm-offline-fairplay-for-ios-concept.md)
- [Media Services v3 を使用した Android 用のオフラインの Widevine ストリーミング](drm-offline-widevine-for-android.md)
- [Media Services v3 を使用した Windows 10 用のオフラインの PlayReady ストリーミング](drm-offline-playready-streaming-for-windows-10.md)

## <a name="samples"></a>サンプル

[V2 と V3 のコードをコード サンプルで比較する](migrate-v-2-v-3-migration-samples.md)こともできます。

## <a name="tools"></a>ツール

- [v3 Azure Media Services Explorer ツール](https://github.com/Azure/Azure-Media-Services-Explorer)
- [v2 Azure Media Services Explorer ツール](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
