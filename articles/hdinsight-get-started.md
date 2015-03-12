<properties 
	pageTitle="HDInsight で Hive と Hadoop を使用する | Azure" 
	description="HDInsight での Hadoop の使用の概要を示します。これは、クラウドのビッグ データ ソリューションです。クラスターのプロビジョニング、Hive でのデータの照会、分析のための Excel への出力の方法を示します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/14/2014" 
	ms.author="nitinme"/>


# HDInsight で Hive と Hadoop を使用し、モバイル ハンドセットの使用状況を分析する

<!--div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Get started using Hadoop 2.4 in HDInsight" class="current">Hadoop 2.4</a>
<a href="../hdinsight-get-started-30" title="Get started using Hadoop 2.2 in HDInsight">Hadoop 2.2</a>
<!--a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a>
</div-->

HDInsight をすぐに使い始めることができるように、このチュートリアルでは、Hive クエリを実行して、Hadoop クラスターの非構造化データから意味のある情報を取得する方法について説明します。次に、Microsoft Excel で結果を分析します。


> [AZURE.NOTE] Hadoop とビッグ データを初めて扱う方は、[Apache Hadoop][apache-hadoop]、[MapReduce][apache-mapreduce]、[HDFS][apache-hdfs]、[Hive][apache-hive] に関するトピックを参照してください。HDInsight によって Azure でどのように Hadoop を利用できるかについては、[HDInsight の Hadoop 入門][hadoop-hdinsight-intro]に関するページを参照してください。

Azure の HDInsight を一般に利用可能にすると共に、Microsoft は HDInsight Emulator for Azure (旧称  *Microsoft HDInsight Developer Preview*) もリリースしました。このエミュレーターは開発者シナリオを対象としており、単一ノード デプロイメントのみをサポートします。HDInsight Emulator の使用法については、「[HDInsight Emulator の概要][hdinsight-emulator]」を参照してください。

> [AZURE.NOTE] HBase クラスターのプロビジョニング方法については、[HDInsight での HBase クラスターのプロビジョニング][hdinsight-hbase-custom-provision]に関するページを参照してください。HBase と Hadoop/HDFS のどちらを選択するか検討する際には、 <a href="http://go.microsoft.com/fwlink/?LinkId=510237">「What Is The Difference Between HBase and Hadoop/HDFS? (HBase と Hadoop/HDFS の違い)」</a> を参照してください。   

## このチュートリアルで目的とする操作 ##

構造化されていないデータ セットが大量にあり、このデータ セットに対してクエリを実行して、意味のある情報を抽出したいとします。これが、このチュートリアルで目的としていることです。これを実現するには、次の手順を実行します。

   !["Get started using Hadoop with Hive in HDInsight" tutorial steps illustrated: create an account; provision a cluster; query data; and analyze in Excel.][image-hdi-getstarted-flow]

このチュートリアルのデモ ビデオも用意されています。

<center><iframe width="560" height="315" src="http://www.youtube.com/embed/v=Y4aNjnoeaHA?list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" frameborder="0" allowfullscreen></iframe></center>

<!--center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target="_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center-->



**前提条件:**

このチュートリアルを読み始める前に、次の項目を用意する必要があります。


- Azure サブスクリプション。サブスクリプションの入手方法の詳細については、[購入オプション][azure-purchase-options]、[メンバー プラン][azure-member-offers]、または[無料評価版][azure-free-trial]に関するページを参照してください。
- Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone、または Office 2010 Professional Plus がインストールされたコンピューター。

**推定所要時間:** 30 分

##このチュートリアルの内容

