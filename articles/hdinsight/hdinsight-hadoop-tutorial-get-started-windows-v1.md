<properties
   pageTitle="Hadoop のチュートリアル: Windows での Hadoop の使用 | Microsoft Azure"
   description="HDInsight での Hadoop の使用Windows での Hadoop クラスターのプロビジョニング、データに対する Hive クエリの実行、Excel での出力の分析を行う方法について説明します。"
   keywords="hadoop チュートリアル,windows 上の hadoop,hadoop クラスター,hadoop について, hive クエリ"
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/07/2016"
   ms.author="nitinme"/>


# Hadoop のチュートリアル: Windows 上の HDInsight で Hadoop と Hive クエリを使用する

> [AZURE.SELECTOR]
- [Windows](../hdinsight-hadoop-tutorial-get-started-windows-v1.md)
- [Linux](../hdinsight-hadoop-linux-tutorial-get-started.md)

Hadoop on Windows を理解して HDInsight の使用を開始できるように、このチュートリアルでは、Hadoop クラスターの非構造化データで Hive クエリを実行し、その結果を Microsoft Excel で分析する方法について説明します。

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [HDInsight での Hadoop の使用 (Windows)](hdinsight-hadoop-tutorial-get-started-windows.md)

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## この Hadoop チュートリアルでは何が達成されますか?

大規模な非構造化データ セットがあり、このデータ セットに対して Hive クエリを実行して、意味のある情報を抽出する場合を仮定します。これが、このチュートリアルで目的としていることです。これを実現するには、次の手順を実行します。

   ![Hadoop チュートリアル: アカウントの作成、Hadoop クラスターのプロビジョニング、Hive クエリーの送信、Excel によるデータ分析  
][image-hdi-getstarted-flow]

HDInsight での Hadoop の説明については、このチュートリアルのデモ ビデオをご覧ください。

![初めての Hadoop チュートリアル動画: Hadoop クラスターでの Hive クエリーの送信、Excel によるデータ分析][img-hdi-getstarted-video]

**[YouTube で HDInsight の Hadoop チュートリアルを見る](https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS)**


Azure の HDInsight を一般に利用可能にすると共に、Microsoft は HDInsight Emulator for Azure (旧称 *Microsoft HDInsight 開発者プレビュー*) もリリースしました。このエミュレーターは開発者シナリオを対象としており、単一ノード デプロイのみをサポートします。HDInsight Emulator の使用法については、「[HDInsight Emulator の概要][hdinsight-emulator]」に関するページをご覧ください。

> [AZURE.NOTE] HBase クラスターのプロビジョニング方法については、「[Provision HBase cluster in HDInsight (HDInsight での HBase クラスターのプロビジョニング)][hdinsight-hbase-custom-provision]」を参照してください。2 つのデータベースのどちらかを選ぶ理由については、「<a href="http://go.microsoft.com/fwlink/?LinkId=510237">What Is The Difference Between HBase and Hadoop/HDFS? (HBase と Hadoop/HDFS の違い)</a>」をご覧ください。

## 前提条件

このチュートリアルを開始する前に、以下の条件を満たしている必要があります。


- **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
- Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone、または Office 2010 Professional Plus がインストールされた**ワークステーション**。

**このチュートリアルの推定所要時間:** 30 分



##<a name="storage"></a>Azure ストレージ アカウントの作成

HDInsight で Hadoop クラスターをプロビジョニングするときに、Azure ストレージ アカウントを指定します。Hadoop 分散ファイルシステム (HDFS) と同様、このアカウントの特定の Blob Storage コンテナーが、既定のファイル システムとして設定されます。既定では、HDInsight クラスターは、指定されたストレージ アカウントと同じデータ センターにプロビジョニングされます。詳細については、「[HDInsight での Azure Blob Storage の使用][hdinsight-storage]」をご覧ください。

>[AZURE.NOTE] 既定の Blob Storage コンテナーと複数の Hadoop クラスターを共有しないでください。

このストレージ アカウントに加えて、クラスターの構成をカスタマイズするときに他のストレージ アカウントを追加できます。この追加のストレージ アカウント用には、同じ Azure サブスクリプションを使用することも異なる Azure サブスクリプションを使用することもできます。手順については、「[Provision HDInsight clusters using custom options (カスタム オプションを使用した HDInsight クラスターのプロビジョニング)][hdinsight-provision]」を参照してください。

このチュートリアルでは、既定の BLOB と既定のストレージ アカウントが使用されています。

**Azure ストレージ アカウントを作成するには**

