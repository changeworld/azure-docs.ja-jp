---
title: Media Services のコンテンツ キー ポリシー - Azure | Microsoft Docs
description: この記事では、コンテンツ キー ポリシーとは何か、および Azure Media Services でそれらを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/22/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 68778cea51144ec33efd4d5843a51b489ea17ca4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155737"
---
# <a name="content-key-policies"></a>コンテンツ キー ポリシー

Media Services では、Advanced Encryption Standard (AES-128) または主要な 3 つのデジタル著作権管理 (DRM) システム (Microsoft PlayReady、Google Widevine、および Apple FairPlay) によって動的に暗号化されたライブまたはオンデマンドのコンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM (PlayReady、Widevine、FairPlay) ライセンスを配信するためのサービスも提供しています。

ストリームで暗号化オプションを指定するには、[ストリーミング ポリシー](streaming-policy-concept.md)を作成し、それを[ストリーミング ロケーター](streaming-locators-concept.md)に関連付ける必要があります。 [コンテンツ キー ポリシー](https://docs.microsoft.com/rest/api/media/contentkeypolicies)を作成して ([資産](assets-concept.md)へのアクセスをセキュリティで保護する) コンテンツ キー をエンド クライアントに配信する方法を構成する必要があります。 **コンテンツ キー ポリシー**は、**ストリーミング ロケーター**にも関連付けられます。 構成が指定されたキーをクライアントに配信するために満たす必要がある要件 (制限) をコンテンツ キー ポリシーに設定する必要があります。 

Media Services にコンテンツ キーを自動生成させることをお勧めします。 通常、存続期間の長いキーを使用し、**Get** でポリシーの存在を確認します。 キーを取得するには、別のアクション メソッドを呼び出してシークレットまたは資格情報を取得する必要があります。次の例を参照してください。

**コンテンツ キー ポリシー**は更新可能です。 たとえば、キーのローテーションを行う必要がある場合には、ポリシーを更新できます。 既存のポリシーにあるプライマリ検証キーと代替検証キーのリストを更新できます。 キー配信キャッシュでポリシーが更新されて、その更新されたポリシーが取得されるまでには、最大 15 分かかる場合があります。 

> [!IMPORTANT]
> * Datetime 型である**コンテンツ キー ポリシー**のプロパティは、常に UTC 形式です。
> * お使いの Media Service アカウント用にポリシーの限られたセットを設計し、同じオプションが必要な場合は常に、ストリーミング ロケーターに対して同じセットを再利用してください。 詳細については、「[クォータと制限](limits-quotas-constraints.md)」をご覧ください。

## <a name="example"></a>例

キーを取得するには、次の例に示すように **GetPolicyPropertiesWithSecretsAsync** を使用します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="filtering-ordering-paging"></a>フィルター処理、順序付け、ページング

「[Media Services エンティティのフィルター処理、順序付け、ページング](entities-overview.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* [AES-128 動的暗号化とキー配信サービスの使用](protect-with-aes128.md)
* [DRM 動的暗号化とライセンス配信サービスの使用](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
