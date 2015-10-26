<properties 
	pageTitle="Machine Learning Recommendations - JavaScript 統合 | Microsoft Azure" 
	description="Azure Machine Learning Recommendations - JavaScript 統合 - ドキュメント" 
	services="machine-learning" 
	documentationCenter="" 
	authors="AharonGumnik" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="10/14/2015" 
	ms.author="luisca"/>

# Azure Machine Learning Recommendations - JavaScript 統合

このドキュメントは、JavaScript を使用して、サイトを統合する方法を表します。JavaScript を使用すると、データ取得のイベントを送信し、推奨設定のモデル作成後に推奨事項を使用できるようになります。JS を使用して行うすべての操作はサーバー側からも実行できます。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##1\.概要
Azure ML Recommendation を使用したサイトの統合は、次の 2 つのフェーズで構成されます。

1.	イベントを Azure ML Recommendation へ送信するこれによって推奨モデルを作成できます。
2.	推奨事項を使用するモデルをビルドした後は、推奨設定を使用できます (このドキュメントでは、モデルを構築する方法については説明しません。詳細についてはクイック スタート ガイドをご覧ください)。


<ins>フェーズ I</ins>

最初のフェーズでは、ご利用の html ページを小規模な JavaScript ライブラリに挿入します。これによって、html ページでイベントが発生すればそれをデータ マーケット経由で Azure ML Recommendations サーバーに送信できるようになります。

![図面 1][1]

<ins>フェーズ II</ins>

2 番目のフェーズで、ページで推奨事項を表示する際は、次のオプションの 1 つを選択します。

1\. ページ レンダリング フェーズでご利用のサーバーでデータ マーケット経由で Azure ML Recommendations Server を呼び出し、推奨を取得する。結果には項目 ID の一覧が含まれます。ご利用のサーバーでは、画像や説明などのメタ データ項目で結果に情報を追加し、作成されたページをブラウザーに送信する必要があります。

![図面 2][2]

2\. もう 1 つのオプションでは、1 番目のフェーズから小さい JavaScript ファイルを使用して、推奨項目の簡単なリストを取得します。ここで受信するデータは最初のオプションよりも少なくなります。

![図面 3][3]

##2\.前提条件

1. API を使用して新しいモデルを作成する実行方法についてはクイック スタート ガイドをご覧ください。
2. &lt;dataMarketUser&gt;:&lt;dataMarketKey&gt; を base64 でエンコードします (これは基本認証で使用され、JS コードで API を呼び出すために使用できます)。


##3\.JavaScript を使用してデータ取得イベントを送信する
次の手順で送信イベントを簡単に実施できます。

1.	コードに JQuery ライブラリを含めます。次の URL の nuget からダウンロードできます。

		http://www.nuget.org/packages/jQuery/1.8.2
2.	次の URL http://1drv.ms/1Aoa1Zp から Recommendation JavaScript ライブラリを含めます。

3.	Azure ML Recommendations ライブラリを適切なパラメーターで初期化します。

		<script>
			AzureMLRecommendationsStart("<base64encoding of username:key>",
			"<model_id>");
		</script>

4.	適切なイベントを送信します。以下のすべてのイベント タイプで詳細セクションをご覧ください (クリック イベントの例)

		<script>
			if (typeof AzureMLRecommendationsEvent=="undefined") { 		
        	        	AzureMLRecommendationsEvent = [];
	                }
			AzureMLRecommendationsEvent.push({ event: "click", item: "18321116" });
		</script>


###3\.1.制限事項とブラウザー サポート
これはリファレンスの実装であり、そのままで提供されます。主なブラウザーすべてをサポートしています。

###3\.2.イベントの種類
ライブラリでサポートされるイベントには、クリック、推奨クリック、ショッピング カートに追加、シッピング カートから削除、購入の 5 種類があります。ログインというユーザー コンテキストの設定に使用されるイベントもあります。

####3\.2.1.クリック イベント
このイベントは、ユーザーが項目をクリックするたびに使用されます。通常、ユーザーが項目をクリックすると項目の詳細を示す新しいページが開きます。このページではこのイベントがトリガーされます。

パラメーター:
- event (文字列、必須) –"click"
- item (文字列、必須) – 項目の一意識別子
- itemName (文字列、省略可能) – 項目の名前
- itemDescription (文字列、省略可能) – 項目の説明
- itemCategory (文字列、省略可能) – 項目のカテゴリ
		
		<script>
			if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: "click", item: "3111718" });
		</script>

または省略可能なデータ。

		<script>
			if (typeof AzureMLRecommendationsEvent === "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({event: "click", item: "3111718", itemName: "Plane", itemDescription: "It is a big plane", itemCategory: "Aviation"});
		</script>


####3\.2.2.推奨クリック イベント
このイベントは、ユーザーが Azure ML Recommendations から推奨項目として取得された項目をクリックするたびに使用されます。通常、ユーザーが項目をクリックすると項目の詳細を示す新しいページが開きます。このページではこのイベントがトリガーされます。

パラメーター:
- event (文字列、必須) – "recommendationclick"
- item (文字列、必須) – 項目の一意識別子
- itemName (文字列、省略可能) – 項目の名前
- itemDescription (文字列、省略可能) – 項目の説明
- itemCategory (文字列、省略可能) – 項目のカテゴリ
- seeds (文字列配列、省略可能) – 推奨クエリを生成するシード
- recoList (文字列配列、省略可能) – 項目がクリックされると生成される、推奨リクエストの結果。
		
		<script>
			if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({event: "recommendationclick", item: "18899918" });
		</script>

または省略可能なデータ。

		<script>
			if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: eventName, item: "198", itemName: "Plane2", itemDescription: "It is a big plane2", itemCategory: "Default2", seeds: ["Seed1", "Seed2"], recoList: ["199", "198", "197"] 				});
		</script>


