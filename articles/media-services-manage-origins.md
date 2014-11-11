<properties linkid="scripting-center-index" urlDisplayName="index" pageTitle="How to Manage Origins in a Media Services Account" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Manage Origins in a Media Services Account" authors="juliako"  solutions="" writer="juliako" manager="" editor=""  />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# <span id="managemediaservicesorigins"></span></a>Media Services アカウントでオリジンを管理する方法

メディア サービスでは、複数のストリーミングのオリジンをアカウントに追加し、オリジンを構成することができます。各メディア サービス アカウントには、**"既定"** と呼ばれるストリーミングのオリジンが少なくとも 1 つ関連付けられています。

## オリジンの追加と削除

1.  [管理ポータル][管理ポータル]で、**[メディア サービス]** をクリックします。次に、メディア サービスの名前をクリックします。
2.  [オリジン] ページを選択します。
3.  ページの下部にある [追加] または [削除] をクリックします。既定のオリジンは削除できない点に注意してください。
4.  [開始] をクリックしてオリジンを開始します。
5.  オリジンを構成する場合は、オリジンの名前をクリックします。

    ![[オリジン] ページ](./media/media-services-manage-origins/media-services-origins-page.png)

## オリジンの構成

[構成] タブでは、次の構成を行うことができます。

1.  HTTP 応答のキャッシュ制御ヘッダーで指定される最大キャッシュ期間を設定する。この値は、BLOB コンテンツで明示的に設定された最大キャッシュ値をオーバーライドしません。

2.  公開されているストリーミング エンドポイントへの接続が許可される IP アドレスを指定する。IP アドレスが指定されていない場合、どの IP アドレスでも接続できます。

3.  Akamai 署名ヘッダー認証の構成を指定する。

    ![オリジンの構成](./media/media-services-manage-origins/media-services-origins-configure.png)

    このページの構成は、1 つ以上の占有ユニットを持つオリジンのみに適用されます。オンデマンド ストリーミング占有ユニットを予約するには、[スケール] タブに移動します。占有ユニットの詳細については、「[メディア サービスの規模の設定方法][メディア サービスの規模の設定方法]」を参照してください。

  [管理ポータル]: https://manage.windowsazure.com/
  [メディア サービスの規模の設定方法]: http://go.microsoft.com/fwlink/?LinkID=275847&clcid=0x409/
