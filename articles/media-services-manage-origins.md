<properties linkid="scripting-center-index" urlDisplayName="索引" pageTitle="スクリプト センター索引" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="メディア サービス アカウントでオリジンを管理する方法" authors="juliako" solutions="" manager="" editor="" />





<h1><a id="managemediaservicesorigins"></a>メディア サービス アカウントでオリジンを管理する方法</h1>

メディア サービスでは、複数のストリーミングのオリジンをアカウントに追加し、オリジンを構成することができます。各メディア サービス アカウントには、**"既定"** と呼ばれるストリーミングのオリジンが少なくとも 1 つ関連付けられています。


<h2>オリジンの追加と削除</h2>

1. [管理ポータル](https://manage.windowsazure.com/)で、**[メディア サービス]** をクリックします。次に、メディア サービスの名前をクリックします。
2. [オリジン] ページを選択します。
3. ページの下部にある [追加] または [削除] をクリックします。既定のオリジンは削除できない点に注意してください。
4. [開始] をクリックしてオリジンを開始します。
5. オリジンを構成する場合は、オリジンの名前をクリックします。

	![[オリジン] ページ][origin-page]

<h2>オリジンの構成</h2>

[構成] タブでは、次の構成を行うことができます。

1. HTTP 応答のキャッシュ制御ヘッダーで指定される最大キャッシュ期間を設定する。この値は、BLOB コンテンツで明示的に設定された最大キャッシュ値をオーバーライドしません。

2. 公開されているストリーミング エンドポイントへの接続が許可される IP アドレスを指定する。IP アドレスが指定されていない場合、どの IP アドレスでも接続できます。

3. Akami サーバーからの G20 認証要求の構成を指定する。


	![オリジンの構成][configure-origin]

	このページの構成は、1 つ以上の占有ユニットを持つオリジンのみに適用されます。オンデマンド ストリーミング占有ユニットを予約するには、[スケール] タブに移動します。占有ユニットの詳細については、「[メディア サービスの規模の設定方法](http://go.microsoft.com/fwlink/?LinkID=275847&clcid=0x409/)」を参照してください。


[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png

