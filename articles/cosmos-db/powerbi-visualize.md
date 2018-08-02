---
title: Azure Cosmos DB コネクタ用 Power BI チュートリアル | Microsoft Docs
description: この Power BI のチュートリアルでは、JSON をインポートしたり、洞察に富むレポートを作成したり、Cosmos DB および Power BI コネクタを使用してデータを視覚化する方法を説明します。
keywords: power bi チュートリアル, データの視覚化, power bi コネクタ
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: sngun
ms.openlocfilehash: 3734c30dad0735164d4501d1d63d8ffe301396a3
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249116"
---
# <a name="power-bi-tutorial-for-azure-cosmos-db-visualize-data-using-the-power-bi-connector"></a>Azure Cosmos DB 用の Power BI チュートリアル: Power BI コネクタでデータを視覚化する
[PowerBI.com](https://powerbi.microsoft.com/) は、ユーザーとその組織の重要なデータを使用してダッシュボードおよびレポートを作成し、共有することができるオンライン サービスです。  Power BI Desktop は、各種データ ソースのデータを取得し、データの結合と変換および強力なレポートと視覚エフェクトの作成を行い、レポートを Power BI に発行することができるレポート作成専用ツールです。  最新バージョンの Power BI Desktop では、Power BI 用 Azure Cosmos DB コネクタ経由で Azure Cosmos DB アカウントに接続できるようになりました。   

この Power BI チュートリアルでは、Power BI Desktop で Azure Cosmos DB アカウントに接続し、ナビゲーターを使用してデータを抽出するコレクションに移動して、Power BI Desktop クエリ エディターで JSON データを表形式に変換し、レポートを作成して PowerBI.com に発行する手順について説明します。

この Power BI チュートリアルを完了すると、次の項目について説明できるようになります。  

* Power BI Desktop では、どのようにして Azure Cosmos DB のデータでレポートを作成できますか?
* Power BI Desktop では、どのようにして Azure Cosmos DB アカウントに接続できますか?
* Power BI Desktop では、どのようにしてコレクションからデータを取得できますか?
* Power BI Desktop では、どのようにして入れ子になった JSON データを変換できますか?
* PowerBI.com では、どのようにしてレポートを発行して共有できますか?

> [!NOTE]
> Azure Cosmos DB 用の Power BI コネクタは、データの抽出および変換のために Power BI Desktop に接続します。 Power BI Desktop で作成されたレポートは、後で PowerBI.com に発行できます。 PowerBI.com では、Azure Cosmos DB データの直接抽出および変換は実行できません。 

> [!NOTE]
> Power BI コネクタによる Azure Cosmos DB への接続は、現在は Azure Cosmos DB SQL アカウントと MongoDB API アカウントでのみサポートされています。 MongoDB API を使用して Azure Cosmos DB を Power BI に接続するには、[Simba MongoDB ODBC Driver](http://www.simba.com/drivers/mongodb-odbc-jdbc/) を使用する必要があります。

## <a name="prerequisites"></a>前提条件
この Power BI チュートリアルの手順に従う前に、次のリソースにアクセスできることを確認してください。

* [最新バージョンの Power BI Desktop](https://powerbi.microsoft.com/desktop)。
* Azure Cosmos DB アカウント内のデモ アカウントまたはデータへのアクセス。
  * デモ アカウントには、このチュートリアルで示される火山データが設定されています。 このデモ アカウントは、どの SLA の制約も受けず、目的がデモンストレーションに限定されています。  当社は、事前の通知や理由なく、いつでもこのデモ アカウントに変更を加える権利を留保しています。これには、アカウントの終了、キーの変更、アクセスの制限、データの変更および削除が含まれますが、これに限定されるわけではありません。
    * URL: https://analytics.documents.azure.com
    * 読み取り専用キー: MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==
  * または、独自のアカウントを作成する場合は、[Azure Portal を使用した Azure Cosmos DB データベース アカウントの作成](https://azure.microsoft.com/documentation/articles/create-account/)に関するページをご覧ください。 その後で、このチュートリアルで使用されるデータと同様のサンプル火山データ (ただし、GeoJSON ブロックは含まれていません) を取得するために、[NOAA サイト](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5)にアクセスし、[Azure Cosmos DB データ移行ツール](import-data.md)を使用してデータをインポートしてください。

PowerBI.com でレポートを共有するには、PowerBI.com のアカウントが必要です。  Power BI (無料) および Power BI Pro について詳しくは、[https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing) をご覧ください。

## <a name="lets-get-started"></a>作業を開始する
このチュートリアルでは、ユーザーが世界中の火山を研究している地質学者であると仮定します。  火山データは Azure Cosmos DB アカウントで格納されており、JSON ドキュメントは次のサンプル ドキュメントのようになっています。

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

Azure Cosmos DB アカウントから火山データを取得し、次のレポートのような対話型の Power BI レポートでデータを視覚化する必要があります。

![この Power BI チュートリアルを Power BI コネクタを使用して完了することで、Power BI Desktop 火山レポートでデータを視覚化できます。](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

試してみる準備はできましたか。 それでは始めましょう。

1. ワークステーションで Power BI Desktop を実行します。
2. Power BI Desktop を起動すると、 *ようこそ* 画面が表示されます。
   
    ![Power BI Desktop のようこそ画面- Power BI コネクタ](./media/powerbi-visualize/power_bi_connector_welcome.png)
3. *ようこそ画面*から、直接**データを取得**し、**最近使ったソース**を確認し、**他のレポートを開く**ことができます。  右上隅の [X] をクリックして画面を閉じます。 Power BI Desktop の **[レポート]** ビューが表示されます。
   
    ![Power BI Desktop のレポート ビュー- Power BI コネクタ](./media/powerbi-visualize/power_bi_connector_pbireportview.png)
4. **[ホーム]** リボンをクリックし、**[データの取得]** をクリックします。  **[データの取得]** ウィンドウが表示されます。
5. **[Azure]** をクリックし、**[Azure Cosmos DB (ベータ版)]** を選択して、**[接続]** をクリックします。 

    ![Power BI Desktop のデータの取得 - Power BI コネクタ](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   
6. **[Preview Connector] \(プレビュー コネクタ)** ページで、**[続行]** をクリックします。 **[Azure Cosmos DB]** ウィンドウが表示されます。
7. 次のように、データを取得する Azure Cosmos DB アカウント エンドポイント URL を指定し、**[OK]** をクリックします。 独自のアカウントを使用するには、Azure Portal の **[[キー]](manage-account.md#keys)** ブレードにある [URI] ボックスから URL を取得できます。 デモ アカウントを使用するには、URL として `https://analytics.documents.azure.com` を入力します。 
   
    データベース名、コレクション名、および SQL ステートメントは省略可能なフィールドなので、空白のままにします。  代わりに、ナビゲーターを使用して、データを取得するデータベースとコレクションを選択します。
   
    ![Azure Cosmos DB Power BI コネクタの Power BI チュートリアル - Desktop Connect ウィンドウ](./media/powerbi-visualize/power_bi_connector_pbiconnectwindow.png)
8. このエンドポイントに初めて接続している場合は、アカウント キーの入力を求められます。 独自のアカウントの場合は、Azure Portal の **[[Read-only Keys] (読み取り専用キー)](manage-account.md#keys)** ブレードにある **[主キー]** ボックスからキーを取得します。 デモ アカウントの場合、キーは `MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==` です。 適切なキーを入力し、**[接続]** をクリックします。
   
    レポートを作成する際は読み取り専用キーを使用することをお勧めします。  これにより、マスター キーが不用意に公開される潜在的なセキュリティ リスクを抑えることができます。 読み取り専用キーは、Azure ポータルの [[キー]](manage-account.md#keys) ブレードで取得できます。また、上記のデモ アカウント情報を使用することもできます。
   
    ![Azure Cosmos DB Power BI コネクタの Power BI チュートリアル - アカウント キー](./media/powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)
    
    > [!NOTE] 
    > "The specified database was not found. (指定されたデータベースが見つかりませんでした。)" というエラーが表示された場合は、 この [Power BI の問題](https://community.powerbi.com/t5/Issues/Document-DB-Power-BI/idi-p/208200)にある回避手順を参照してください。
    
9. アカウントが正常に接続されると、 **ナビゲーター** ウィンドウが表示されます。  **ナビゲーター** には、アカウントで利用できるデータベースの一覧が表示されます。
10. レポート用のデータを取得するデータベースをクリックして展開します。デモ アカウントを使用する場合は、**[volcanodb]** を選択します。   
11. 次に、取得するデータを含むコレクションを選択します。 デモ アカウントを使用する場合は、 **[volcano1]** を選択します。
    
    プレビュー ウィンドウに、 **Record** アイテムの一覧が表示されます。  Power BI では、ドキュメントは **Record** タイプとして表されます。 同様に、ドキュメント内の入れ子になった JSON ブロックも、 **Record**として表されます。
    
    ![Azure Cosmos DB Power BI コネクタの Power BI チュートリアル - ナビゲーション ウィンドウ](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. データを変換するには、**[編集]** をクリックしてクエリ エディターを新しいウィンドウで起動します。

## <a name="flattening-and-transforming-json-documents"></a>JSON ドキュメントをフラット化して変換する
1. [Power BI Query Editor] \(Power BI クエリ エディター) ウィンドウに切り替えます。中央のウィンドウに **[ドキュメント]** 列が表示されます。
   ![Power BI Desktop クエリ エディター](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. **[ドキュメント]** 列ヘッダーの右側にある展開コントロールをクリックします。  フィールドの一覧を示すコンテキスト メニューが表示されます。  Volcano Name、Country、Region、Location、Elevation、Type、Status、Last Know Eruption など、レポートに必要なフィールドを選択します。 **[Use original column name as prefix]\(元の列名をプレフィックスとして使用する\)** チェックボックスをオフにし、**[OK]** をクリックします。
   
    ![Azure Cosmos DB Power BI コネクタの Power BI チュートリアル - ドキュメントの展開](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. 中央のウィンドウに、選択したフィールドを含む結果のプレビューが表示されます。
   
    ![Azure Cosmos DB Power BI コネクタの Power BI チュートリアル - フラット化された結果](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. この例では、Location プロパティは、ドキュメント内の GeoJSON ブロックです。  ご覧のように、Power BI Desktop では Location は **Record** タイプとして表されます。  
5. [Document.Location] 列ヘッダーの右側にある展開コントロールをクリックします。  type フィールドと coordinates フィールドを含むコンテキスト メニューが表示されます。  coordinates フィールドを選択してみましょう。**[Use original column name as prefix]\(元の列名をプレフィックスとして使用する\)** が選択されていないことを確認し、**[OK]** をクリックします。
   
    ![Azure Cosmos DB Power BI コネクタの Power BI チュートリアル - 場所レコード](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. 中央のウィンドウに、 **List** タイプの [coordinates] 列が表示されます。  このチュートリアルの冒頭に示されているように、ここで使用する GeoJSON データは、Latitude 値と Longitude 値が coordinates 配列に記録された Point タイプです。
   
    coordinates[0] 要素は経度を表し、coordinates[1] は緯度を表します。
    ![Azure Cosmos DB Power BI コネクタの Power BI チュートリアル - 座標一覧](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. coordinates 配列をフラット化するために、LatLong という **カスタム列** を作成します。  **[列の追加]** リボンを選択し、**[カスタム列]** をクリックします。  **[カスタム列]** ウィンドウが表示されます。
8. "LatLong" など、新しい列の名前を入力します。
9. 次に、新しい列のカスタム式を指定します。  たとえば、次のような式を使用して、緯度と経度の値をコンマ区切りで連結します: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`。 Click **OK**.
   
    DAX 関数など Data Analysis Expressions(DAX) の詳細については、「 [Power BI Desktop における DAX の基本事項](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop)」を参照してください。
   
    ![Azure Cosmos DB Power BI コネクタの Power BI チュートリアル - カスタム列の追加](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. これで、中央のウィンドウに、値が入力された新しい LatLong 列が表示されます。
    
    ![Azure Cosmos DB コネクタの Power BI チュートリアル - カスタム LatLong 列](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    新しい列でエラーが発生する場合は、クエリ設定で適用された手順が次の図と一致することを確認してください。
    
    ![適用された手順は、Source、Navigation、Expanded Document、Expanded Document.Location、Added Custom である必要がある](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    手順が異なる場合は、余分な手順を削除し、カスタム列をもう一度追加してみてください。 

11. **[終了して適用]** をクリックしてデータ モデルを保存します。
    
    ![Azure Cosmos DB Power BI コネクタの Power BI チュートリアル - 閉じて適用](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>レポートを作成する
Power BI Desktop レポート ビューは、データを視覚化するためにレポート作成を開始できる場所です。  **[レポート]** キャンバスにフィールドをドラッグ アンド ドロップすることにより、レポートを作成できます。

![Power BI Desktop のレポート ビュー- Power BI コネクタ](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

レポート ビューには以下が表示されます。

1. **[フィールド]** ウィンドウ。ここで、レポートに使用できるデータ モデルとフィールドの一覧を確認できます。
2. **[視覚エフェクト]** ウィンドウ。 レポートに 1 つまたは複数の視覚エフェクトを含めることができます。  **[視覚エフェクト]** ウィンドウで、ニーズに合った視覚エフェクトの種類を指定します。
3. **[レポート]** キャンバス。ここで、レポート用のビジュアルを作成します。
4. **[レポート]** ページ。 Power BI Desktop では、複数のレポート ページを追加できます。

単純な対話型マップ ビュー レポートを作成する基本的な手順を次に示します。

1. 例として、個々の火山の場所を示すマップ ビューを作成します。  **[視覚エフェクト]** ウィンドウで、ビジュアルの種類として、上記のスクリーンショットで強調表示されているマップをクリックします。  **[レポート]** キャンバスに、ビジュアルの種類のマップが描かれます。  **[視覚化エフェクト]** ウィンドウには、ビジュアルの種類のマップに関連するプロパティのセットも表示されます。
2. ここで、**[フィールド]** ウィンドウの LatLong フィールドを **[視覚エフェクト]** ウィンドウの **Location** プロパティにドラッグ アンド ドロップします。
3. 次に、Volcano Name フィールドを **Legend** プロパティにドラッグ アンド ドロップします。  
4. さらに、Elevation フィールドを **Size** プロパティにドラッグ アンド ドロップします。  
5. ここで、マップ ビジュアルに複数のバブルが表示されます。バブルは個々の火山の場所を示し、バブルのサイズは火山の高度と相関性があります。
6. これで、基本的なレポートを作成できました。  別の視覚エフェクトを追加することで、レポートをさらにカスタマイズできます。  この例では、レポートを対話的にするために Volcano Type スライサーを追加しました。  
   
    ![Azure Cosmos DB の Power BI チュートリアルの完了時の最終的な Power BI Desktop レポートのスクリーンショット](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)
7. [ファイル] メニューの **[保存]** をクリックして、ファイルを PowerBITutorial.pbix として保存します。

## <a name="publish-and-share-your-report"></a>レポートを発行して共有する
レポートを共有するには、PowerBI.com のアカウントが必要です。

1. Power BI Desktop で、 **[ホーム]** リボンをクリックします。
2. **[発行]** をクリックします。  PowerBI.com アカウントのユーザー名とパスワードの入力が求められます。
3. 資格情報が認証されると、選択済みの宛先にレポートが発行されます。
4. レポートを PowerBI.com に表示して共有するには、 **['PowerBITutorial.pbix' を Power BI で開く]** をクリックします。
   
    ![Power BI への発行が成功しました! Power BI でチュートリアルを開く](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>PowerBI.com でのダッシュボードの作成
レポートが用意できたので、PowerBI.com で共有しましょう。

レポートを Power BI Desktop から PowerBI.com に発行すると、PowerBI.com テナントに **[レポート]** と **[データセット]** が生成されます。 たとえば、**PowerBITutorial** という名前のレポートを PowerBI.com に発行すると、PowerBI.com の **[レポート]** セクションと **[データセット]** セクションの両方に PowerBITutorial が表示されます。

   ![PowerBI.com の新しいレポートとデータセットのスクリーンショット](./media/powerbi-visualize/powerbi-reports-datasets.png)

共有可能なダッシュ ボードを作成するには、PowerBI.com レポートで **[ライブ ページをピン留めする]** ボタンをクリックします。

   ![PowerBI.com の新しいレポートとデータセットのスクリーンショット](./media/powerbi-visualize/power-bi-pin-live-tile.png)

その後、「 [レポートからタイルをピン留め](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) 」の指示に従って、新しいダッシュボードを作成します。 

ダッシュボードを作成する前に、レポートをその場で変更することもできます。 ただし、変更は Power BI Desktop で実行し、変更後のレポートを PowerBI.com にもう一度発行することをお勧めします。

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

![Screenshot of Refresh Now in PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    ![Screenshot of the Schedule Refresh in PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>次の手順
* Power BI の詳細については、「 [Power BI の概要](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)」を参照してください。
* Azure Cosmos DB について詳しくは、[Azure Cosmos DB ドキュメントのランディング ページ](https://azure.microsoft.com/documentation/services/cosmos-db/)をご覧ください。