* [Azure のストレージ アカウントの作成](#storage)
* [HDInsight クラスターをプロビジョニングする](#provision)
* [ポータルからサンプルを実行する](#sample)
* [Hive ジョブを実行する](#hivequery)
* [次のステップ](#nextsteps)

##<a name="storage"></a>Azure のストレージ アカウントの作成

HDInsight では、データを格納するために Azure BLOB ストレージを使用します。これは、 *WASB* または  *Azure Storage - Blob* と呼ばれます。WASB は、HDFS を Azure BLOB ストレージ上で Microsoft が実装したものです。詳細については、[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]に関するページを参照してください。

HDInsight クラスターをプロビジョニングするときは、Azure Storage アカウントを指定します。HDFS と同様、このアカウントの特定の BLOB ストレージ コンテナーが、既定のファイル システムとして設定されます。既定では、HDInsight クラスターは、指定されたストレージ アカウントと同じデータ センターにプロビジョニングされます。

このストレージ アカウントに加えて、HDInsight クラスターの構成をカスタマイズするときに他のストレージ アカウントを追加できます。この追加のストレージ アカウント用には、同じ Azure サブスクリプションを使用することも異なる Azure サブスクリプションを使用することもできます。手順については、[カスタム オプションを使用した HDInsight クラスターのプロビジョニング][hdinsight-provision]に関するページを参照してください。 

このチュートリアルでは、わかりやすくするために、既定の BLOB コンテナーと既定のストレージ アカウントが使用されています。実際には、データ ファイルを専用のストレージ アカウントに格納するのが一般的です。

**Azure Storage アカウントを作成するには**

1. [Azure の管理ポータル][azure-management-portal]にサインインします。
2. 左下隅にある **[新規]** をクリックし、**[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

	![Azure portal where you can use Quick Create to set up a new storage account.][image-hdi-storageaccount-quickcreate]

3. **[URL]**、**[場所]**、および **[レプリケーション]** に値を入力し、**[ストレージ アカウントの作成]** をクリックします。アフィニティ グループはサポートされていません。新しいストレージ アカウントがストレージ一覧に表示されます。

	>[AZURE.NOTE]  このチュートリアルで使用したような、HDInsight クラスターをプロビジョニングする簡易作成オプションでは、クラスターをプロビジョニングする際に場所をたずねるメッセージが表示されません。クラスターは、既定でストレージ アカウントと同じデータ センターに配置されます。そのため、ストレージ アカウントは、クラスターでサポートされる場所**東アジア**、**東南アジア**、**北ヨーロッパ**、**西ヨーロッパ**、**米国東部**、**米国西部**、**米国中北部**、および**米国中南部**です。

4. 新しいストレージ アカウントの **[状態]** 列が **[オンライン]** になるまで待ちます。
5. 一覧の新しいストレージ アカウントを選択し、ページの下部の **[アクセス キーの管理]** をクリックします。
7. **[ストレージ アカウント名]** と **[プライマリ アクセス キー]** (または **[セカンダリ アクセス キー]**) の値をメモします。どちらのキーでもかまいません。この情報は後で必要になります。


詳細については、「
[ストレージ アカウントの作成方法][azure-create-storageaccount]に関するページおよび[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]に関するページを参照してください。
	
##<a name="provision"></a>HDInsight クラスターをプロビジョニングする

HDInsight クラスターをプロビジョニングすると、Hadoop と関連アプリケーションを含む Azure コンピューティング リソースがプロビジョニングされます。このセクションでは、Hadoop Version 2.4 に基づく HDInsight クラスター Version 3.1 をプロビジョニングします。また、Azure ポータル、HDInsight PowerShell コマンドレット、または HDInsight .NET SDK を使用して、他のバージョン用の Hadoop クラスターを作成することもできます。手順については、[カスタム オプションを使用した HDInsight クラスターのプロビジョニング][hdinsight-provision]に関するページを参照してください。異なる HDInsight バージョンとその SLA については、[HDInsight コンポーネントのバージョン](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/)に関するページを参照してください。

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


**HDInsight クラスターをプロビジョニングするには** 

1. [Azure の管理ポータル][azure-management-portal]にサインインします。 

2. 左側にある **[HDInsight]** をクリックして、アカウント内のクラスターの状態を一覧表示します。次のスクリーンショットには、既存の HDInsight クラスターが 1 つもありません。

	![Status of HDInsight clusters in the Azure portal.][image-hdi-clusterstatus]

3. 左下にある **[新規]** をクリックし、**[データ サービス]**、**[HDInsight]**、**[Hadoop]** の順にクリックします。

	![Creation of a Hadoop cluster in HDInsight.][image-hdi-quickcreatecluster]

4. 次の値を入力または選択します。

	<table border="1">
	<tr><th>名前</th><th>値</th></tr>
	<tr><td>クラスター名</td><td>クラスターの名前です。</td></tr>
	<tr><td>クラスター サイズ</td><td>デプロイするデータ ノードの数です。既定値は 4 ですが、ボックスの一覧で 1 つまたは 2 つのデータ ノードを使用するオプションも選択できます。[カスタム作成] オプションを使用すれば、クラスター ノードの数を自由に指定できます。 <strong>カスタム作成</strong> オプションを使用してテーブルを作成する必要があります。各種クラスター サイズの料金を確認するには、このボックスの <strong>[?]</strong> [?] マークをクリックして、表示されるリンクをクリックします。</td></tr>
	<tr><td>パスワード</td><td>admin <i>アカウントの</i> パスワードです。[カスタム作成] オプションを使用していないときは、クラスター ユーザー名 "admin" が指定されます。 <strong>カスタム作成</strong> オプションを使用してテーブルを作成する必要があります。これはクラスターがプロビジョニングされた VM の Windows 管理者アカウントではないことに注意してください。このアカウント名は、カスタム作成ウィザードを使用して変更できます。 <strong>カスタム作成</strong> 続行します。</td></tr>
	<tr><td>ストレージ アカウント</td><td>作成したストレージ アカウントをボックスの一覧から選択します。 <br/>

	ストレージ アカウントは、いったん選択すると、変更することはできません。ストレージ アカウントを削除すると、関連付けたクラスターを使用できなくなります。

	HDInsight クラスターは、ストレージ アカウントと同じデータセンターに配置されます。 
	</td></tr>
	</table>

	クラスター名をメモに記録しておきます。この情報は後で必要になります。

	
5. **[HDInsight クラスターの作成]** をクリックします。プロビジョニングが完了すると、[状態] 列に **[実行中]** と表示されます。

	>[AZURE.NOTE] 上記の手順では、HDInsight クラスター Version 3.1 のクラスターが作成されます。他のクラスター バージョンを作成するには、管理ポータルで [カスタム作成] オプションを使用するか、または Azure PowerShell を使用します。各クラスター バージョンの相違については、[HDInsight で提供されるクラスター バージョンの新機能][hdinsight-versions]に関するページを参照してください。**[カスタム作成]** オプションの使用法については、[カスタム オプションを使用した HDInsight クラスターのプロビジョニング][hdinsight-provision]に関するページを参照してください。


##<a name="sample"></a>ポータルからサンプルを実行する

正常にプロビジョニングされた HDInsight クラスターは、ポータルからサンプルを直接実行するためのクエリ コンソールを提供します。サンプルを使っていくつかの基本的なシナリオを検証することで、HDInsight の操作方法を学ぶことができます。これらのサンプルには、分析対象のデータ、データに対して実行するクエリなど、必要なものがすべて用意されています。 

**サンプルを実行するには、**Azure の管理ポータルでサンプルを実行するクラスターの名前をクリックし、ページの下部にある **[クエリ コンソール]** をクリックします。開いた Web ページの **[入門ギャラリー]** タブをクリックし、**[サンプル]** カテゴリで、実行するサンプルをクリックします。Web ページの指示に従ってサンプルを実行します。各サンプルの内容については、次のリンクをクリックしてください。

サンプル | 内容
------ | ---------------
[センサー データの分析][hdinsight-sensor-data-sample] | HDInsight を使用して、暖房、換気、および空調 (HVAC) システムによって生成された履歴データを処理し、設定した温度を正確に維持できないシステムを識別する方法について説明します。
[Web サイト ログの分析][hdinsight-weblogs-sample] | HDInsight を使用して、Web サイトのログ ファイルを分析する方法について説明します。このサンプルでは、1 日の間に発生した外部 Web サイトからの Web サイトへのアクセス数を調べ、ユーザーが経験した Web サイト エラーの概要を取得します。


##<a name="hivequery"></a>ポータルから Hive クエリを実行する
HDInsight クラスターがプロビジョニングされたら、HDInsight クラスターに付属するサンプルの Hive テーブル *hivesampletable* を照会する Hive ジョブを実行します。このテーブルには、モバイル デバイスの製造元、プラットフォーム、モデルに関するデータが格納されています。ここでは、このテーブルを照会して、特定の製造元のモバイル デバイスのデータを取得します。

> [AZURE.NOTE] HDInsight Tools for Visual Studio は、Azure SDK for .NET バージョン 2.5 以降に付属しています。  Visual Studio からこのツールを使用すると、HDInsight クラスターに接続し、Hive テーブルを作成し、Hive クエリを実行できます。  詳細については、[HDInsight Hadoop Tools for Visual Studio の使用開始][1]に関するページを参照してください。

**クラスター ダッシュボードから Hive ジョブを実行するには**

1. [Azure の管理ポータル][azure-management-portal]にサインインします。 
2. 左側ウィンドウの **[HDINSIGHT]** をクリックします。先のセクションで作成したクラスターを含む、作成済みのクラスターの一覧が表示されます。
3. Hive ジョブを実行するクラスターの名前をクリックし、ページの下部にある **[クエリ コンソール]** をクリックします。 
4. これにより、別のブラウザー タブで Web ページが開きます。Hadoop ユーザー アカウントとパスワードを入力します。  既定のユーザー名は **admin** で、パスワードはプロビジョニング処理中に入力したパスワードです。  ダッシュボードは、次のようになります。

	![Hive Editor tab in the HDInsight cluster dashboard.][img-hdi-dashboard]

	上部には、いくつかのタブが用意されています。  既定のタブは **[Hive エディター]** です。これ以外に、**[ジョブ履歴]** タブと **[ファイル ブラウザー]** タブがあります。  ダッシュボードでは、Hive クエリの送信、Hadoop ジョブ ログの確認、WASB ファイルの参照などの操作を実行できます。

	> [AZURE.NOTE] Web ページの URL は *&lt;ClusterName&gt;.azurehdinsight.net* です。管理ポータルからダッシュボードを開く代わりに、URL を使用して Web ブラウザーからダッシュボードを開くこともできます。

6. **[Hive エディター]** タブで、**[クエリ名]** に「**HTC20**」と入力します。  クエリ名は、ジョブのタイトルです。

7. クエリ ウィンドウに、次のクエリを入力します。 

		SELECT * FROM hivesampletable
			WHERE devicemake LIKE "HTC%"
			LIMIT 20;

	![Query entered in the query pane of the Hive Editor.][img-hdi-dashboard-query-select]

4. **[Submit]** をクリックします。結果が返されるまでしばらく時間がかかります。画面は 30 秒ごとに更新されます。**[最新の情報に更新]** をクリックして画面を更新することもできます。

    処理が完了すると、次のような画面になります。

	![Results from a Hive query in listed at the bottom of the cluster dashboard.][img-hdi-dashboard-query-select-result]

5. 画面のクエリ名をクリックして、出力を表示します。**[ジョブの開始時刻 (UTC)]** の値をメモしておきます。この情報は後で必要になります。 

    ![Job Start Time listed in the Job History tab of the HDInsight cluster dashboard.][img-hdi-dashboard-query-select-result-output]

    このページには、**ジョブの出力**と**ジョブのログ**も表示されます。出力ファイル (\_stdout) とログ ファイル (\_stderr) をダウンロードすることもできます。


	> [AZURE.NOTE] **[Hive エディター]** タブの **[ジョブ セッション]** テーブルには、このタブを表示している限り、完了したジョブまたは実行中のジョブの一覧が表示されます。このページから他のページに移動した場合は、テーブルにジョブは表示されません。**[ジョブ履歴]** タブには、すべてのジョブ (完了したジョブ、実行中のジョブ) の一覧が保持されます。
 

**出力ファイルを参照するには**

1. クラスターのダッシュボードで、上部にある **[ファイル ブラウザー]** をクリックします。 
2. ストレージ アカウント名、コンテナー名 (クラスター名と同じ名前)、**[ユーザー]** の順にクリックします。
3. **[管理者]** をクリックし、以前書き留めたジョブの開始時刻よりも少し後の最終変更時刻の GUID をクリックします。この GUID をメモしておきます。これは、次のセクションで必要になります。


   	![The output file GUID listed in the File Browser tab.][img-hdi-dashboard-query-browse-output]


###<a name="powerquery"></a>Microsoft Business Intelligence ツールに接続する 

Microsoft Excel 用 Power Query アドインを使用すると、HDInsight からのジョブ出力を Excel にインポートして、Microsoft Business Intelligence (BI) ツールで結果をさらに分析することができます。 

チュートリアルのこの部分を完了するには、Excel 2010 または 2013 がインストールされている必要があります。 

**Microsoft Power Query for Excel をダウンロードするには**

- [Microsoft ダウンロード センター](http://www.microsoft.com/ja-jp/download/details.aspx?id=39379) から Microsoft Power Query for Microsoft Excel をダウンロードして、インストールします。

**HDInsight データをインポートするには**

1. Excel を開き、新しい空のブックを作成します。
3. **[Power Query]** メニューをクリックし、**[その他のデータ ソース]**、**[Azure HDInsight から]** の順にクリックします。

	![Excel PowerQuery Import menu open for Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. クラスターに関連付けられた Azure BLOB ストレージ アカウントの名前を **[アカウント名]** ボックスに入力し、**[OK]** をクリックします。これは、このチュートリアルで先に作成したストレージ アカウントです。
4. Azure BLOB ストレージ アカウントのアカウント キーを **[アカウント キー]** ボックスに入力し、**[保存]** をクリックします。 
5. 右側にある [ナビゲーター] ウィンドウで、BLOB ストレージ コンテナーの名前をダブルクリックします。既定で、コンテナー名はクラスター名と同じです。 

6. **[名前]** 列で **stdout** を見つけます。対応する **[フォルダーのパス]** 列の GUID が前の手順でメモした GUID と一致していることを確認します。一致している場合、出力データは送信したジョブに対応しています。**stdout** の左側の **[バイナリ]** をクリックします。

	![Finding the data output by GUID in the list of content.][image-hdi-gettingstarted-powerquery-importdata2]

9. 左上隅にある **[閉じて読み込む]** をクリックして、Hive ジョブ出力を Excel にインポートします。


##<a name="nextsteps"></a>次のステップ
このチュートリアルでは、HDInsight を使用してクラスターをプロビジョニングした後、そのクラスター上で MapReduce ジョブを実行し、結果を Excel にインポートする方法を説明しました。このデータは、BI ツールを使用してさらに処理し、グラフィカルに表示することができます。詳細については、次の記事を参照してください。

- [HDInsight Hadoop Tools for Visual Studio の使用開始][1]
- [HDInsight Emulator の概要][hdinsight-emulator]
- [HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]
- [PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]に関するページ
- [HDInsight へのデータのアップロード][hdinsight-upload-data]に関するページ
- [HDInsight での MapReduce の使用][hdinsight-use-mapreduce]
- [HDInsight での Hive の使用][hdinsight-use-hive]に関するページ
- [HDInsight での Pig の使用][hdinsight-use-pig]に関するページ
- [HDInsight での Oozie の使用][hdinsight-use-oozie]に関するページ
- [HDInsight 用 C# Hadoop ストリーミング プログラムの開発][hdinsight-develop-streaming]
- [HDInsight 用 Java MapReduce プログラムの開発][hdinsight-develop-mapreduce]に関するページ


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-emulator]: ../hdinsight-get-started-emulator/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-weblogs-sample]: ../hdinsight-hive-analyze-website-log/
[hdinsight-sensor-data-sample]: ../hdinsight-hive-analyze-sensor-data/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-get-started/HDI.GetStarted.Video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
<!--HONumber=42-->
