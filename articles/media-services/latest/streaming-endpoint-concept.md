---
title: Azure Media Services のストリーミング エンドポイント | Microsoft Docs
description: この記事では、ストリーミング エンドポイントとは何か、および Azure Media Services でそれらを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.openlocfilehash: 8f3bcc3c631f17880c66e482234effcc4ea6424d
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744551"
---
# <a name="streaming-endpoints"></a>ストリーミング エンドポイント

Microsoft Azure Media Services (AMS) では、[ストリーミング エンドポイント](https://docs.microsoft.com/rest/api/media/streamingendpoints) エンティティは、コンテンツをクライアント プレーヤー アプリケーションや、再配布のための Content Delivery Network (CDN) に直接配信するストリーミング サービスを表します。 ストリーミング エンドポイント サービスからの送信ストリームには、Media Services アカウントのライブ ストリームやビデオ オンデマンドの資産があります。 Media Services アカウントを作成すると、**既定**のストリーミング エンドポイントが停止状態で作成されます。 **既定**のストリーミング エンドポイントは削除できません。 アカウントで追加のストリーミング エンドポイントを作成できます。 ビデオのストリーミングを開始するには、ビデオをストリーミングするストリーミング エンドポイントを開始する必要があります。 

## <a name="streamingendpoint-types"></a>StreamingEndpoint の型  

**StreamingEndpoint** には**Standard** と **Premium** の 2 つの型があります。 型は、ストリーミング エンドポイントに割り当てられたスケール ユニットの数 (`scaleUnits`) によって定義されます。 

次の表で、型について説明します。  

|type|スケール ユニット|説明|
|--------|--------|--------|  
|**Standard ストリーミング エンドポイント** (推奨)|0|**Standard** 型は、実質的にすべてのストリーミング シナリオとオーディエンス サイズで推奨されるオプションです。 **Standard** 型は、送信帯域幅を自動的にスケールします。 <br/>要件が非常に厳しいユーザー向けに、Media Services は **Premium** ストリーミング エンドポイントを提供しています。これは、多数のインターネット ユーザー向けに容量をスケールアウトするために使用できます。 同時に視聴するユーザーが多数であることが予想される場合は、amsstreaming@microsoft.com までお問い合わせください。**Premium** 型に移行する必要があるかどうかガイダンスを提供します。 |
|**Premium ストリーミング エンドポイント**|>0|**Premium** ストリーミング エンドポイントは専用のスケーラブルな帯域幅の容量を提供するため、高度なワークロードに適しています。 **Premium** 型には、`scaleUnits` を調整することで移行します。 `scaleUnits` は 200 Mbps 単位で購入できる専用の送信容量を提供します。 **Premium** 型を使用するときは、有効になっている各ユニットがアプリケーションに追加の帯域幅容量を提供します。 |

## <a name="working-with-cdn"></a>CDN の操作

CDN はほとんどのケースで有効にしておくことをお勧めします。 ただし、CDN はコンカレンシーに合わせて最適なスケーリングを行うため、最大コンカレンシーが 500 に満たないことが予想される場合は、CDN を無効にすることをお勧めします。

### <a name="detailed-explanation-of-how-caching-works"></a>キャッシュのしくみに関する詳しい説明

CDN を追加するケースに、具体的な帯域幅の値はありません。CDN が有効なストリーミング エンドポイントに必要な帯域幅の量は場合によって変わるためです。 多くはコンテンツの種類、その人気、ビットレート、プロトコルによって変わります。 CDN は要求されたもののみをキャッシュします。 つまり、ビデオ フラグメントがキャッシュされている限り、人気のコンテンツは CDN から直接配信されます。 通常、多くのユーザーがまったく同じものを視聴するライブ コンテンツは、キャッシュされる可能性が高くなります。 人気のコンテンツもあればそうでないものもあるオンデマンドのコンテンツは、やや複雑になる場合があります。 あまり人気のない (視聴者が週に 1 人か 2 人) ビデオ アセットが何百万もあるものの、何千ものユーザーが全員異なるビデオを視聴している場合、CDN はほとんど効力を発揮しません。 このキャッシュ ミスにより、ストリーミング エンドポイントの負荷が高くなります。
 
また、アダプティブ ストリーミングの動作も考慮する必要があります。 個々のビデオ フラグメントはそれぞれのエンティティとしてキャッシュされます。 たとえば、ある特定のビデオが初めて視聴され、そのユーザーがあちこち数秒ずつ見てスキップすることを繰り返している場合、そのユーザーが視聴したビデオに関連するビデオ フラグメントのみが CDN にキャッシュされます。 アダプティブ ストリーミングでは通常、5 から 7 の異なるビットレートのビデオがあります。 あるユーザーがあるビットレートで視聴しており、別のユーザーが異なるビットレートで視聴している場合、CDN ではそれぞれ別個にキャッシュされます。 2 人が同じビットレートで視聴していても、異なるプロトコルでストリーミングしている可能性もあります。 各プロトコル (HLS、MPEG-DASH、スムーズ ストリーミング) は別個にキャッシュされます。 つまり、各ビットレートやプロトコルは別個にキャッシュされ、要求されたビデオ フラグメントのみがキャッシュされます。
 
## <a name="streamingendpoint-properties"></a>StreamingEndpoint のプロパティ 

このセクションでは、StreamingEndpoint の一部のプロパティについて説明します。 新しいストリーミング エンドポイントを作成する方法の例と全プロパティの説明については、[ストリーミング エンドポイント](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)に関する記事をご覧ください。 

|プロパティ|説明|  
|--------------|----------|
|`accessControl`|このストリーミング エンドポイントのセキュリティ設定(このエンドポイントに接続することが許可された Akamai 署名認証キーと IP アドレス) 構成するために使用されます。<br />このプロパティは、`cdnEnabled`"` が false に設定されているときに設定できます。|  
|`cdnEnabled`|このストリーミング エンドポイントに対する Azure CDN 統合が有効になっているかどうかを示します (既定では無効)。<br /><br /> `cdnEnabled` を true に設定した場合、`customHostNames` と `accessControl` の構成が無効になります。<br /><br />すべてのデータ センターが Azure CDN 統合をサポートしているわけではありません。 対象のデータ センターで Azure CDN 統合を利用できるかどうかを確認するには、次の操作を行います。<br /><br /> - `cdnEnabled` を true に設定する。<br /><br /> - メッセージ「Streaming endpoint CdnEnabled property cannot be set to true as CDN capability is not available in the current region」(現在のリージョンで CDN の機能が利用できないため、ストリーミング エンドポイント CdnEnabled プロパティを true に設定できません) と共に返された `HTTP Error Code 412` (PreconditionFailed) の結果を確認する。<br /><br /> このエラーが発生した場合、そのデータ センターはサポートされていません。 別のデータ センターをお試しください。|  
|`cdnProfile`|`cdnEnabled` が true に設定されていると、`cdnProfile` の値を渡すこともできます。 `cdnProfile` は CDN エンドポイントのポイントが作成される、CDN プロファイルの名前です。 既存の cdnProfile を指定するか、新しいものを使用できます。 値が NULL で `cdnEnabled` が true の場合、既定値 "AzureMediaStreamingPlatformCdnProfile" が使用されます。 指定された `cdnProfile` が既に存在する場合、その下にエンドポイントが作成されます。 プロファイルが存在しない場合、新しいプロファイルが自動的に作成されます。|
|`cdnProvider`|CDN が有効になっていると、`cdnProvider` の値を渡すこともできます。 `cdnProvider` は使用されるプロバイダーを制御します。 現時点では、"StandardVerizon"、"PremiumVerizon"、および "StandardAkamai" の 3 つの値がサポートされています。 値が指定されておらず、`cdnEnabled` が true の場合、"StandardVerizon" (既定値) が使用されます。|
|`crossSiteAccessPolicies`|さまざまなクライアントのクロス サイト アクセス ポリシーを指定するために使用されます。 詳しくは、[Cross-domain ポリシー ファイルの仕様](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)と「[Making a Service Available Across Domain Boundaries](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx)」(ドメインの境界を越えてサービスを利用できるようにする) をご覧ください。|  
|`customHostNames`|カスタム ホスト名に送信されるトラフィックを受け取るようストリーミング エンドポイントを構成するために使用します。 これにより、Global Traffic Manager (GTM) を通じてトラフィック管理の構成が簡単になり、ストリーミング エンドポイントの名前としてブランド化されたドメイン名を使用することを許可します。<br /><br /> ドメイン名の所有権は、Azure Media Services によって確認される必要があります。 Azure Media Services は、使用中のドメインに追加されるコンポーネントとして Azure Media Services アカウント ID が含まれる `CName` レコードを要求することで、ドメイン名の所有権を検証します。 例として、ストリーミング エンドポイントのカスタム ホスト名として "sports.contoso.com" が使用されるには、Media Services の検証ホスト名の 1 つにポイントするよう、`<accountId>.contoso.com` のレコードを構成する必要があります。 検証ホスト名は、verifydns.\<mediaservices-dns-zone> で構成されます。 次の表に、Azure のさまざまなリージョンにおけるレコードの検証での使用が想定される DNS ゾーンを示します。<br /><br /> 北米、ヨーロッパ、シンガポール、香港、日本:<br /><br /> - mediaservices.windows.net<br /><br /> - verifydns.mediaservices.windows.net<br /><br /> 中国:<br /><br /> - mediaservices.chinacloudapi.cn<br /><br /> - verifydns.mediaservices.chinacloudapi.cn<br /><br /> たとえば、"945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" を "verifydns.mediaservices.windows.net" にマップする `CName` レコードは、Azure Media Services ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad が contoso.com ドメインを所有していることを証明し、contoso.com の下の任意の名前を、そのアカウントの下のストリーミング エンドポイントのカスタム ホスト名として使用することを有効にします。<br /><br /> メディア サービス ID の値は、[Azure portal](https://portal.azure.com/) に移動してお使いのメディア サービス アカウントを選択することで確認できます。 メディア サービス ID は、ダッシュボード ページの右側に表示されます。<br /><br /> **警告**:`CName` レコードの適切な検証なしにカスタム ホスト名を設定しようとした場合、DNS の応答は失敗し、その後しばらくの間キャッシュされます。 適切なレコードが配置されると、キャッシュされた応答が再検証されるまで少し時間がかかる場合があります。 カスタム ドメインの DNS プロバイダーによっては、レコードの再検証に数分から数時間かかることがあります。<br /><br /> `<accountId>.<parent domain>` を `verifydns.<mediaservices-dns-zone>` にマップする `CName` に加えて、カスタム ホスト名 (たとえば、`sports.contoso.com`) を Media Services StreamingEndpont のホスト名 (たとえば、`amstest.streaming.mediaservices.windows.net`) にマップする別の `CName` を作成する必要があります。<br /><br /> **メモ**:同じデータ センターにあるストリーミング エンドポイントで、同じカスタム ホスト名を共有することはできません。<br /> このプロパティは Standard と Premium のストリーミング エンドポイントで有効で、"cdnEnabled": false のときに設定できます。<br/><br/> 現在のところ、AMS ではカスタム ドメインを使用した SSL はサポートされていません。  |  
|`maxCacheAge`|メディア フラグメントとオンデマンドのマニフェスト上のストリーミング エンドポイントで設定されている、既定の max-age HTTP キャッシュ コントロール ヘッダーをオーバーライドします。 値は秒単位で設定されます。|
|`resourceState`|プロパティの値は次のとおりです。<br /><br /> - 停止済み。 ストリーミング エンドポイント作成後の初期状態です。<br /><br /> - 開始中。 ストリーミング エンドポイントが実行状態に遷移しています。<br /><br /> - 実行中。 ストリーミング エンドポイントがクライアントにコンテンツをストリーミングできます。<br /><br /> - スケーリング中。 スケール ユニットが増加しているまたは減少しています。<br /><br /> - 停止中。 ストリーミング エンドポイントが停止状態に遷移しています。<br/><br/> - 削除中。 ストリーミング エンドポイントを削除しています。|
|`scaleUnits `|scaleUnits は 200 Mbps 単位で購入できる専用の送信容量を提供します。 **Premium** 型に移行する必要がある場合は、`scaleUnits` を調整します。 |

## <a name="next-steps"></a>次の手順

[このリポジトリ](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)のサンプルは、既定のストリーミング エンドポイントを .NET を使用して開始する方法について示します。

