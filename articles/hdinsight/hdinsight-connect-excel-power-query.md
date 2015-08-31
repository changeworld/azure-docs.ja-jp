<properties
	pageTitle="Power Query を使用した Excel から Hadoop への接続 | Microsoft Azure"
	description="ビジネス インテリジェンス コンポーネントを活用し、HDInsight 上の Hadoop に格納されているデータに Power Query for Excel でアクセスする方法を説明します。"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="jgao"/>


#Power Query を使用した Excel から Hadoop への接続

マイクロソフトのビッグ データ ソリューションの重要な特徴の 1 つに、Microsoft ビジネス インテリジェンス (BI) コンポーネントと Azure HDInsight の Hadoop クラスターとの統合があります。この統合の主な例は、Microsoft Power Query for Excel アドインを使用して Hadoop クラスターと関連付けられたデータを格納する Azure ストレージ アカウントに Excel を接続する機能です。この記事では、Power Query をセットアップして、HDInsight で管理される Hadoop クラスターに関連付けられたデータの照会に使用する方法を説明します。

> [AZURE.NOTE]この記事の手順は、Linux と Windows ベースの HDInsight クラスターのどちらにも使用できますが、クライアント ワークステーションには Windows が必要です。

## 前提条件

この記事を読み始める前に、次の項目を用意する必要があります。

- **HDInsight クラスター**。その構成方法については、「[Azure HDInsight の概要][hdinsight-get-started]」を参照してください。
- Windows 7、Windows Server 2008 R2、またはそれ以降のオペレーティング システムが実行されている**ワークステーション**。
- **Office 2013 Professional Plus、Office 365 ProPlus、Excel 2013 Standalone、または Office 2010 Professional Plus**。


## <a id="InstallPowerQuery"></a>Microsoft Power Query for Excel のインストール

Power Query を使うと、各種ソースから Microsoft Excel にデータをインポートして、そこで PowerPivot や Power View のような BI ツールを利用することができます。特に、Power Query は、HDInsight クラスターで実行される Hadoop ジョブによって出力されたデータや生成されたデータをインポートすることができます。

[Microsoft ダウンロード センター][powerquery-download]から Microsoft Power Query for Excel をダウンロードして、インストールします。

## <a id="ImportData"></a>HDInsight データを Excel にインポート

Power Query for Excel アドインを使うと、HDInsight クラスターから Excel にデータを簡単にインポートして、そこで PowerPivot や Power Map のような BI ツールを使用してデータの調査、分析、表示ができます。

**HDInsight クラスターから Excel にデータをインポートするには**

1. Excel を開きます。

2. 新しい空のブックを作成します。

3. **[Power Query]** メニューをクリックし、**[その他のデータ ソース]**、**[Azure HDInsight から]** の順にクリックします。

	![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

	注: **[Power Query]** メニューが表示されない場合は、**[ファイル]**、**[オプション]**、**[アドイン]** の順にクリックして、ページ下部にある **[管理]** ボックスの一覧の **[COM アドイン]** を選択します。**[設定]** をクリックして、Power Query for Excel アドインのボックスがオンになっていることを確認します。

3. **[アカウント名]** にクラスターに関連付けられた Azure BLOB ストレージ アカウントの名前を入力し、**[OK]** をクリックします。

4. **アカウント キー**に BLOB ストレージ アカウントのキーを入力し、**[保存]** をクリックします。(この操作が必要となるのは、このストアに最初にアクセスするときだけです)。

5. クエリ エディターの左側にある **[ナビゲーター]** ウィンドウで、BLOB ストレージ コンテナーの名前をダブルクリックします。既定で、コンテナー名はクラスター名と同じです。

6. **[名前]** 列 (フォルダーのパスは **../hive/warehouse/hivesampletable/**) で **HiveSampleData.txt** を見つけて、HiveSampleData.txt の左側の **[バイナリ]** をクリックします。

	![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. 列名を変更することもできます。準備ができたら **[適用して閉じる]** をクリックします。

	![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a id="NextSteps"></a>次のステップ

この記事では、Power Query を使用して HDInsight から Excel にデータを取得する方法を学習しました。同様に、Azure SQL Database に HDInsight からデータを取得することもできます。また、HDInsight にデータをアップロードすることもできます。詳細については、次の記事を参照してください。

* [Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][hdinsight-ODBC]
* [HDInsight へのデータのアップロード][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689

<!---HONumber=August15_HO8-->