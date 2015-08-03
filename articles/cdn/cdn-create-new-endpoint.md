<properties 
	 pageTitle="Azure の Content Delivery Network (CDN) を有効にする方法" 
	 description="このトピックでは、Azure の Content Delivery Network (CDN) を有効にする方法を説明します。" 
	 services="cdn" 
	 documentationCenter="" 
	 authors="zhangmanling" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="06/03/2015" 
	 ms.author="mazha"/>



#Azure の Content Delivery Network (CDN) を有効にする方法  

Azure 管理ポータルを使用して送信元の CDN を有効にできます。現在使用可能な配信元の種類は、Web アプリ、ストレージ、クラウド サービスです。Azure Media Services ストリーミング エンドポイントに対して CDN を有効にすることもできます。配信元の CDN エンドポイントを有効にすると、パブリックにアクセスできるオブジェクトはすべて CDN エッジ キャッシュの対象になります。

カスタム配信元を作成することもでき、それは Azure でなくてもかまわないことに注意してください。

##新しい CDN エンドポイントを作成するには  

1.	[Azure の管理ポータル](http://manage.windowsazure.com/)にログインします。
2.	ナビゲーション ウィンドウで [**CDN**] をクリックします。
3.	リボンで、**[新規]** をクリックします。[**新規**] ダイアログで、[**App Services**]、[**CDN**]、[**簡易作成**] の順に選択します。
4.	[**配信元の種類**] ドロップダウンで、使用可能な送信元の種類のリストから配信元の種類を選択します。
	
	使用できる配信元 URL の一覧が、[**配信元 URL**] ボックスの一覧に表示されます。
	

	![createnew][createnew]

	[**カスタム配信元**] を選択した場合、カスタム配信元の URL を入力できます。Azure の配信元を使用する必要はありません。

	![customorigin][customorigin]

	>[AZURE.NOTE]現在、配信元でサポートされているのは HTTP だけであり、Azure Media Services ストリーミング エンドポイント用に Azure CDN を有効にするには Media Services Extension を使用する必要があります。
	
5.	**[作成]** ボタンをクリックして、新しいエンドポイントを作成します。


>[AZURE.NOTE]エンドポイントのために作成された構成は、すぐには使用できません。CDN ネットワークを通じて伝播する登録などのために最大 60 分かかります。CDN ドメイン名を直ちに使用しようとするユーザーは、CDN を経由してコンテンツを取得できるようになるまでは状態コード 400 (正しくない要求) を受け取る場合があります。

##関連項目
[カスタム ドメインにコンテンツ配信ネットワーク (CDN) コンテンツをマップする方法](cdn-map-content-to-custom-domain.md)

[createnew]: ./media/cdn-create-new-endpoint/cdn-create-new-account.png

[customorigin]: ./media/cdn-create-new-endpoint/cdn-custom-origin.png
 

<!---HONumber=July15_HO4-->