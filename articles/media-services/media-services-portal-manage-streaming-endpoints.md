<properties 
	pageTitle="Azure ポータルを使用したストリーミング エンドポイントの管理 | Microsoft Azure" 
	description="このトピックでは、Azure ポータルを使用してストリーミング エンドポイントを管理する方法について説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	writer="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/29/2016"
	ms.author="juliako"/>


#Azure ポータルを使用したストリーミング エンドポイントの管理

## Overview

> [AZURE.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。

Microsoft Azure Media Services では、**ストリーミング エンドポイント**は、コンテンツをクライアント プレーヤー アプリケーションや、再配布のための Content Delivery Network (CDN) に直接配信するストリーミング サービスを表します。Media Services は、シームレスな Azure CDN 統合もサポートしています。StreamingEndpoint サービスからの送信ストリームには、ライブ ストリームまたは Media Services アカウントのオンデマンド ビデオ資産を使用します。

さらに、ストリーミング ユニットを調整することで、ストリーミング エンドポイント サービスの容量を制御し、帯域幅の増化ニーズに対応できます。実稼働環境でのアプリケーションの 1 つ以上のスケール単位を割り当てることをお勧めします。ストリーミング ユニットを使用すると、専用の送信容量を 200 Mbps 単位で購入できるほかに、[ダイナミック パッケージ](media-services-dynamic-packaging-overview.md)、CDN 統合、高度な構成などの追加機能を利用できるようになります。

>[AZURE.NOTE]ストリーミング エンドポイントが実行状態の場合のみ課金されます。

このトピックでは、ストリーミング エンドポイントで利用できる主な機能の概要について説明します。また、Azure ポータルを使用してストリーミング エンドポイントを管理する方法についても説明します。ストリーミング エンドポイントのスケールを設定する方法については、[こちらの](media-services-portal-scale-streaming-endpoints.md)トピックを参照してください。

## ストリーミング エンドポイントの管理を開始する

アカウントのストリーミング エンドポイントの管理を開始するには、次の操作を行います。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. **[設定]** ウィンドウで **[ストリーミング エンドポイント]** をクリックします。

	![ストリーミング エンドポイント](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

##ストリーミング エンドポイントの追加/削除

Azure ポータルを使用してストリーミング エンドポイントを追加または削除するには、次の操作を行います。

1. ストリーミング エンドポイントを追加するには、ページの上部にある **[+ エンドポイント]** をクリックします。
2. ストリーミング エンドポイントを削除するには、**[削除]** ボタンをクリックします。

	既定のストリーミング エンドポイントは削除できません。
2. **[開始]** をクリックしてストリーミング エンドポイントを開始します。

	![ストリーミング エンドポイント](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)

既定では、最大 2 つのストリーミング エンドポイントを作成できます。それ以上を要求する必要がある場合は、「[クォータと制限](media-services-quotas-and-limitations.md)」をご覧ください。
	
##<a id="configure_streaming_endpoints"></a>ストリーミング エンドポイントの構成

ストリーミング エンドポイントでは、1 つ以上のスケール ユニットがある場合、次のプロパティを構成できます。

- Access control
- Cache control
- Cross site access policies

これらのプロパティの詳細については、「[StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)」をご覧ください。

ストリーミング エンドポイントを構成するには、以下の操作を行います。

1. 構成するストリーミング エンドポイントを選択します。
1. **[設定]** をクリックします。
  
以下で、その各フィールドについて簡単に説明します。

![ストリーミング エンドポイント](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)
  
1. [Maximum cache policy (最大キャッシュ ポリシー)]: このストリーミング エンドポイントから提供されるアセットのキャッシュの有効期間を構成するために使用します。値を設定しない場合は、既定値が使用されます。既定値は Azure ストレージで直接定義することもできます。ストリーミング エンドポイントで Azure CDN を有効にする場合、キャッシュ ポリシーの値は 600 秒以上に設定してください。

2. [Allowed IP addresses (使用できる IP アドレス)]: 公開されているストリーミング エンドポイントへの接続を許可する IP アドレスを指定するために使用します。IP アドレスが指定されていない場合、どの IP アドレスでも接続できます。IP アドレスは、1 つの IP アドレス (例: ‘10.0.0.1’)、IP アドレスと CIDR サブネット マスクを使用した IP 範囲 (例: ‘10.0.0.1/22’)、IP アドレスとピリオドで区切られた 10 進数のサブネット マスクを使用した IP 範囲 (例: ‘10.0.0.1(255.255.255.0)’) のいずれかの形で指定できます。

3. [Configuration for Akamai signature header authentication (Akamai 署名ヘッダー認証の構成)]: Akamai サーバーからの署名ヘッダー認証要求を構成する方法 を指定するために使用します。有効期限は UTC 時間で指定します。



##<a id="enable_cdn"></a>Azure CDN 統合を有効にする

ストリーミング エンドポイント用に Azure CDN 統合を有効にするよう指定できます (既定では無効)。

Azure CDN 統合を true に設定するには、次の操作を行います。

- ストリーミング エンドポイントには少なくとも 1 つのストリーミング ユニットが必要です。後でスケール ユニットを 0 に設定する場合は、まず、CDN 統合を無効にする必要があります。既定では、新しいストリーミング エンドポイントを作成するときに、1 つのストリーミング ユニットが自動的に設定されます。

- ストリーミング エンドポイントを停止状態にする必要があります。CDN を有効にすると、ストリーミング エンドポイントを開始できます。

Azure CDN 統合が有効になるまでに最大 90 分かかる場合があります。すべての CDN の POP に変更が適用されるには、最大 2 時間がかかります。

CDN 統合は、すべての Azure データ センター (米国西部、米国東部、北ヨーロッパ、西ヨーロッパ、西日本、東日本、東南アジア、東アジア) で有効になっています。

有効になると、**Access Control** の構成が無効になります。

![ストリーミング エンドポイント](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints5.png)

>[AZURE.IMPORTANT] Azure CDN と統合された Azure Media Services は、**Azure CDN from Verizon** に実装されています。Azure Media Services に **Azure CDN from Akamai** を使用する場合、[エンドポイントを手動で構成する](../cdn/cdn-create-new-endpoint.md)必要があります。Azure CDN 機能の詳細については、[CDN の概要](../cdn/cdn-overview.md)に関するページをご覧ください。

###追加の考慮事項

- CDN がストリーミング エンドポイントで有効になっている場合、クライアントは配信元から直接コンテンツを要求することはできません。CDN の有無にかかわらずコンテンツをテストする必要がある場合は、CDN が有効になっていない別のストリーミング エンドポイントを作成できます。
- ストリーミング エンドポイントのホスト名は、CDN を有効にした後も変化しません。CDN を有効にした後、Media Services のワークフローを変更する必要はありません。たとえば、ストリーミング エンドポイントのホスト名が strasbourg.streaming.mediaservices.windows.net の場合、CDN を有効にした後も同じホスト名が使用されます。
- 新しいストリーミング エンドポイントの場合は、新しいエンドポイントを作成するだけで CDN を有効にできます。既存のストリーミング エンドポイントの場合は、最初にエンドポイントを停止してから CDN を有効にする必要があります。
 

詳細については、[「Azure CDN (コンテンツ配信ネットワーク) との Azure Media Services 統合の発表 (ブログの投稿)](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/) 」をご覧ください。


##次のステップ

Media Services のラーニング パスを確認します。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 

<!---HONumber=AcomDC_0831_2016-->