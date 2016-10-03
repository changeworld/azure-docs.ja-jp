<properties
	pageTitle="Media Services Extension での CDN キャッシュ ポリシー"
	description="このトピックでは、Media Services Extension での CDN キャッシュ ポリシーの概要について説明します。"
	services="media-services,cdn"
	documentationCenter=".NET"
	authors="juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="juliako"/>
 
#Media Services Extension での CDN キャッシュ ポリシー

Azure Media Services では、HTTP ベースのアダプティブ ストリーミングとプログレッシブ ダウンロードが提供されます。HTTP ベースのストリーミングは、プロキシと CDN レイヤーにおけるキャッシュだけでなくクライアント側のキャッシュの利点もあり、高い拡張性を備えています。ストリーミング エンドポイントは、一般的なストリーミング機能と、HTTP キャッシュ ヘッダーの構成も提供します。ストリーミング エンドポイントは、HTTP Cache-Control: max-age および Expires ヘッダーを設定します。HTTP キャッシュ ヘッダーの詳細については、[W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html) を参照してください。

##既定のキャッシュ ヘッダー

既定では、ストリーミング エンドポイントは、オンデマンド ストリーミング データ (実際のメディア フラグメント/チャンク) および マニフェスト (プレイリスト) に対して 3 日間有効なキャッシュ ヘッダーを適用します。ライブ ストリーミングの場合は、ストリーミング エンドポイントは、データ (実際のメディア フラグメント/チャンク) に対しては 3 日間有効なキャッシュ ヘッダーを、マニフェスト (プレイリスト) 要求には 2 秒間有効なキャッシュ ヘッダーを適用します。ライブ プログラムがオンデマンド (ライブ アーカイブ) に変わると、オンデマンド ストリーミング キャッシュ ヘッダーが適用されます。

##Azure CDN 統合

Azure Media services は、ストリーミング エンドポイントに対して[統合 CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) を提供します。キャッシュ制御ヘッダーは、ストリーミング エンドポイントと同じ方法で CDN が有効なストリーミング エンドポイントに適用されます。Azure CDN は、ストリーミング エンドポイントで構成されたキャッシュ値を使用して内部キャッシュされるオブジェクトの有効期間を定義し、配信キャッシュ ヘッダーもこの値を使用して設定します。CDN 対応のストリーミング エンドポイントを使用するときは、小さいキャッシュ値を設定することは推奨されません。小さい値を設定すると、パフォーマンスが低下し、CDN のメリットが小さくなります。CDN が有効なストリーミング エンドポイントの場合、600 秒未満にキャッシュ ヘッダーを設定することは許可されません。

>[AZURE.IMPORTANT] Azure CDN と統合された Azure Media Services は、**Azure CDN from Verizon** に実装されています。Azure Media Services に **Azure CDN from Akamai** を使用する場合、[エンドポイントを手動で構成する](cdn-create-new-endpoint.md)必要があります。Azure CDN 機能の詳細については、「[CDN の概要](cdn-overview.md)」を参照してください。

##Azure Media Services でのキャッシュ ヘッダーの構成

Azure 管理ポータルまたは Azure Media Services API を使用して、キャッシュ ヘッダーの値を構成できます。

1. 管理ポータルを使用してキャッシュ ヘッダーを構成する方法については、「[Media Services アカウントでストリーミング エンドポイントを管理する方法](../media-services/media-services-portal-manage-streaming-endpoints.md)」の「ストリーミング エンドポイントの構成」セクションを参照してください。
2. Azure Media Services REST API の「[StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl)」。
3. Azure Media Services .NET SDK の「[StreamingEndpointCacheControl プロパティ](http://go.microsoft.com/fwlink/?LinkId=615302)」。

##キャッシュの構成の優先順位

1. Azure Media Services で構成されたキャッシュ値は、既定値を上書きします。
2. 手動による構成がない場合は既定値が適用されます。
3. 既定では、Azure Media または Azure Storage の構成に関係なく 2 秒のキャッシュ ヘッダーがライブ ストリーミング マニフェスト (プレイリスト) に適用され、この値の上書きは使用できません。

<!---HONumber=AcomDC_0921_2016-->