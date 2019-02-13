---
title: Azure Media Services のストリーミング ロケーター | Microsoft Docs
description: この記事では、ストリーミング ロケーターとは何か、および Azure Media Services でそれらを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: be66dcf8115258b6f593ec913e75785a3f8dbe1f
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2019
ms.locfileid: "55743482"
---
# <a name="streaming-locators"></a>ストリーミング ロケーター

出力アセット内のビデオをクライアントが再生できるようにするには、[ストリーミング ロケーター](https://docs.microsoft.com/rest/api/media/streaminglocators)を作成し、ストリーミング URL をビルドする必要があります。 .NET のサンプルについては、「[Get a Streaming Locator](stream-files-tutorial-with-api.md#get-a-streaming-locator)」(ストリーミング ロケーターを取得する) を参照してください。

**ストリーミング ロケーター** を作成するプロセスは発行と呼ばれます。 既定では、**ストリーミング ロケーター** は API 呼び出しを行うとすぐに有効になり、省略可能な開始時刻と終了時刻を構成しない限り、削除されるまで存続します。 

**ストリーミング ロケーター**を作成するときに、[アセット](https://docs.microsoft.com/rest/api/media/assets)名と[ストリーミング ポリシー](https://docs.microsoft.com/rest/api/media/streamingpolicies)名を指定する必要があります。 定義済みのストリーミング ポリシーまたは作成済みのカスタム ポリシーのいずれかを使用できます。 現在利用できる定義済みのポリシーは次のとおりです。'Predefined_DownloadOnly'、'Predefined_ClearStreamingOnly'、'Predefined_DownloadAndClearStreaming'、'Predefined_ClearKey'、'Predefined_MultiDrmCencStreaming' および 'Predefined_MultiDrmStreaming' カスタム ストリーミング ポリシーの使用時には、お使いの Media Service アカウント用にこうしたポリシーの限られたセットを設計し、同じオプションとプロトコルが必要な場合は常に、ストリーミング ロケーターに対して同じセットを再利用してください。 

ストリームで暗号化オプションを指定する場合は、コンテンツ キーを Media Services の Key Delivery コンポーネント経由でエンド クライアントへ配信する方法を構成する[コンテンツ キー ポリシー](https://docs.microsoft.com/rest/api/media/contentkeypolicies)を作成します。 ストリーミング ロケーターを**コンテンツ キー ポリシー**とコンテンツ キーに関連付けます。 Media Services でキーを自動生成させることができます。 次の .NET の例では、Media Services v3:[EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted) でトークン制限を使用して AES 暗号化を構成する方法を示します。 **コンテンツ キー ポリシー**は更新が可能なため、キーのローテーションを行う必要がある場合には、ポリシーを更新することができます。 キー配信キャッシュでポリシーが更新されて、その更新されたポリシーが取得されるまでには、最大 15 分かかる場合があります。 ストリーミング ロケーターごとに新しいコンテンツ キー ポリシーを作成しないことをお勧めします。 同じオプションが必要な場合は常に、既存のポリシーを再利用するようにしてください。

> [!IMPORTANT]
> * Datetime 型の**ストリーミング ロケーター**のプロパティは、常に UTC 形式です。
> * お使いの Media Service アカウント用にポリシーの限られたセットを設計し、同じオプションが必要な場合は常に、ストリーミング ロケーターに対して同じセットを再利用してください。 

## <a name="filtering-ordering-paging"></a>フィルター処理、順序付け、ページング

「[Media Services エンティティのフィルター処理、順序付け、ページング](entities-overview.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* [チュートリアル:.NET を使用してビデオをアップロード、エンコード、ストリーム配信する](stream-files-tutorial-with-api.md)
* [DRM 動的暗号化とライセンス配信サービスの使用](protect-with-drm.md)
