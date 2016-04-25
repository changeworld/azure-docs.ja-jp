<properties
	pageTitle="DocumentDB コネクタ用 Power BI チュートリアル | Microsoft Azure"
	description="この Power BI のチュートリアルでは、JSON をインポートしたり、洞察に富むレポートを作成したり、DocumentDB および Power BI コネクタを使用してデータを視覚化する方法を説明します。"
	keywords="power bi チュートリアル, データの視覚化, power bi コネクタ"
	services="documentdb"
	authors="h0n"
	manager="jhubbard"
	editor="mimig"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="hawong"/>

# DocumentDB 用の Power BI チュートリアル: Power BI コネクタでデータを視覚化する
[PowerBI.com](https://powerbi.microsoft.com/) は、ユーザーとその組織の重要なデータを使用してダッシュボードおよびレポートを作成し、共有することができるオンライン サービスです。Power BI Desktop は、各種データ ソースのデータを取得し、データの結合と変換および強力なレポートと視覚エフェクトの作成を行い、レポートを Power BI に発行することができるレポート作成専用ツールです。最新バージョンの Power BI Desktop では、Power BI 用 DocumentDB コネクタ経由で DocumentDB アカウントに接続できるようになりました。

この Power BI チュートリアルでは、Power BI Desktop で DocumentDB アカウントに接続し、ナビゲーターを使用してデータを抽出するコレクションに移動して、Power BI Desktop クエリ エディターで JSON データを表形式に変換し、レポートを作成して PowerBI.com に発行する手順について説明します。

この Power BI チュートリアルを完了すると、次の項目について説明できるようになります。

-	Power BI Desktop では、どのようにして DocumentDB のデータでレポートを作成できますか?
-	Power BI Desktop では、どのようにして DocumentDB アカウントに接続できますか?
-	Power BI Desktop では、どのようにしてコレクションからデータを取得できますか?
-	Power BI Desktop では、どのようにして入れ子になった JSON データを変換できますか?
-	PowerBI.com では、どのようにしてレポートを発行して共有できますか?

## 前提条件

この Power BI チュートリアルの手順を実行する前に、次のものを備えておく必要があります。

- [最新バージョンの Power BI Desktop](https://powerbi.microsoft.com/desktop)。
- Azure DocumentDB アカウント内のデモ アカウントまたはデータへのアクセス。
	- デモ アカウントには、このチュートリアルで示される火山データが設定されています。このデモ アカウントは、どの SLA の制約も受けず、目的がデモンストレーションに限定されています。Microsoft では、事前の通知や理由の提示なしでいつでもこのデモ アカウントに変更を加える権利を保有しています。これには、アカウントの終了、キーの変更、アクセスの制限、データの変更と削除などが含まれます。
		- URL: https://analytics.documents.azure.com
		- 読み取り専用キー: MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==
	- または、独自のアカウントを作成する場合は、「[Azure ポータルを使用して DocumentDB データベース アカウントを作成する](https://azure.microsoft.com/documentation/articles/documentdb-create-account/)」を参照してください。その後で、このチュートリアルで使用されるデータと同様のサンプル火山データ (ただし、GeoJSON ブロックは含まれていません) を取得するために、[NOAA サイト](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5)にアクセスし、[DocumentDB データ移行ツール](https://azure.microsoft.com/documentation/articles/documentdb-import-data/)を使用してデータをインポートしてください。


PowerBI.com でレポートを共有するには、PowerBI.com のアカウントが必要です。Power BI (無料) および Power BI Pro の詳細については、[https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing) を参照してください。

## 作業を開始する
このチュートリアルでは、ユーザーが世界中の火山を研究している地質学者であると仮定します。火山データは DocumentDB アカウントに格納されており、JSON ドキュメントは次のようになっています。

	{
    	"Volcano Name": "Rainier",
   		"Country": "United States",
  		"Region": "US-Washington",
  		"Location": {
    		"type": "Point",
    		"coordinates": [
      		-121.758,
      		46.87
    		]
  		},
  		"Elevation": 4392,
  		"Type": "Stratovolcano",
  		"Status": "Dendrochronology",
  		"Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
	}

DocumentDB アカウントから火山データを取得し、次のような対話型の Power BI レポートでデータを視覚化する必要があります。

![この Power BI チュートリアルを Power BI コネクタを使用して完了することで、Power BI Desktop 火山レポートでデータを視覚化できます。](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportfinal.png)

試してみる準備はできましたか。 それでは始めましょう。


1. ワークステーションで Power BI Desktop を実行します。
2. Power BI Desktop を起動すると、*ようこそ*画面が表示されます。

	![Power BI Desktop のようこそ画面- Power BI コネクタ](./media/documentdb-powerbi-visualize/power_bi_connector_welcome.png)

3. *ようこそ*画面から直接、**データを取得し**、**最近使ったソース**を確認し、**他のレポートを開く**ことができます。右上隅の [X] をクリックして画面を閉じます。Power BI Desktop の **[レポート]** ビューが表示されます。

	![Power BI Desktop のレポート ビュー- Power BI コネクタ](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportview.png)

4. **[ホーム]** リボンをクリックし、**[データの取得]** をクリックします。**[データの取得]** ウィンドウが表示されます。

5. **[Azure]** をクリックし、**[Microsoft Azure DocumentDB (ベータ版)]** を選択して、**[接続]** をクリックします。**[Microsoft Azure DocumentDB 接続]** ウィンドウが表示されます。

	![Power BI Desktop のデータの取得 - Power BI コネクタ](./media/documentdb-powerbi-visualize/power_bi_connector_pbigetdata.png)

6. 次のように、データを取得する DocumentDB アカウント エンドポイント URL を指定し、**[OK]** をクリックします。URL は、Azure ポータルの **[キー]** ブレードで URI ボックスから取得するか、上記で指定されたデモ アカウント情報を使用することができます。詳細については、[キー](documentdb-manage-account.md#keys)に関するページを参照してください。


	*注: このチュートリアルでは、オプションのフィールドであるデータベース名、コレクション名、および SQL ステートメントは指定しません。代わりに、ナビゲーターを使用して、データを取得するデータベースとコレクションを選択します。*

    ![DocumentDB Power BI コネクタの Power BI チュートリアル - Desktop Connect ウィンドウ](./media/documentdb-powerbi-visualize/power_bi_connector_pbiconnectwindow.png)

7. このエンドポイントに初めて接続した場合は、アカウント キーの入力を求められます。アカウント キーを入力し、**[接続]** をクリックします。

	*注: レポートを作成する際は読み取り専用キーを使用することをお勧めします。これにより、マスター キーが不用意に公開される潜在的なセキュリティ リスクを抑えることができます。読み取り専用キーは、Azure ポータルの [読み取り専用キー] ブレードで取得できます。また、上記のデモ アカウント情報を使用することもできます。*

    ![DocumentDB Power BI コネクタの Power BI チュートリアル - アカウント キー](./media/documentdb-powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)

8. アカウントが正常に接続されると、**ナビゲーター**が表示されます。**ナビゲーター**には、アカウントで利用できるデータベースの一覧が表示されます。
9. レポートのデータを取得するデータベースをクリックして展開します。データベースの下にコレクションの一覧が表示されます。  

10. これで、データを取得するコレクション (volcano1 など) を選択できます。

	*注: プレビュー ウィンドウに、**Record** アイテムの一覧が表示されます。Power BI では、ドキュメントは **Record** タイプとして表されます。同様に、ドキュメント内の入れ子になった JSON ブロックも、**Record** として表されます。*

    ![DocumentDB Power BI コネクタの Power BI チュートリアル - ナビゲーター ウィンドウ](./media/documentdb-powerbi-visualize/power_bi_connector_pbinavigator.png)

11. データを変換するために、**[編集]** をクリックしてクエリ エディターを起動します。

## JSON ドキュメントをフラット化して変換する
1. Power BI クエリ エディターの中央のウィンドウに **[ドキュメント]** 列が表示されます。![Power BI Desktop クエリ エディター](./media/documentdb-powerbi-visualize/power_bi_connector_pbiqueryeditor.png)

2. **[ドキュメント]** 列ヘッダーの右側にある展開コントロールをクリックします。フィールドの一覧を示すコンテキスト メニューが表示されます。Volcano Name、Country、Region、Location、Elevation、Type、Status、Last Know Eruption など、レポートに必要なフィールドを選択し、**[OK]** をクリックします。

	![DocumentDB Power BI コネクタの Power BI チュートリアル - ドキュメントの展開](./media/documentdb-powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)

3. 中央のウィンドウに、選択したフィールドを含む結果のプレビューが表示されます。

	![DocumentDB Power BI コネクタの Power BI チュートリアル - フラット化された結果](./media/documentdb-powerbi-visualize/power_bi_connector_pbiresultflatten.png)

4. この例では、Location プロパティは、ドキュメント内の GeoJSON ブロックです。ご覧のように、Power BI Desktop では Location は **Record** タイプとして表されます。
5. [Location] 列ヘッダーの右側にある展開コントロールをクリックします。type フィールドと coordinates フィールドを含むコンテキスト メニューが表示されます。coordinates フィールドを選択して **[OK]** をクリックします。

    ![DocumentDB Power BI コネクタの Power BI チュートリアル - 場所レコード](./media/documentdb-powerbi-visualize/power_bi_connector_pbilocationrecord.png)

6. 中央のウィンドウに、**List** タイプの [coordinates] 列が表示されます。このチュートリアルの冒頭に示されているように、ここで使用する GeoJSON データは、Latitude 値と Longitude 値が coordinates 配列に記録された Point タイプです。

	*注: coordinates[0] 要素は経度を表し、coordinates[1] は緯度を表します。* ![DocumentDB Power BI コネクタの Power BI チュートリアル - 座標一覧](./media/documentdb-powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)

7. coordinates 配列をフラット化するために、LatLong という **カスタム列**を作成します。**[列の追加]** リボンを選択し、**[カスタム列の追加]** をクリックします。**[カスタム列の追加]** ウィンドウが表示されます。

8. "LatLong" など、新しい列の名前を入力します。

9. 次に、新しい列のカスタム式を指定します。たとえば、次のような式を使用して、緯度と経度の値をコンマ区切りで連結します: Text.From([coordinates]{1})&","&Text.From([coordinates]{0})。**[OK]** をクリックします。

    *注: DAX 関数など Data Analysis Expressions(DAX) の詳細については、[Power BI Desktop における DAX の基本](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop)に関するページを参照してください。*

    ![DocumentDB Power BI コネクタの Power BI チュートリアル - カスタム列の追加](./media/documentdb-powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. これで、中央のウィンドウには Latitude と Longitude の値がコンマ区切りで設定された新しい [LatLong] 列が表示されます。

	![DocumentDB Power BI コネクタの Power BI チュートリアル - カスタム LatLong 列](./media/documentdb-powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)

11. このように、データをフラット化して表形式に変換することができました。クエリ エディターのすべての機能を活用して、DocumentDB のデータを整形および変換することができます。たとえば、Elevation のデータ型を**10 進数**に変更するには、**[ホーム]** リボンで **[データ型]** を変更します。

    ![DocumentDB Power BI コネクタの Power BI チュートリアル - 列の型の変更](./media/documentdb-powerbi-visualize/power_bi_connector_pbichangetype.png)

12. **[終了して適用]** をクリックしてデータ モデルを保存します。

    ![DocumentDB Power BI コネクタの Power BI チュートリアル - 閉じて適用](./media/documentdb-powerbi-visualize/power_bi_connector_pbicloseapply.png)

## レポートを作成する
Power BI Desktop レポート ビューは、データを視覚化するためにレポート作成を開始できる場所です。**[レポート]** キャンバスにフィールドをドラッグ アンド ドロップすることにより、レポートを作成できます。

![Power BI Desktop のレポート ビュー- Power BI コネクタ](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportview2.png)

レポート ビューには以下が表示されます。

 1. **[フィールド]** ウィンドウ。ここには、レポートに使用できるフィールドと共にデータ モデルの一覧が表示されます。

 2. **[視覚エフェクト]** ウィンドウ。レポートに 1 つまたは複数の視覚エフェクトを含めることができます。**[視覚エフェクト]** ウィンドウで、ニーズに合った視覚エフェクトの種類を指定します。

 3. **[レポート]** キャンバス。ここでは、レポート用の視覚エフェクトを作成します。

 4. **[レポート]** ページ。Power BI Desktop では、複数のレポート ページを追加できます。

単純な対話型マップ ビュー レポートを作成する基本的な手順を次に示します。

1. 例として、個々の火山の場所を示すマップ ビューを作成します。**[視覚エフェクト]** ウィンドウで、ビジュアルの種類として、上記のスクリーンショットで強調表示されている "マップ" をクリックします。**[レポート]** キャンバスに、ビジュアルの種類のマップが描かれます。**[視覚化エフェクト]** ウィンドウには、ビジュアルの種類のマップに関連するプロパティのセットも表示されます。

2. ここで、**[フィールド]** ウィンドウの LatLong フィールドを **[視覚エフェクト]** ウィンドウの **Location** プロパティにドラッグ アンド ドロップします。
3. 次に、Volcano Name フィールドを **Legend** プロパティにドラッグ アンド ドロップします。  

4. さらに、Elevation フィールドを **Values** プロパティにドラッグ アンド ドロップします。

5. ここで、マップ ビジュアルに複数のバブルが表示されます。バブルは個々の火山の場所を示し、バブルのサイズは火山の高度と相関性があります。

6. これで、基本的なレポートを作成できました。別の視覚エフェクトを追加することで、レポートをさらにカスタマイズできます。この例では、レポートを対話的にするために Volcano Type スライサーを追加しました。

    ![DocumentDB の Power BI チュートリアルの完了時の最終的な Power BI Desktop レポートのスクリーン ショット](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportfinal.png)

## レポートを発行して共有する
レポートを共有するには、PowerBI.com のアカウントが必要です。

1. Power BI Desktop で、**[ホーム]** リボンをクリックします。
2. **[発行]** をクリックします。PowerBI.com アカウントのユーザー名とパスワードの入力が求められます。
3. 資格情報が認証されると、PowerBI.com アカウントにレポートが発行されます。
4. これにより、PowerBI.com でレポートを共有できます。

## 次のステップ
- Power BI の詳細については、[ここ](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)をクリックしてください。
- DocumentDB の詳細については、[ここ](https://azure.microsoft.com/documentation/services/documentdb/)をクリックしてください。

<!---HONumber=AcomDC_0413_2016-->