<properties 
	pageTitle="Azure CDN エンドポイントの消去" 
	description="CDN エンドポイントからキャッシュされたすべてのコンテンツを消去する方法について説明します。" 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/03/2015" 
	ms.author="casoper"/>
	
# Azure CDN エンドポイントの消去

## 概要 

Azure CDN エッジ ノードは、資産の Time-to-Live (TTL) が期限切れになるアセットをキャッシュします。資産の TTL の期限が切れた後に、クライアントがエッジ ノードから資産を要求すると、エッジ ノードは資産の最新コピーを取得し、クライアント要求に対応して、更新されたキャッシュを格納します。

必要に応じて、すべてのエッジ ノードのキャッシュされたコンテンツを消去し、すべてのエッジ ノードが新しい更新された資産を取得するように強制することもできます。たとえば、Web アプリケーションの更新に対応する場合や、正しくない情報を含む資産をすばやく更新する場合などです。

このチュートリアルでは、エンドポイントのすべてのエッジ ノードから資産を消去する方法について説明します。

## チュートリアル

1. [Azure ポータル](http://portal.azure.com)で、消去するエンドポイントを含む CDN プロファイルを開きます。

2. CDN プロファイル ブレードで、[消去] ボタンをクリックします。
	
	![CDN プロファイル ブレード](./media/cdn-purge-endpoint/cdn-profile-blade.png)
	
	[消去] ブレードが開きます。
	
	![CDN の [消去] ブレード](./media/cdn-purge-endpoint/cdn-purge-blade.png)
	
3. [消去] ブレードで、[URL] ドロップダウンから消去するサービス アドレスを選択します。

	![[消去] フォーム](./media/cdn-purge-endpoint/cdn-purge-form.png)
	
	> [AZURE.NOTE] [消去] ブレードを開くには、CDN エンドポイント ブレードの **[消去]** ボタンをクリックします。この例では、そのエンドポイントのサービス アドレスが既定値として **[URL]** フィールドに表示されるようになります。
	
4. エッジ ノードから消去する資産を選択します。すべての資産を消去する場合は、**[すべて消去]** チェックボックスをクリックします。それ以外の場合は、消去する各資産の完全パスを **[パス]** テキストボックスに入力します (*/pictures/kitten.png* など)。

	> [AZURE.TIP]テキストを入力すると、**[パス]** テキストボックスが追加され、複数の資産の一覧を作成できます。一覧から資産を削除するには、省略記号 (...) ボタンをクリックします。
	>
	> パスには相対 URL を指定します。ワイルドカードとしてアスタリスク (*) を使用できます。
	
5. **[消去]** ボタンをクリックします。

	![[消去] ボタン](./media/cdn-purge-endpoint/cdn-purge-button.png)
	

## 関連項目
[Azure CDN REST API リファレンス - エンドポイントの消去または事前読み込み](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_1210_2015--->