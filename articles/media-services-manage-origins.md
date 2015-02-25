<properties pageTitle="Media Services アカウントでオリジンを管理する方法" description="" services="media-services" documentationCenter="" authors="juliako" 
	writer="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/29/2015" ms.author="juliako"/>


# <a id="managemediaservicesorigins"></a>Media Services アカウントでストリーミング エンドポイントを管理する方法

Media Services では、複数のストリーミング エンドポイントをアカウントに追加し、ストリーミング エンドポイントを構成することができます。各 Media Services アカウントには、"**既定**" と呼ばれるストリーミング エンドポイントが少なくとも 1 つ関連付けられています。

>[AZURE.NOTE] ストリーミング エンドポイントは、以前はオリジンと呼ばれていました。 


## ストリーミング エンドポイントの追加および削除 

1. [管理ポータル](https://manage.windowsazure.com/)で **[メディア サービス]** をクリックします。次に、メディア サービスの名前をクリックします。
2. [ストリーミング エンドポイント] ページを選択します。 
3. ページの下部にある [追加] または [削除] をクリックします。既定のストリーミング エンドポイントは削除できない点に注意してください。 
4. [開始] をクリックしてストリーミング エンドポイントを開始します。 
5. ストリーミング エンドポイントの名前をクリックして、それを構成します。   

	![Origin page][origin-page]

## ストリーミング エンドポイントの拡張

ストリーミング ユニットを使用すると、専用の送信容量を 200 Mbps 単位で購入できるほか、  [動的パッケージ化機能](http://go.microsoft.com/fwlink/?LinkId=276874)などの追加機能を利用できるようになります。既定では、オンデマンド ストリーミングは、サーバー リソース (コンピューティング、送信容量など) を他のユーザーと共有する共有インスタンス モデルとして構成されます。オンデマンド ストリーミングのスループットを高めるために、ストリーミング ユニットの購入をお勧めします。 

ストリーミング ユニットの数を変更するには、以下の手順を実行します。

1. ストリーミング ユニットの数を指定するには、[スケール] タブをクリックし、**[占有容量]** スライダーを動かします。

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. [保存] ボタンを押して、変更を保存します。

	新しいストリーミング ユニットの割り当ては完了するまでに約 20 分かかります。 

	 
	>[AZURE.NOTE] 現在のところ、ストリーミング占有ユニットの数を正の値からゼロに戻すと、オンデマンド ストリーミングが最大 1 時間無効になります。

	>[AZURE.NOTE] コストの計算時には、24 時間の期間内に指定されたユニットの最大数が使用されます。料金設定の詳細については、「[Azure 料金早見表](http://go.microsoft.com/fwlink/?LinkId=275107)」を参照してください。
	
## ストリーミング エンドポイントの構成

この画像に示した構成は、[構成] タブで実行できます。以降、その各フィールドについて説明します。

>[AZURE.NOTE] このページの構成は、1 つ以上の占有ユニットを持つストリーミング エンドポイントのみに適用されます。オンデマンド ストリーミング占有ユニットを予約するには、次の手順を実行します。

![Configure origin][configure-origin]
  

1. HTTP 応答のキャッシュ制御ヘッダーで指定される最大キャッシュ期間を設定する。この値は、BLOB コンテンツで明示的に設定された最大キャッシュ値をオーバーライドしません。

2. 公開されているストリーミング エンドポイントへの接続が許可される IP アドレスを指定する。IP アドレスが指定されていない場合、どの IP アドレスでも接続できます。

3. Akamai 署名ヘッダー認証の構成を指定する。

4. Adobe Flash クライアントについては、クロス ドメインのアクセス ポリシーを指定できます (詳細については、[クロス ドメイン ポリシー ファイルの仕様](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)に関するページを参照)。Microsoft Silverlight クライアントについてはクライアント アクセス ポリシーを指定できます (詳細については、[ドメインの境界を越えたサービス提供に関するページ](https://msdn.microsoft.com/ja-jp/library/cc197955(v=vs.95).aspxを参照)。  

5. **[構成]** ボタンをクリックしてカスタム ホスト名を構成することもできます。詳細については、[StreamingEndpont](https://msdn.microsoft.com/ja-jp/library/dn783468.aspx) のトピックの **CustomHostNames** プロパティを参照してください。  




[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png


<!--HONumber=42-->
