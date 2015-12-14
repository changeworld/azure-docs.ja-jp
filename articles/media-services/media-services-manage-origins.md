<properties 
	pageTitle="Media Services アカウントでストリーミング エンドポイントを管理する方法" 
	description="このトピックでは、Azure クラシック ポータルを使用して、ストリーミング エンドポイントを管理する方法について説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	writer="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015"
	ms.author="juliako"/>


#<a id="managemediaservicesorigins"></a>Media Services アカウントでストリーミング エンドポイントを管理する方法

> [AZURE.SELECTOR]
- [Portal](media-services-manage-origins.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)

Microsoft Azure Media Services では、**ストリーミング エンドポイント**は、コンテンツをクライアント プレーヤー アプリケーションや、再配布のための Content Delivery Network (CDN) に直接配信するストリーミング サービスを表します。Media Services は、シームレスな Azure CDN 統合もサポートしています。StreamingEndpoint サービスからの送信ストリームには、ライブ ストリームまたは Media Services アカウントのオンデマンド ビデオ資産を使用します。

さらに、スケール単位 (ストリーミング占有ユニットとも呼ばれます) を調整することによって、帯域幅で増え続けるニーズを処理するストリーミング エンドポイント サービスの容量を制御できます。運用環境でのアプリケーションの 1 つ以上のスケール単位を割り当てることをお勧めします。スケール ユニットを使用すると、専用の送信容量を 200 Mbps 単位で購入できるほかに、[動的パッケージング](media-services-dynamic-packaging-overview.md)、CDN 統合、高度な構成などの追加機能を利用できるようになります。

StreamingEndpoint が実行状態の場合にのみ課金されます。

このトピックでは、ストリーミング エンドポイントで提供される主な機能の概要を説明します。また、このトピックでは、Azure クラシック ポータルを使用してストリーミング エンドポイントを管理する方法についても説明します。


##ストリーミング エンドポイントの追加および削除

ストリーミング エンドポイントは、.NET SDK、REST API、Azure クラシック ポータルを使用して追加または削除できます。

Azure クラシック ポータルを使用してストリーミング エンドポイントを追加または削除するには、次の操作を行います。

1. [Azure クラシック ポータル](https://manage.windowsazure.com/)で、**[Media Services]** をクリックします。次に、メディア サービスの名前をクリックします。
2. [**ストリーミング エンドポイント**] ページを選択します。
3. ページの下部にある [追加] または [削除] をクリックします。既定のストリーミング エンドポイントは削除できない点に注意してください。
4. [開始] をクリックしてストリーミング エンドポイントを開始します。
5. ストリーミング エンドポイントの名前をクリックして、それを構成します。

	![ストリーミング エンドポイント ページ][streaming-endpoint]


既定では、最大 2 つのストリーミング エンドポイントを作成できます。それ以上を要求する必要がある場合は、「[クォータと制限](media-services-quotas-and-limitations.md)」をご覧ください。

##<a id="scale_streaming_endpoints"></a>ストリーミング エンドポイントのスケールを設定する

ストリーミング占有ユニットを使用すると、専用の送信容量を 200 Mbps 単位で購入できるほか、[動的パッケージ化機能](media-services-dynamic-packaging-overview.md)などの追加機能を利用できるようになります。既定では、ストリーミングは、サーバー リソース (コンピューティング、送信容量など) を他のユーザーと共有する共有インスタンス モデルとして構成されます。ストリーミングのスループットを向上させるにはストリーミング占有ユニットの購入を勧めします。

スケールは、.NET SDK、REST API、Azure クラシック ポータルを使用して設定できます。

ポータルを使用してストリーミング ユニットの数を変更するには、以下の手順を実行します。

1. ストリーミング ユニットの数を指定するには、[スケール] タブをクリックし、**[占有容量]** スライダーを動かします。

	![[スケール] ページ](./media/media-services-manage-origins/media-services-origin-scale.png)

4. [保存] ボタンを押して、変更を保存します。

	新しいストリーミング ユニットの割り当ては完了するまでに約 20 分かかります。

	 
	>[AZURE.NOTE]現在のところ、ストリーミング ユニットの数を正の値からゼロに戻すと、オンデマンド ストリーミングが最大 1 時間無効になります。

	>[AZURE.NOTE]コストの計算時には、24 時間の期間内に指定されたユニットの最大数が使用されます。料金設定の詳細については、「[Azure 料金早見表](http://go.microsoft.com/fwlink/?LinkId=275107)」を参照してください。
	
##<a id="configure_streaming_endpoints"></a>ストリーミング エンドポイントの構成

ストリーミング エンドポイントでは、1 つ以上のスケール ユニットがある場合、次のプロパティを構成できます。

- Access control
- Custom host names
- Cache control
- Cross site access policies

これらのプロパティの詳細については、「[StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)」をご覧ください。

.NET SDK、REST API、Azure クラシック ポータルを使用してこれらのプロパティを構成できます。

ポータルを使用してストリーミング ユニットの数を変更するには、以下の手順を実行します。

1. 構成するストリーミング エンドポイントを選択します。
1. [構成] タブを選択します。
  
以下で、その各フィールドについて簡単に説明します。

![オリジンの構成][configure-origin]
  

1. HTTP 応答のキャッシュ制御ヘッダーで指定される最大キャッシュ期間を設定する。この値は、BLOB コンテンツで明示的に設定された最大キャッシュ値をオーバーライドしません。

2. 公開されているストリーミング エンドポイントへの接続が許可される IP アドレスを指定する。IP アドレスが指定されていない場合、どの IP アドレスでも接続できます。

3. Akamai 署名ヘッダー認証の構成を指定する。

4. Adobe Flash クライアントについては、クロス ドメインのアクセス ポリシーを指定できます (詳細については、[クロス ドメイン ポリシー ファイルの仕様](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)に関するページをご覧ください)。Microsoft Silverlight クライアントについてはクライアント アクセス ポリシーを指定できます (詳細については、「Making a Service Available Across Domain Boundaries （ドメインの境界を越えたサービス提供)https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx)」をご覧ください)。

