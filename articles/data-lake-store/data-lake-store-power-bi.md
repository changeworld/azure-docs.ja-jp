---
title: "Power BI を使用して Data Lake Store のデータを分析する | Microsoft Docs"
description: "Power BI を使用して Azure Data Lake Store に格納されているデータを分析する"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/05/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2cd2ef87032d1691f2c56a9da44ce29ccb4e9963


---
# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>Power BI を使用して Data Lake Store のデータを分析する
この記事では、Power BI Desktop を使用して Azure Data Lake ストアに格納されているデータを分析し、視覚化する方法を説明します。

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Data Lake Store アカウント**。 「 [Azure ポータルで Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。 この記事では、既に **mybidatalakestore** という Data Lake Store アカウントを作成しており、サンプル データ ファイル (**Drivers.txt**) をそのアカウントにアップロードしていることを前提とします。 このサンプル ファイルは、 [Azure Data Lake Git リポジトリ](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)からダウンロードできます。
* **Power BI Desktop**。 これは [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=45331) からダウンロードできます。 

## <a name="create-a-report-in-power-bi-desktop"></a>Power BI Desktop でレポートを作成する
1. コンピューターで Power BI Desktop を起動します。
2. **[ホーム]** リボンの **[データの取得]** をクリックしてから [詳細] をクリックします。 **[データの取得]** ダイアログ ボックスで、**[Azure]**、**[Azure Data Lake Store]**、**[接続]** の順にクリックします。
   
    ![Data Lake Store に接続する](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Connect to Data Lake Store")
3. 開発段階にあるコネクタに関するダイアログ ボックスが表示された場合は、続行することを選択します。
4. **[Microsoft Azure Data Lake Store]** ダイアログ ボックスで、Data Lake Store アカウントの URL を指定してから **[OK]** をクリックします。
   
    ![Data Lake Store の URL](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL for Data Lake Store")
5. 次のダイアログ ボックスで、 **[サインイン]** をクリックして Data Lake Store アカウントにサインインします。 組織のサインイン ページにリダイレクトされます。 画面の指示に従ってアカウントにサインインします。
   
    ![Data Lake Store にサインインする](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Sign into Data Lake Store")
6. 正常にサインインしたら、 **[接続]**をクリックします。
   
    ![Data Lake Store に接続する](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Connect to Data Lake Store")
7. 次のダイアログ ボックスに、Data Lake Store アカウントにアップロードしたファイルが表示されます。 情報を確認してから **[ロード]**をクリックします。
   
    ![Data Lake Store からデータをダウンロードする](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Load data from Data Lake Store")
8. データが Power BI に正常に読み込まれたら、 **[フィールド]** タブに以下のフィールドが表示されます。
   
    ![インポートされたフィールド](./media/data-lake-store-power-bi/imported-fields.png "Imported fields")
   
    ただし、ここではデータを視覚化して分析するために、以下のフィールドごとにデータを使用可能にします。
   
    ![必要なフィールド](./media/data-lake-store-power-bi/desired-fields.png "Desired fields")
   
    次の手順では、必要な形式でインポートされたデータを変換するためにクエリを更新します。
9. **[ホーム]** リボンの **[クエリの編集]** をクリックします。
   
    ![[クエリの編集]](./media/data-lake-store-power-bi/edit-queries.png "Edit queries")
10. クエリ エディターで、**[コンテンツ]** 列の下にある **[バイナリ]** をクリックします。
    
    ![[クエリの編集]](./media/data-lake-store-power-bi/convert-query1.png "Edit queries")
11. アップロードした **Drivers.txt** ファイルを表すファイル アイコンが表示されます。 このファイルを右クリックしてから **[CSV]**をクリックします。    
    
    ![[クエリの編集]](./media/data-lake-store-power-bi/convert-query2.png "Edit queries")
12. 出力は次のようになります。 これで、データは視覚エフェクトの作成に使用できる形式で利用できるようになりました。
    
    ![[クエリの編集]](./media/data-lake-store-power-bi/convert-query3.png "Edit queries")
13. **[ホーム]** リボンで、**[閉じて適用]**、**[閉じて適用]** の順にクリックします。
    
    ![[クエリの編集]](./media/data-lake-store-power-bi/load-edited-query.png "Edit queries")
14. クエリが更新されたら、 **[フィールド]** タブに、視覚エフェクトで使用可能な新しいフィールドが表示されます。
    
    ![更新されたフィールド](./media/data-lake-store-power-bi/updated-query-fields.png "Updated fields")
15. 特定の国の各都市のドライバーを示す円グラフを作成してみましょう。 これを行うには、以下のように選択します。
    
    1. [視覚エフェクト] タブで、円グラフのシンボルをクリックします。
       
        ![円グラフを作成する](./media/data-lake-store-power-bi/create-pie-chart.png "Create pie chart")
    2. 使用する列は **Column 4** (都市名) と **Column 7** (国名) です。 以下のように、これらの列を **[フィールド]** タブから **[視覚エフェクト]** タブにドラッグします。
       
        ![グラフを作成します](./media/data-lake-store-power-bi/create-visualizations.png "Create visualizations")
    3. これで、円グラフの外観は以下のようになります。
       
        ![円グラフ](./media/data-lake-store-power-bi/pie-chart.png "Create visualizations")
16. これで、ページ レベル フィルターから特定の国を選択し、選択した国の各都市のドライバー数を確認できます。 たとえば、**[視覚エフェクト]** タブの **[ページ レベル フィルター]** で、**[ブラジル]** を選択します。
    
    ![国を選択する](./media/data-lake-store-power-bi/select-country.png "Select a country")
17. 円グラフが自動的に更新され、ブラジルの各都市のドライバーが表示されます。
    
    ![国のドライバー](./media/data-lake-store-power-bi/driver-per-country.png "Drivers per country")
18. **[ファイル]** メニューの **[保存]** をクリックして、Power BI Desktop ファイルとして視覚エフェクトを保存します。

## <a name="publish-report-to-power-bi-service"></a>レポートを Power BI サービスに発行する
Power BI Desktop で視覚エフェクトを作成したら、Power BI サービスに発行して他のユーザーと共有することができます。 その手順については、「 [Power BI Desktop からの発行](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/)」参照してください。

## <a name="see-also"></a>関連項目
* [チュートリアル: Azure ポータルで Azure Data Lake Analytics の使用を開始する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)




<!--HONumber=Nov16_HO3-->