1. [Azure クラシック ポータル][azure-management-portal]にサインインします。
2. 左下隅にある **[新規]** をクリックし、次の図に示すように、値を入力します。

	![簡易作成を使用して新しいストレージ アカウントを設定できる Azure クラシック ポータル。][image-hdi-storageaccount-quickcreate]

>[AZURE.NOTE]  ストレージ アカウントは、クラスターでサポートされる場所 (**東アジア**、**東南アジア**、**北ヨーロッパ**、**西ヨーロッパ**、**米国東部**、**米国西部**、**米国中北部**、**米国中南部**) に作成してください。

一覧の新しいストレージ アカウントを選択し、ページの下部の **[アクセス キーの管理]** をクリックします。**[プライマリ アクセス キー]** (または **[セカンダリ アクセス キー]** 動作しているいずれかのキー) の値を書き留めます。この情報は後で必要になります。詳細については、「[ストレージ アカウントの作成方法][azure-create-storageaccount]」をご覧ください。

##<a name="provision"></a>Hadoop クラスターのプロビジョニング

クラスターをプロビジョニングすると、Hadoop と関連アプリケーションを含む Azure コンピューティング リソースがプロビジョニングされます。このセクションでは、Hadoop バージョン 2.4 に基づいて HDInsight バージョン 3.1 クラスターをプロビジョニングします。また、Azure クラシック ポータル、HDInsight PowerShell コマンドレット、HDInsight .NET SDK を使用して、他のバージョン用の Hadoop クラスターを作成することもできます。手順については、「[Provision HDInsight clusters using custom options (カスタム オプションを使用した HDInsight クラスターのプロビジョニング)][hdinsight-provision]」を参照してください。HDInsight バージョンとその SLA については、「[HDInsight コンポーネントのバージョン](hdinsight-component-versioning.md)」をご覧ください。

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]


**Hadoop クラスターをプロビジョニングするには**

1. [Azure クラシック ポータル][azure-management-portal]にサインインします。

2. 左下隅にある **[新規]** をクリックし、次の図に示すように、値を入力します。

	![HDInsight での Hadoop クラスターの作成。][image-hdi-quickcreatecluster]

<!-- COMMENTED OUT TEXT BEGINS --

4. Enter or select the following values:


	<table border="1">
	<tr><th>Name</th><th>Value</th></tr>
	<tr><td>Cluster Name</td><td>Name of the cluster.</td></tr>
	<tr><td>Cluster Size</td><td>Number of data nodes you want to deploy. The default value is 4. But the option to use 1 or 2 data nodes is also available from the drop-down list. Any number of cluster nodes can be specified by using the <strong>Custom Create</strong> option. Pricing details about the billing rates for various cluster sizes are available. Click the <strong>?</strong> symbol above the drop-down list and follow the link that appears.</td></tr>
	<tr><td>Password</td><td>The password for the <i>admin</i> account. The cluster user name "admin" is specified when you are not using the <strong>Custom Create</strong> option. Note that this is NOT the Windows Administrator account for the VMs on which the clusters are provisioned. The account name can be changed by using the <strong>Custom Create</strong> wizard.</td></tr>
	<tr><td>Storage Account</td><td>Click the drop-down list, and select the storage account that you created. <br/>

	When a storage account is chosen, it cannot be changed. If the storage account is removed, the cluster will no longer be available for use.

	The HDInsight cluster is located in the same datacenter as the storage account.
	</td></tr>
	</table>

	Keep a copy of the cluster name. You will need it later in the tutorial.


5. Click **Create HDInsight Cluster**. When the provisioning completes, the  status column shows **Running**.

-- COMMENTED OUT TEXT ENDS -->

