<properties
	pageTitle="Azure CDN エンドポイントの消去 | Microsoft Azure"
	description="CDN エンドポイントからキャッシュされたすべてのコンテンツを消去する方法について説明します。"
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="casoper"/>

# Azure CDN エンドポイントの消去

## 概要

Azure CDN エッジ ノードは、アセットの Time-to-Live (TTL) が期限切れになるまで、そのアセットをキャッシュします。資産の TTL の期限が切れた後に、クライアントがエッジ ノードから資産を要求すると、エッジ ノードは資産の最新コピーを取得し、クライアント要求に対応して、更新されたキャッシュを格納します。

必要に応じて、すべてのエッジ ノードのキャッシュされたコンテンツを消去し、すべてのエッジ ノードが新しい更新された資産を取得するように強制することもできます。たとえば、Web アプリケーションの更新に対応する場合や、正しくない情報を含む資産をすばやく更新する場合などです。

> [AZURE.TIP] 消去処理では、CDN エッジ サーバーにキャッシュされているコンテンツのみが消去される点に注意してください。ダウンストリームのキャッシュ (プロキシ サーバーやローカル ブラウザーのキャッシュなど) がある場合、キャッシュされたファイルのコピーが残る可能性があります。ファイルの有効期間を設定するときに、この点に気を付けることが重要です。ダウンストリーム クライアントが最新バージョンのファイルを要求するように強制するには、更新するたびに一意の名前を付けるか、[クエリ文字列のキャッシュ](cdn-query-string.md)を利用します。

このチュートリアルでは、エンドポイントのすべてのエッジ ノードから資産を消去する方法について説明します。

## チュートリアル

1. [Azure ポータル](https://portal.azure.com)で、消去するエンドポイントを含む CDN プロファイルを開きます。

2. CDN プロファイル ブレードで、[消去] ボタンをクリックします。

	![CDN プロファイル ブレード](./media/cdn-purge-endpoint/cdn-profile-blade.png)

	[消去] ブレードが開きます。

	![CDN の [消去] ブレード](./media/cdn-purge-endpoint/cdn-purge-blade.png)

3. [消去] ブレードで、[URL] ドロップダウンから消去するサービス アドレスを選択します。

	![[消去] フォーム](./media/cdn-purge-endpoint/cdn-purge-form.png)

	> [AZURE.NOTE] [消去] ブレードを開くには、CDN エンドポイント ブレードの **[消去]** ボタンをクリックします。この例では、そのエンドポイントのサービス アドレスが既定値として **[URL]** フィールドに表示されるようになります。

4. エッジ ノードから消去する資産を選択します。すべての資産を消去する場合は、**[すべて消去]** チェックボックスをクリックします。それ以外の場合は、消去する各資産の完全パスを **[パス]** テキストボックスに入力します (`/pictures/kitten.png` など)。

	> [AZURE.TIP] テキストを入力すると、**[パス]** テキストボックスが追加され、複数の資産の一覧を作成できます。一覧から資産を削除するには、省略記号 (...) ボタンをクリックします。
	>
	> パスは[正規表現](https://msdn.microsoft.com/library/az24scfc.aspx)、`^\/(?:[a-zA-Z0-9-_.\u0020]+\/)**$";` に準拠する相対 URL にする必要があります。**Azure CDN from Verizon** (Standard と Premium) の場合、アスタリスク (*) をワイルドカードとして使用できます (例: `/music/*`)。ワイルドカードと**すべて消去**は、**Azure CDN from Akamai** では使用できません。
	
5. **[消去]** ボタンをクリックします。

	![[消去] ボタン](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [AZURE.IMPORTANT] 消去要求にかかる時間は、**Azure CDN from Verizon** (Standard と Premium) で約 2 ～ 3 分、**Azure CDN from Akamai** で約 7 分です。Azure CDN には、常に同時の消去要求が 50 件という上限があります。

## 関連項目
- [Azure CDN エンドポイント上のアセットを事前に読み込む](cdn-preload-endpoint.md)
- [Azure CDN REST API リファレンス - エンドポイントの消去または事前読み込み](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0803_2016-->