5. **[構成]** ボタンをクリックしてカスタム ホスト名も構成できます。詳細については、[StreamingEndpont](https://msdn.microsoft.com/library/dn783468.aspx) のトピックの **CustomHostNames** プロパティをご覧ください。


##<a id="enable_cdn"></a>Azure CDN 統合を有効にする

ストリーミング エンドポイント用に Azure CDN 統合を有効にするよう指定できます (既定では無効)。

Azure CDN 統合を true に設定するには、次の操作を行います。

- ストリーミング エンドポイントには少なくとも 1 つのストリーミング (スケール) ユニットが必要です。後でスケール ユニットを 0 に設定する場合は、まず、CDN 統合を無効にする必要があります。既定では、新しいストリーミング エンドポイントを作成するときに、1 つのストリーミング ユニットが自動的に設定されます。

- ストリーミング エンドポイントを停止状態にする必要があります。CDN を有効にすると、ストリーミング エンドポイントを開始できます。

Azure CDN 統合が有効になるまでに最大 90 分かかる場合があります。すべての CDN の POP に変更が適用されるには、最大 2 時間がかかります。


CDN 統合は、すべての Azure データ センター (米国西部、米国東部、北ヨーロッパ、西ヨーロッパ、西日本、東日本、東南アジア、東アジア) で有効になっています。

これが有効になっている場合は、**カスタム ホスト名**と **Access Control** が無効になります。

![CDN が有効になっているストリーミング エンドポイント][streaming-endpoint-enable-cdn]


###追加の考慮事項

- CDN がストリーミング エンドポイントで有効になっている場合、クライアントは配信元から直接コンテンツを要求することはできません。CDN の有無にかかわらずコンテンツをテストする必要がある場合は、CDN が有効になっていない別のストリーミング エンドポイントを作成できます。
- ストリーミング エンドポイントのホスト名は、CDN を有効にした後も変化しません。CDN を有効にした後、Media Services のワークフローを変更する必要はありません。たとえば、ストリーミング エンドポイントのホスト名が strasbourg.streaming.mediaservices.windows.net の場合、CDN を有効にした後も同じホスト名が使用されます。
- 新しいストリーミング エンドポイントの場合は、新しいエンドポイントを作成するだけで CDN を有効にできます。既存のストリーミング エンドポイントの場合は、最初にエンドポイントを停止してから CDN を有効にする必要があります。
 

詳細については、[「Azure CDN (Content Delivery Network) との Azure Media Services 統合の発表 (ブログの投稿)](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/) 」をご覧ください。


##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[streaming-endpoint-enable-cdn]: ./media/media-services-manage-origins/media-services-origins-enable-cdn.png
[streaming-endpoint]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png
 

<!---HONumber=AcomDC_1203_2015-->