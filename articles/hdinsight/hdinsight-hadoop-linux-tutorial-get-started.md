<properties
   	pageTitle="Linux のチュートリアル: Hadoop と Hive の使用 | Microsoft Azure"
   	description="HDInsight で Hadoop を使用するには、この Linux チュートリアルに従います。Linux のクラスターをプロビジョニングする方法、および Hive でデータを照会する方法について説明します。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="03/01/2016"
   	ms.author="jgao"/>

# Hadoop チュートリアル: HDInsight で Linux ベースの Hadoop を使用する

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

Linux ベースの Hadoop クラスターを HDInsight で作成し、Ambari Hive View を利用して Hive ジョブを実行する方法について説明します。

Hadoop とビッグ データを初めて扱う場合は、[Apache Hadoop](http://go.microsoft.com/fwlink/?LinkId=510084)、[MapReduce](http://go.microsoft.com/fwlink/?LinkId=510086)、[Hadoop Distributed File System (HDFS)](http://go.microsoft.com/fwlink/?LinkId=510087)、[Hive](http://go.microsoft.com/fwlink/?LinkId=510085) に関するトピックを参照してください。HDInsight によって Azure でどのように Hadoop を利用できるかについては、「[Introduction to Hadoop in HDInsight (HDInsight の Hadoop 入門)](hdinsight-hadoop-introduction.md)」を参照してください。

### 前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**: [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページをご覧ください。

## クラスターの作成

Hadoop ジョブのほとんどはバッチ ジョブです。クラスターを作成し、いくつかのジョブを実行します。このセクションでは、[Azure ARM テンプレート](../resource-group-template-deploy.md)を利用し、HDInsight で Linux ベースの Hadoop クラスターを作成します。このチュートリアルを利用するにあたり、Azure ARM テンプレートの使用経験は必要ありません。その他のクラスター作成方法と設定の詳細については、「[HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。ARM テンプレートを利用して HDInsight で Hadoop クラスターを作成する方法の詳細については、「[ARM テンプレートを使用した HDInsight での Windows ベースの Hadoop クラスターの作成](hdinsight-hadoop-create-windows-clusters-arm-templates.md)」を参照してください。

1. 次の画像をクリックして Azure ポータルで ARM テンプレートを開きます。ARM テンプレートは、**https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json* という URL のパブリック BLOB コンテナーにあります。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/ja-JP/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. **[パラメーター]** ブレードで、次の各項目を入力します。

    - **ClusterName**: 作成する Hadoop クラスターの名前を入力します。
    - **ClusterStorageAccountName**: 各クラスターには Azure BLOB ストレージ アカウントとの依存関係があります。クラスターを削除すると、データはストレージ アカウントに保持されます。
    - **クラスターのログイン名とパスワード**: 既定のログイン名は **admin** です。
    - **SSH ユーザー名とパスワード**: 既定のユーザー名は **sshuser** です。この名前は変更できます。 
    
    その他のパラメーターは省略可能です。そのまま残すことができます。クラスターの既定のストレージ アカウント名は、クラスター名に "store" が追加されたものです。これは、テンプレート内の variables セクションでハードコードされます。
3. **[OK]** をクリックしてパラメーターを保存します。
4. **[カスタム デプロイ]** ブレードで **[リソース グループ]** ボックスをクリックし、**[新規]** をクリックして新しいリソース グループを作成します。リソース グループとは、クラスター、依存するストレージ アカウント、その他のリンクされたリソースをグループ化しているコンテナーです。リソース グループの場所は、クラスターの場所とは異なる場合があります。
5. **[法律条項]** をクリックし、**[作成]** をクリックします。
6. **[作成]** をクリックします。"**Submitting deployment for Template deployment**" という新しいタイルが表示されます。クラスターの作成には約 20 分かかります。クラスターが作成されたら、ポータルのクラスター ブレードをクリックして開きます。

チュートリアルを完了したら、必要に応じてクラスターを削除できます。HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。また、HDInsight クラスターは、使用していない場合でも課金されます。クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。クラスターの削除手順については、「[Azure ポータルを使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-management-portal.md#delete-clusters)」を参照してください。


##Hive クエリの実行

[Ambari](hdinsight-hadoop-manage-ambari.md) のビューでは、1 つの Web ページから複数のユーティリティを提供しています。ユーティリティの 1 つは Hive ビューです。このセクションでは、Hive ビューを使用し、HDInsight クラスターに対して Hive クエリを実行します。Hive クエリを実行する他の方法については、「[HDInsight で Hadoop と共に Hive と HiveQL を使用して Apache log4j サンプル ファイルを分析する](hdinsight-use-hive.md)」を参照してください。

1. **https://&lt;ClusterName>.azurehdinsight.net** に移動します。&lt;ClusterName> は、Ambari を開くために前のセクションで作成したクラスターです。
2. Hadoop ユーザー名と前のセクションで指定したパスワードを入力します。既定のユーザー名は **admin** です。
3. 次のスクリーンショットのように **[Hive View]** (Hive ビュー) を開きます。

    ![Selecting ambari views](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png)
4. ページの __[Query Editor]__ セクションで、次の HiveQL ステートメントをワークシートに貼り付けます。

		SHOW tables;
5. __[実行]__ をクリックします。クエリ エディターの下に __[Query Process Results]__ セクションが表示され、ジョブに関する情報が表示されます。 

    クエリが完了すると、__[Query Process Results]__ セクションに操作の結果が表示されます。**hivesampletable** という名前のテーブルが 1 つ表示されます。このサンプルの Hive テーブルにはすべての HDInsight クラスターが付属します。
    
    > [AZURE.TIP] __[Query Process Results]__ セクションの左上にある __[Save results]__ ボックスに注意してください。これを使用すると、結果をダウンロードすることも、CSV ファイルとして HDInsight のストレージに保存することもできます。
6. 手順 4. と手順 5 を繰り返し、次のクエリを実行します。

        SELECT * FROM hivesampletable;

Hive ジョブを完了したら、[結果を Azure SQL データベースまたは SQL Server データベースにエクスポート](hdinsight-use-sqoop-mac-linux.md)できます。[Excel を利用して結果を視覚化](hdinsight-connect-excel-power-query.md)することもできます。HDInsight で Hive を使用する方法の詳細については、「[HDInsight で Hadoop と共に Hive と HiveQL を使用して Apache log4j サンプル ファイルを分析する](hdinsight-use-hive.md)」を参照してください。

## 次のステップ

このチュートリアルでは、ARM テンプレートを利用し、Linux ベースの HDInsight クラスターを作成する方法と基本的な Hive クエリを実行する方法について説明しました。

HDInsight でデータを分析する方法の詳細については、次を参照してください。

- Visual Studio から Hive クエリを実行する方法など、HDInsight で Hive を使用する方法の詳細については、「[HDInsight での Hive の使用][hdinsight-use-hive]」を参照してください。

- データの変換に使用される言語 Pig の詳細については、「[HDInsight での Pig の使用][hdinsight-use-pig]」を参照してください。

- Hadoop 上のデータを処理するプログラムを作成する方法の 1 つである MapReduce の詳細については、「[HDInsight での MapReduce の使用][hdinsight-use-mapreduce]」を参照してください。

- HDInsight Tools for Visual Studio を使用して HDInsight 上のデータを分析する方法については、[HDInsight Hadoop Tools for Visual Studio の使用開始](hdinsight-hadoop-visual-studio-tools-get-started.md)に関するページを参照してください。

実際のデータを使用する準備が整っていて、HDInsight のデータの格納方法や HDInsight にデータを取り込む方法を確認する場合は、以下を参照してください。

- HDInsight で Azure Blob Storage を使用する方法の詳細については、[HDInsight での Azure Blob Storage の使用](hdinsight-hadoop-use-blob-storage.md)に関するページを参照してください。

- データを HDInsight にアップロードする方法については、「[データを HDInsight にアップロードする方法][hdinsight-upload-data]」を参照してください。

HDInsight クラスターの作成または管理の詳細については、以下を参照してください。

- Linux ベースの HDInsight クラスターを管理する方法については、「[Ambari を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)」を参照してください。

- HDInsight クラスターの作成時に選択できるオプションの詳細については、「[HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

- Linux と Hadoop を使い慣れていて、HDInsight 上の Hadoop に関する詳細情報を確認するには、「[Linux での HDInsight の使用](hdinsight-hadoop-linux-information.md)」を参照してください。次のような情報が掲載されています。

	* クラスターでホストされる URL (Ambari や WebHCat など)
	* Hadoop ファイルの場所とローカル ファイル システムの例
	* 既定のデータ ストアとして、HDFS ではなく Azure Storage (WASB) を使用する


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png

<!---HONumber=AcomDC_0302_2016-->