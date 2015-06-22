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
	 ms.date="03/05/2015" 
	 ms.author="mazha"/>



#Azure の Content Delivery Network (CDN) を有効にする方法  

ストレージ アカウント、クラウド サービスなどのオリジンの CDN エンドポイントを有効にすると、パブリックにアクセスできるオブジェクトはすべて CDN エッジ キャッシュの対象になります。  


##新しい CDN エンドポイントを作成するには  

1.	[Azure の管理ポータル](http://manage.windowsazure.com/)にログインします。
2.	ナビゲーション ウィンドウで **[CDN]** をクリックします。
3.	リボンで、**[新規]** をクリックします。**[新規]** ダイアログで、**[アプリケーション サービス]**、**[CDN]**、**[簡易作成]** の順に選択します。
4.	**[元のドメイン]** ボックスで、使用可能なオリジンの種類の一覧から、目的の作成先を選択します。
5.	**[作成]** ボタンをクリックして、新しいエンドポイントを作成します。




> 注:エンドポイントのために作成された構成は、すぐには使用できません。CDN ネットワークを通じて伝播する登録などのために最大 60 分かかります。CDN ドメイン名を直ちに使用しようとするユーザーは、CDN を経由してコンテンツを取得できるようになるまでは状態コード 400 (正しくない要求) を受け取る場合があります。

#関連項目
[カスタム ドメインに Content Delivery Network (CDN) コンテンツをマップする方法](cdn-map-content-to-custom-domain.md)

<!--HONumber=49--> 