<properties
	pageTitle="CDN - クエリ文字列による要求のキャッシュ動作の制御"
	description="CDN クエリ文字列のキャッシュにより、ファイルにクエリ文字列が含まれている場合のファイルのキャッシュ方法を制御します。"
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2016"
	ms.author="casoper"/>

#クエリ文字列による CDN 要求のキャッシュ動作の制御

> [AZURE.SELECTOR]
- [Standard](cdn-query-string.md)
- [Azure CDN Premium from Verizon](cdn-query-string-premium.md)

##概要

クエリ文字列のキャッシュにより、ファイルにクエリ文字列が含まれている場合のファイルのキャッシュ方法を制御します。

> [AZURE.IMPORTANT] Standard および Premium CDN 製品では同じクエリ文字列キャッシュ機能が提供されますが、ユーザー インターフェイスは異なります。ここでは、**Azure CDN Standard from Akamai** と **Azure CDN Standard from Verizon** のインターフェイスについて説明します。**Azure CDN Premium from Verizon** でのクエリ文字列のキャッシュについては、「[クエリ文字列による CDN 要求のキャッシュ動作の制御 - Premium](cdn-query-string-premium.md)」を参照してください。

次の 3 つのモードを使用できます。

- **クエリ文字列を無視する**: これは、既定のモードです。CDN エッジ ノードは、クエリ文字列を要求元から最初の要求の配信元に渡して、資産をキャッシュします。エッジ ノードから提供される資産の後続の要求はすべて、キャッシュされた資産の有効期限が切れるまで、クエリ文字列を無視します。
- **クエリ文字列を含む URL のキャッシュをバイパス**: このモードでは、クエリ文字列のある要求は CDN エッジ ノードでキャッシュされません。エッジ ノードは配信元から直接資産を取得し、それを各要求により要求元に渡します。
- **一意の URL をすべてキャッシュ**: このモードでは、クエリ文字列のある各要求は、独自のキャッシュを持つ一意の資産として処理されます。たとえば、*foo.ashx?q=bar* の要求の配信元からの応答はエッジ ノードでキャッシュされ、後続のキャッシュではその同じクエリ文字列により返されます。*foo.ashx?q=somethingelse* の要求は、独自の有効期限を持つ個別の資産としてキャッシュされます。

##Standard CDN プロファイル用にクエリ文字列のキャッシュ設定を変更する

1. CDN プロファイル ブレードで、管理する CDN エンドポイントをクリックします。

	![CDN プロファイル ブレード エンドポイント](./media/cdn-query-string/cdn-endpoints.png)

	CDN エンドポイントのブレードが開きます。

2. **[構成]** をクリックします。

	![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-query-string/cdn-config-btn.png)

	CDN 構成ブレードが開きます。

3. **[クエリ文字列のキャッシュ動作]** ドロップダウンから設定を選択します。

	![CDN クエリ文字列のキャッシュ オプション](./media/cdn-query-string/cdn-query-string.png)

4. 選択したら、**[保存]** をクリックします。

> [AZURE.IMPORTANT] 設定の変更が CDN に反映されるまでに時間がかかるので、変更がすぐに表示されないことがあります。<b>Azure CDN from Akamai</b> プロファイルの場合、通常、反映は 1 分以内で完了します。<b>Azure CDN from Verizon</b> プロファイルの場合、通常、反映は 90 分以内に完了しますが、もっと時間がかかる場合もあります。

<!---HONumber=AcomDC_0518_2016-->