####3.2.3.ショッピング カートのイベントを追加
このイベントはユーザーがショッピング カートに項目を追加する際に使用されます。
パラメーター:
* event (文字列、必須) –"addshopcart"
* item (文字列、必須) – 項目の一意識別子
* itemName (文字列、省略可能) – 項目の名前
* itemDescription (文字列、省略可能) – 項目の説明
* itemCategory (文字列、省略可能) – 項目のカテゴリ
		
		<script>
			if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({event: "addshopcart", item: "13221118" });
		</script>

####3\.2.4.ショッピング カートのイベントを削除
このイベントはユーザーがショッピング カートから項目を削除した使用されます。

パラメーター:
* event (文字列、必須) –"removeshopcart"
* item (文字列、必須) – 項目の一意識別子
* itemName (文字列、省略可能) – 項目の名前
* itemDescription (文字列、省略可能) – 項目の説明
* itemCategory (文字列、省略可能) – 項目のカテゴリ
		
		<script>
			if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: "removeshopcart", item: "111118" });
		</script>

####3\.2.5.購入イベント
このイベントはユーザーがショッピング カートの項目を購入した際に使用されます。

パラメーター:
* event (文字列) – “購入”
* items (購入された) – 各項目を購入したエントリを保持する配列。<br><br>
購入の形式:
	* item (文字列) - 項目の一意識別子。
	* count (int または文字列) – 購入された項目の数。
	* price (float 型か文字列) – 省略可能フィールド – 項目の価格。

次の例では、3 つの項目の購入を示します (33、34、35)。2 つはすべてのフィールドが入力されており (項目、数、価格)、1 つ (項目 34) は価格が入力されていません。

		<script>
			if ( typeof AzureMLRecommendationsEvent == "undefined"){ AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: "purchase", items: [{ item: "33", count: "1", price: "10" }, { item: "34", count: "2" }, { item: "35", count: "1", price: "210" }] });
		</script>

####3\.2.6.ユーザー ログイン イベント
Azure ML Recommendations のイベント ライブラリでは、同じブラウザーでのイベントの特定に使用される cookie が作成され使用されます。モデル結果を向上するため、Azure ML Recommendations ではユーザーに一意識別子を設定し、cookie の使用状況をオーバーライドします。

このイベントは、ユーザーがサイトにログインした後に使用される必要があります。

パラメーター:
* event (文字列) –"userlogin"
* ユーザー (文字列) – ユーザーの一意識別子。
	<script>
		if (typeof 	AzureMLRecommendationsEvent=="undefined	") { AzureMLRecommendationsEvent = ; }
			AzureMLRecommendationsEvent.push({event: "userlogin", user: “ABCD10AA” });
		</script>

##4\.JavaScript で推奨設定を使用します。
推奨設定を使用するコードは、クライアント Web ページで、一部の JavaScript イベントによってトリガーされます。推奨設定の応答には、推奨項目の ID、名前、評価が含まれます。このオプションは、推奨項目の一覧表示に対してのみ使うことをお勧めします – 項目のメタデータの追加などのより複雑な処理は、サーバー側の統合で行われます。

###4\.1 推奨を使用する
推奨を使用するには、ページに JavaScript ライブラリを含め、AzureMLRecommendationsStart を呼び出す必要があります。セクション 2 をご覧ください。

1 つまたは複数の項目の推奨を使用するには、AzureMLRecommendationsGetI2IRecommendation というメソッドを呼び出す必要があります。

パラメーター:
* items (文字列の配列) – 推奨を取得するための 1 つまたは複数の項目Fbt ビルドを使用している場合は、ここでは 1 つの項目のみ設定できます。
* numberOfResults (int) – 必要な結果の数。
* includeMetadata (ブール値、省略可能) - 'true' に設定されると、メタデータ フィールドが結果に入力されます。
* Processing function - 返された推奨を処理する関数。次の配列として、データが返されます。
	* Item – 項目の一意の ID
	* name – 項目名 (カタログに存在する場合)
	* rating – 推奨の評価
	* metadata - 項目のメタデータを表す文字列。

例: 次のコードでは、項目 "64f6eb0d-947a-4c18-a16c-888da9e228ba" の 8 つの推奨をリクエストしています (includeMetadata は指定していません - メタデータが必要ないことが暗示的に示されているため)。その後、結果をバッファにつなげます。

		<script>
 			var reco = AzureMLRecommendationsGetI2IRecommendation(["64f6eb0d-947a-4c18-a16c-888da9e228ba"], 8, false, function (reco) {
 				var buff = "";
 				for (var ii = 0; ii < reco.length; ii++) {
   					buff += reco[ii].item + "," + reco[ii].name + "," + reco[ii].rating + "\n";
 				}
 				alert(buff);
			});
		</script>


[1]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing1.png
[2]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing2.png
[3]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing3.png
 

<!---HONumber=Oct15_HO3-->