>[AZURE.NOTE] この手順では、HDInsight クラスターをバージョン 3.1 でプロビジョニングします。他のバージョンのクラスターを作成するには、ポータルで **[カスタム作成]** オプションを使用するか、Azure PowerShell を使用します。各バージョンの相違については、「[What's new in the Hadoop cluster versions provided by HDInsight? (HDInsight で提供される Hadoop クラスター バージョンの新機能)][hdinsight-versions]」をご覧ください。**[カスタム作成]** オプションの使用法については、「[Provision HDInsight clusters using custom options (カスタム オプションを使用した HDInsight クラスターのプロビジョニング)][hdinsight-provision]」ご覧ください。


##<a name="sample"></a>ポータルからサンプル データを実行する

正常にプロビジョニングされた HDInsight クラスターは、ポータルからサンプルを直接実行する概要ギャラリーを含むクエリ コンソールを提供します。サンプルを使っていくつかの基本的なシナリオを検証することで、HDInsight の操作方法を学ぶことができます。これらのサンプルには、分析対象のデータ、データに対して実行するクエリなど、必要なものがすべて用意されています。概要ギャラリーのサンプルの詳細については、「[Learn Hadoop in HDInsight using the HDInsight Getting Started Gallery (HDInsight 概要ギャラリーを使用した HDInsight での Hadoop の説明)](hdinsight-learn-hadoop-use-sample-gallery.md)」をご覧ください。

**サンプルを実行するには**、Azure クラシック ポータルで、サンプルを実行するクラスターの名前をクリックし、ページの下部にある **[クエリ コンソール]** をクリックします。開いた Web ページの **[概要ギャラリー]** タブをクリックし、**[サンプル]** カテゴリで、実行するサンプルをクリックします。Web ページの指示に従ってサンプルを実行します。次の表では、いくつかのサンプルを一覧し、各サンプルについて詳しく説明します。

サンプル | 内容
------ | ---------------
[センサー データの分析][hdinsight-sensor-data-sample] | HDInsight を使用して、暖房、換気、空調 (HVAC) システムによって生成された履歴データを処理し、設定した温度を正確に維持できないシステムを識別する方法について説明します。
[Web サイト ログの分析][hdinsight-weblogs-sample] | HDInsight を使用して、Web サイトのログ ファイルを分析する方法について説明します。このサンプルでは、1 日の間に発生した外部 Web サイトからの Web サイトへのアクセス数を調べ、ユーザーが経験した Web サイト エラーの概要を取得します。
[Twitter の傾向分析](hdinsight-analyze-twitter-data.md) | HDInsight を使用して、Twitter の傾向を分析する方法について説明します。



##<a name="hivequery"></a>ポータルから Hive クエリを実行する
HDInsight クラスターがプロビジョニングされたら、サンプルの Hive テーブルを照会する Hive ジョブを実行します。HDInsight クラスターに付属する *hivesampletable* を使用します。このテーブルには、モバイル デバイスの製造元、プラットフォーム、モデルに関するデータが格納されています。このテーブルの Hive クエリは、特定の製造元のモバイル デバイスのデータを取得します。

> [AZURE.NOTE] HDInsight Tools for Visual Studio は、Azure SDK for .NET バージョン 2.5 以降に付属しています。Visual Studio からこのツールを使用すると、HDInsight クラスターに接続し、Hive テーブルを作成し、Hive クエリを実行できます。詳細については、「[HDInsight Hadoop Tools for Visual Studio の使用開始][1]」をご覧ください。

**クラスター ダッシュボードから Hive ジョブを実行するには**

1. [Azure クラシック ポータル][azure-management-portal]にサインインします。
2. 左側ウィンドウの **[HDINSIGHT]** をクリックします。前のセクションで作成したクラスターなど、作成したクラスターが一覧表示されます。
3. Hive ジョブの実行に使用するクラスターの名前をクリックし、ページの下部にある **[クエリ コンソール]** をクリックします。
4. 別のブラウザー タブで Web ページが開きます。Hadoop ユーザー アカウントとパスワードを入力します。既定のユーザー名は **admin** で、パスワードはプロビジョニング処理中に入力したパスワードです。ダッシュボードは、次のようになります。

	![Hive Editor tab in the HDInsight cluster dashboard.][img-hdi-dashboard]

	ページの上部にいくつかのタブがあります。既定のタブは **[Hive エディター]** です。これ以外に、**[ジョブ履歴]** タブと **[ファイル ブラウザー]** タブがあります。ダッシュボードでは、Hive クエリの送信、Hadoop ジョブ ログの確認、ストレージ内のファイルの参照などの操作を実行できます。

	> [AZURE.NOTE] Web ページの URL は *&lt;ClusterName&gt;.azurehdinsight.net* です。ポータルからダッシュボードを開く代わりに、URL を使用して Web ブラウザーからダッシュボードを開くこともできます。

6. **[Hive エディター]** タブで、**[クエリ名]** に「**HTC20**」と入力します。クエリ名は、ジョブのタイトルです。クエリ ウィンドウで、次の図に示すように、Hive クエリを入力します。

	![[Hive エディター] に入力された Hive クエリ][img-hdi-dashboard-query-select]

4. **[Submit]** をクリックします。結果が返されるまでしばらく時間がかかります。画面は 30 秒ごとに更新されます。**[最新の情報に更新]** をクリックして画面を更新することもできます。

    ![クラスターのダッシュボードの下部に表示される Hive クエリの結果。][img-hdi-dashboard-query-select-result]

5. 状態にジョブの完了が表示されたら、画面のクエリ名をクリックして出力を表示します。**[ジョブの開始時刻 (UTC)]** の値をメモしておきます。この情報は後で必要になります。

    ![HDInsight クラスターのダッシュボードの [ジョブ履歴] タブに表示されるジョブ開始時間。][img-hdi-dashboard-query-select-result-output]

    このページには、**ジョブの出力**と**ジョブのログ**も表示されます。出力ファイル (\_stdout) とログ ファイル (\_stderr) をダウンロードすることもできます。


**出力ファイルを参照するには**

1. クラスターのダッシュボードで、**[ファイル ブラウザー]** をクリックします。
2. ストレージ アカウント名、コンテナー名 (クラスター名と同じ名前)、**[ユーザー]** の順にクリックします。
3. **[管理者]** をクリックし、以前書き留めたジョブの開始時刻よりも少し後の最終変更時刻の GUID をクリックします。この GUID をコピーします。これは、次のセクションで必要になります。


   	![[ファイル ブラウザー] タブに表示された Hive クエリ出力ファイルの GUID][img-hdi-dashboard-query-browse-output]


##<a name="powerquery"></a>Excel 用 Microsoft Business Intelligence ツールに接続する

Microsoft Excel 用 Power Query アドインを使用すると、HDInsight からのジョブ出力を Excel にインポートして、Microsoft Business Intelligence ツールで結果をさらに分析することができます。

チュートリアルのこの部分を完了するには、Excel 2013 または 2010 がインストールされている必要があります。

**Microsoft Power Query for Excel をダウンロードするには**

- [Microsoft ダウンロード センター](http://www.microsoft.com/download/details.aspx?id=39379)から Microsoft Power Query for Microsoft Excel をダウンロードして、インストールします。

**HDInsight データをインポートするには**

1. Excel を開き、新しいブックを作成します。
3. **[Power Query]** メニューをクリックし、**[その他のデータ ソース]**、**[Azure HDInsight から]** の順にクリックします。

	![Excel PowerQuery Import menu open for Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. クラスターに関連付けられた Azure BLOB ストレージ アカウントの名前を **[アカウント名]** ボックスに入力し、**[OK]** をクリックします。(これは、このチュートリアルで作成済みのストレージ アカウントです。)
4. Azure Blob Storage アカウントのアカウント キーを **[アカウント キー]** ボックスに入力し、**[保存]** をクリックします。
5. 右側のウィンドウで、BLOB 名をダブルクリックします。既定で、BLOB名はクラスター名と同じです。

6. **[名前]** 列で **stdout** を見つけます。対応する **[フォルダーのパス]** 列の GUID が前にコピーした GUID と一致していることを確認します。一致している場合、出力データは送信したジョブに対応しています。**[stdout]** の左側の列の **[バイナリ]** をクリックします。

	![コンテンツの一覧の GUID でデータ出力を検索中。][image-hdi-gettingstarted-powerquery-importdata2]

9. 左上隅にある **[閉じて読み込む]** をクリックして、Hive ジョブ出力を Excel にインポートします。

##クラスターを削除する

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="nextsteps"></a>次のステップ
この Hadoop のチュートリアルでは、HDInsight で Windows ベースの Hadoop クラスターをプロビジョニングした後、そのデータ上で Hive クエリを実行し、結果を Excel にインポートする方法を説明しました。このデータは、ビジネス インテリジェンス ツールを使用してさらに処理し、グラフィカルに表示することができます。詳細については、次のチュートリアルをご覧ください。

- [HDInsight Hadoop Tools for Visual Studio の使用開始][1]
- [HDInsight Emulator の概要][hdinsight-emulator]
- [HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]
- [PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]
- [HDInsight へのデータのアップロード][hdinsight-upload-data]
- [HDInsight での MapReduce の使用][hdinsight-use-mapreduce]
- [HDInsight での Hive の使用][hdinsight-use-hive]
- [HDInsight での Pig の使用][hdinsight-use-pig]
- [HDInsight での Oozie の使用][hdinsight-use-oozie]
- [HDInsight 用 Java MapReduce プログラムの開発][hdinsight-develop-mapreduce]


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-versions]: hdinsight-component-versioning.md


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-emulator]: hdinsight-hadoop-emulator-get-started.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage/storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: hdinsight-hbase-tutorial-get-started.md


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#setup-1-install


[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/hdi-get-started-video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.GettingStarted.PowerQuery.ImportData2.png
 

<!---HONumber=AcomDC_0511_2016-->