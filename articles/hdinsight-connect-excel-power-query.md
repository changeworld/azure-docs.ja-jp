<properties 
	pageTitle="Power Query を使用した Excel から Hadoop への接続 | Azure" 
	description="ビジネス インテリジェンス コンポーネントを活用し、Power Query を使用して Azure HDInsight に格納されているデータに Excel でアクセスする方法を説明します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="bradsev"/>




#Power Query を使用した Excel から Hadoop への接続

Microsoft のビッグ データ ソリューションの重要な特徴の 1 つに、  Microsoft Business Intelligence (BI) コンポーネントと HDInsight の Hadoop clusters との統合があります。この統合の主な例は、Microsoft Power Query for Excel を使用して Hadoop クラスターと関連付けられたデータを含む Azure ストレージ アカウントに Excel を接続する機能です。この記事では、Excel から Power Query を使用できるようにセットアップし、HDInsight で管理する Hadoop クラスターに関連付けられたデータを照会する方法を説明します。 

**前提条件**

この記事を読み始める前に、次の項目を用意する必要があります。

- HDInsight クラスター。その構成方法については、[Azure HDInsight の概要][hdinsight-get-started]に関するページを参照してください。
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

3. **[Power Query]** メニューをクリックし、**[その他のデータ ソース]**、**[Azure HDInsight から]** の順にクリックします。 

	![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

	注:**[Power Query]** メニューが表示されない場合は、**[ファイル]** > **[オプション]** > **[アドイン]** の順にクリックして、ページ下部にある **[マネージャー]** ボックスの一覧の **[COM アドイン]** を選択します。**[設定]** をクリックして、Microsoft Office Power Query for Excel アドインのボックスがオンになっていることを確認します。

3. クラスターに関連付けられた Azure BLOB ストレージ アカウントの名前を **[アカウント名]** ボックスに入力し、**[OK]** をクリックします。 

4. BLOB ストレージ アカウントの **アカウント キー** を入力し、**[保存]** をクリックします。(この操作が必要となるのは、このストアに最初にアクセスするときだけです)。	

5. **クエリ エディター** の左側にある **[ナビゲーター]** ウィンドウで、BLOB ストレージ コンテナーの名前をダブルクリックします。既定で、コンテナー名はクラスター名と同じです。 

6. **[名前]** 列 (フォルダーのパスは **../hive/warehouse/hivesampletable/**) で **HiveSampleData.txt** を見つけて、HiveSampleData.txt の左側の **[バイナリ]** をクリックします。

	![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. 列名を変更することもできます。準備ができたら **[適用して閉じる]** をクリックします。	

	![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a id="NextSteps"></a>次のステップ

この記事では、Power Query を使用して HDInsight から Excel にデータを取得する方法を学習しました。同様に、SQL Azure に HDInsight からデータを取得することもできます。また、HDInsight にデータをアップロードすることもできます。詳細については、次の記事を参照してください。

* [Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][hdinsight-ODBC]に関するページ
* [HDInsight へのデータのアップロード][hdinsight-upload-data]。

[hdinsight-ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-upload-data]: ../hdinsight-upload-data/

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png 
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG 

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689 

<!--HONumber=42-->
