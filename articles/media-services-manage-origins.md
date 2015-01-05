<properties urlDisplayName="index" pageTitle="Media Services アカウントでオリジンを管理する方法" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Manage Streaming Endpoints in a Media Services Account" authors="juliako"  solutions="" writer="juliako" manager="dwrede" editor=""  />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />


<h1><a id="managemediaservicesorigins"></a>Media Services アカウントでストリーミング エンドポイントを管理する方法</h1>

Media Services では、複数のストリーミング エンドポイントをアカウントに追加し、ストリーミング エンドポイントを構成することができます。各 Media Services アカウントには、"**既定**" と呼ばれるストリーミング エンドポイントが少なくとも 1 つ関連付けられています。

>[WACOM.NOTE] ストリーミング エンドポイントは、以前はオリジンと呼ばれていました。 


<h2>ストリーミング エンドポイントの追加および削除</h2> 

1. [管理ポータル](https://manage.windowsazure.com/)で、**[メディア サービス]** をクリックします。次に、メディア サービスの名前をクリックします。
2. [ストリーミング エンドポイント] ページを選択します。 
3. ページの下部にある [追加] または [削除] をクリックします。既定のストリーミング エンドポイントは削除できない点に注意してください。 
4. [開始] をクリックしてストリーミング エンドポイントを開始します。 
5. ストリーミング エンドポイントの名前をクリックして、それを構成します。   

	![Origin page][origin-page]

<h2>ストリーミング エンドポイントの構成</h2>

[構成] タブでは、次の構成を行うことができます。

1. HTTP 応答のキャッシュ制御ヘッダーで指定される最大キャッシュ期間を設定する。この値は、BLOB コンテンツで明示的に設定された最大キャッシュ値をオーバーライドしません。

2. 公開されているストリーミング エンドポイントへの接続が許可される IP アドレスを指定する。IP アドレスが指定されていない場合、どの IP アドレスでも接続できます。

3. Akamai 署名ヘッダー認証の構成を指定する。



	![Configure origin][configure-origin]

	このページの構成は、1 つ以上の占有ユニットを持つストリーミング エンドポイントのみに適用されます。オンデマンド ストリーミング占有ユニットを予約するには、[スケール] タブに移動します。占有ユニットの詳細については、「[メディア サービスの規模の設定方法](../media-services-how-to-scale/)」を参照してください。


[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png

<!--HONumber=35.1-->
