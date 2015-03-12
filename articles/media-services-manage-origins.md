<properties 
	pageTitle="メディア サービス アカウントでストリーミング エンドポイントを管理する方法" 
	description="このトピックでは、Azure の管理ポータルを使用して、ストリーミング エンドポイントを管理する方法について説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	writer="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


#<a id="managemediaservicesorigins"></a>メディア サービス アカウントでストリーミング エンドポイントを管理する方法

この記事は、[メディア サービスのビデオ オンデマンド ワークフロー](../media-services-video-on-demand-workflow)と[メディア サービスのライブ ストリーミング ワークフロー](../media-services-live-streaming-workflow) シリーズの一部です。  


メディア サービスでは、ストリーミング エンドポイントは、コンテンツをクライアント プレーヤー アプリケーション、または再配布のためのコンテンツ配信ネットワーク (CDN) に直接、配信するストリーミング サービスを表します。現時点では、Microsoft Azure メディア サービスは、シームレスな CDN 統合を提供しませんが、市場の CDN プロバイダーの 1 つを使用できます。(Azure CDN または Akamai)。ストリーミング エンドポイント サービスからの送信ストリームには、ライブ ストリームまたは メディア サービス アカウントのオンデマンド ビデオ アセットを使用します。 

さらに、スケール単位 (ストリーミング占有ユニットとも呼ばれます) を調整することによって、帯域幅で増え続けるニーズを処理するストリーミング エンドポイント サービスの容量を制御できます。実稼働環境でのアプリケーションの 1 つ以上のスケール単位を割り当てることをお勧めします。スケール ユニットを使用すると、専用の送信容量を 200 Mbps 単位で購入できるほかに、動的パッケージ化機能などの追加機能を利用できるようになります。 

このトピックでは、Azure の管理ポータルを使用して、ストリーミング エンドポイントを管理する方法について説明します。


##ストリーミング エンドポイントの追加と削除 

1. [管理ポータル](https://manage.windowsazure.com/)で **[メディア サービス]** をクリックします。次に、メディア サービスの名前をクリックします。
2. [**ストリーミング エンドポイント**] ページを選択します。 
3. ページの下部にある [追加] または [削除] をクリックします。既定のストリーミング エンドポイントは削除できない点に注意してください。 
4. [開始] をクリックしてストリーミング エンドポイントを開始します。 
5. ストリーミング エンドポイントの名前をクリックして、それを構成します。   

	![Origin page][origin-page]

##<a id="scale_streaming_endpoints"></a>ストリーミング エンドポイントの拡張

ストリーミング占有ユニットを使用すると、専用の送信容量を 200 Mbps 単位で購入できるほか、[動的パッケージ化機能](http://go.microsoft.com/fwlink/?LinkId=276874)などの追加機能を利用できるようになります。既定では、ストリーミングは、サーバー リソース (コンピューティング、送信容量など) を他のユーザーと共有する共有インスタンス モデルとして構成されます。ストリーミングのスループットを向上させるにはストリーミング占有ユニットの購入を勧めします。 

ストリーミング ユニットの数を変更するには、以下の手順を実行します。

1. ストリーミング ユニットの数を指定するには、[スケール] タブをクリックし、**[占有容量]** スライダーを動かします。

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. [保存] ボタンを押して、変更を保存します。

	新しいストリーミング ユニットの割り当ては完了するまでに約 20 分かかります。 

	 
	>[AZURE.NOTE] 現在のところ、ストリーミング占有ユニットの数を正の値からゼロに戻すと、オンデマンド ストリーミングが最大 1 時間無効になります。

	>[AZURE.NOTE] コストの計算時には、24 時間の期間内に指定されたユニットの最大数が使用されます。料金設定の詳細については、「[Azure 料金早見表](http://go.microsoft.com/fwlink/?LinkId=275107)」をご覧ください。
	
##ストリーミング エンドポイントの構成

この画像に示した構成は、[構成] タブで実行できます。以降、その各フィールドについて説明します。

>[AZURE.NOTE] このページの構成は、1 つ以上の占有ユニットを持つストリーミング エンドポイントのみに適用されます。オンデマンド ストリーミング占有ユニットを予約するには、次の手順を実行します。

![Configure origin][configure-origin]
  

1. HTTP 応答のキャッシュ制御ヘッダーで指定される最大キャッシュ期間を設定する。この値は、BLOB コンテンツで明示的に設定された最大キャッシュ値をオーバーライドしません。

2. 公開されているストリーミング エンドポイントへの接続が許可される IP アドレスを指定する。IP アドレスが指定されていない場合、どの IP アドレスでも接続できます。

3. Akamai 署名ヘッダー認証の構成を指定する。

4. Adobe Flash クライアントについては、クロス ドメインのアクセス ポリシーを指定できます (詳細については、[クロス ドメイン ポリシー ファイルの仕様](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)に関するページを参照)。Microsoft Silverlight クライアントについてはクライアント アクセス ポリシーを指定できます (詳細については、[ドメインの境界を越えたサービス提供に関するページ](https://msdn.microsoft.com/ja-jp/library/cc197955(v=vs.95).aspxを参照)。  

5. **[構成]** ボタンをクリックしてカスタム ホスト名も構成できます。詳細については、[StreamingEndpont](https://msdn.microsoft.com/ja-jp/library/dn783468.aspx) のトピックの **CustomHostNames** プロパティをご覧ください。  



[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png

<!--HONumber=45--> 
