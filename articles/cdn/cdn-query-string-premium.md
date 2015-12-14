<properties 
	pageTitle="CDN - クエリ文字列による要求のキャッシュ動作の制御 - Premium" 
	description="CDN クエリ文字列のキャッシュにより、ファイルにクエリ文字列が含まれている場合のファイルのキャッシュ方法を制御します。" 
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
	ms.date="12/02/2015" 
	ms.author="casoper"/>

#クエリ文字列による CDN 要求のキャッシュ動作の制御 - Premium

> [AZURE.SELECTOR]
- [Standard](cdn-query-string.md)
- [Premium](cdn-query-string-premium.md)

##概要

クエリ文字列のキャッシュにより、ファイルにクエリ文字列が含まれている場合のファイルのキャッシュ方法を制御します。

> [AZURE.NOTE]Standard CDN 層と Premium CDN 層には同じクエリ文字列キャッシュ機能が備わっていますが、ユーザー インターフェイスが異なります。このドキュメントでは、**Premium** 層のユーザー インターフェイスについて説明します。Standard 層については、「[クエリ文字列による CDN 要求のキャッシュ動作の制御](cdn-query-string.md)」を参照してください。

次の 3 つのモードを使用できます。

- **standard-cache**: これは、既定のモードです。CDN エッジ ノードは、クエリ文字列を要求元から最初の要求の配信元に渡して、資産をキャッシュします。エッジ ノードから提供される資産の後続の要求はすべて、キャッシュされた資産の有効期限が切れるまで、クエリ文字列を無視します。 
- **no-cache**: このモードでは、クエリ文字列のある要求は CDN エッジ ノードでキャッシュされません。エッジ ノードは元のドメインから直接資産を取得し、それを各要求により要求元に渡します。 
- **unique-cache**: このモードでは、クエリ文字列のある各要求は、独自のキャッシュを持つ一意の資産として処理されます。たとえば、*foo.ashx?q=bar* の要求の配信元からの応答はエッジ ノードでキャッシュされ、後続のキャッシュではその同じクエリ文字列により返されます。*foo.ashx?q=somethingelse* の要求は、独自の有効期限を持つ個別の資産としてキャッシュされます。
	
	>[AZURE.WARNING]このモードは、クエリ文字列に各要求で異なるパラメーター (セッション ID、ユーザー名など) が含まれる場合には使用できません。これは、非常に低いキャッシュ ヒット率を招くことがあるためです。

##クエリ文字列キャッシュ設定を変更する

1. [CDN プロファイル] ブレードで、**[管理]** をクリックします。

	![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-query-string-premium/cdn-manage-btn.png)
	
	CDN 管理ポータルが開きます。
	
2. **[HTTP ラージ]** タブ、**[キャッシュの設定]** フライアウトの順にマウスのカーソルを合わせます。**[クエリ文字列のキャッシュ]** をクリックします。
	
	クエリ文字列のキャッシュ オプションが表示されます。
	
	![CDN クエリ文字列のキャッシュ オプション](./media/cdn-query-string-premium/cdn-query-string.png)
	
3. 選択したら、**[更新]** をクリックします。




	

<!---HONumber=AcomDC_1203_2015-->