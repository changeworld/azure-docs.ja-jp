<properties linkid="manage-services-hdinsight-connect-excel-with-power-query" urlDisplayName="HDInsight と Excel" pageTitle="Power Query を使用した Excel から HDInsight への接続 | Azure" metaKeywords="hdinsight, excel, データ エクスプローラー, hive excel, hdinsight excel, power query" description="ビジネス インテリジェンス コンポーネントを活用し、Excel を使用して、Power Query を使用して Azure HDInsight に格納されているデータにアクセスする方法を学習します。" metaCanonical="" services="hdinsight" documentationCenter="" title="Power Query を使用した Excel から Azure HDInsight への接続" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />




#Power Query を使用した Excel から HDInsight への接続

Microsoft のビッグ データ ソリューションの重要な特徴の 1 つに、Microsoft Business Intelligence (BI) コンポーネントと HDInsight Hadoop クラスターとの統合があります。この統合の主な例は、Microsoft Power Query for Excel を使用して HDInsight クラスターと関連付けられたデータを含む Azure ストレージ アカウントに Excel を接続する機能です。この記事では、Excel から Power Query を使用できるようにセットアップし、HDInsight クラスターに関連付けられたデータを照会する方法を説明します。

**前提条件**

この記事を読み始める前に、次の項目を用意する必要があります。

- HDInsight クラスターが必要です。その構成方法については、「[Azure HDInsight の概要][hdinsight-get-started]」を参照してください。
- Windows 8、Windows 7、Windows Server 2012、Windows Server 2008 R2 のいずれかを実行しているコンピューターが必要です。
- Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone、Office 2010 Professional Plus のいずれかが必要です。

## この記事の内容

- [Microsoft Power Query for Excel のインストール](#InstallPowerQuery)
- [データを Excel へインポート](#ImportData)
- [次のステップ](#NextSteps)


## <a id="InstallPowerQuery"></a>Microsoft Power Query for Excel のインストール

Power Query を使うと、各種ソースから Microsoft Excel にデータをインポートして、そこで PowerPivot や Power View のようなビジネス インテリジェンス (BI) ツールを利用することができます。特に、Power Query は、HDInsight クラスターで実行される Hadoop ジョブによって出力されたデータや生成されたデータをインポートすることができます。

[Microsoft ダウンロード センター][powerquery-download]から Microsoft Power Query for Excel をダウンロードして、インストールします。

## <a id="ImportData"></a>HDInsight データを Excel へインポート

Power Query for Excel アドインを使うと、HDInsight クラスターから Excel にデータを簡単にインポートして、そこで PowerPivot や Power Map のようなビジネス インテリジェンス ツールを使用してデータの調査、分析、表示ができます。

**HDInsight クラスターから Excel にデータをインポートするには**

1. Excel を開きます。

2. 新しい空のブックを作成します。

3. **[Power Query]** メニューをクリックし、**[その他のデータ ソース]**、**[Azure の HDInsight]** の順にクリックします。

	![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

	注: **[Power Query]** メニューが表示されない場合は、**[ファイル]**、**[オプション]**、**[アドイン]** の順にクリックして、ページ下部にある **[マネージャー]** ボックスの一覧の **[COM アドイン]** をクリックします。**[設定]** をクリックして、Microsoft Office Power Query for Excel アドインのボックスがオンになっていることを確認します。

3. クラスターに関連付けられた Azure BLOB ストレージ アカウントの名前を **[アカウント名]** ボックスに入力し、**[OK]** をクリックします。

4. BLOB ストレージ アカウントのアカウント キーを **[アカウント キー]** ボックスに入力し、**[保存]** をクリックします。(この操作が必要となるのは、このストアに最初にアクセスするときだけです)。	

5. **クエリ エディター**の左側にある **[ナビゲーター]** ウィンドウで、BLOB ストレージ コンテナーの名前をダブルクリックします。既定で、コンテナー名はクラスター名と同じです。

6. **[名前]** 列 (フォルダーのパスが **../hive/warehouse/hivesampletable/**) で **HiveSampleData.txt** を見つけて、HiveSampleData.txt の左側の **[バイナリ]** をクリックします。

	![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. 列名を変更することもできます。準備ができたら **[適用して閉じる]** をクリックします。	

	![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a id="NextSteps"></a>次のステップ

この記事では、Power Query を使用して HDInsight から Excel にデータを取得する方法を学習しました。同様に、SQL Azure に HDInsight からデータを取得することもできます。また、HDInsight にデータをアップロードすることもできます。詳細については、次の記事を参照してください。

* [Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][hdinsight-excel-odbc]
* [データを HDInsight へアップロードする方法][hdinsight-upload-data]。

[hdinsight-excel-odbc]: /ja-jp/documentation/articles/hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-get-started]: /ja-jp/documentation/articles/hdinsight-get-started/
[hdinsight-upload-data]: /ja-jp/documentation/articles/hdinsight-upload-data/

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png 
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG 

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689 

