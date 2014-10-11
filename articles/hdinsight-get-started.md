<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop" urlDisplayName="Get Started" pageTitle="Get started using Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using Hadoop in HDInsight, a big data solution. Learn how to provision clusters, run hive jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop in HDInsight" authors="nitinme" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="nitinme"></tags>

# HDInsight で Hadoop 2.4 を使用する

<div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="HDInsight で Hadoop 2.4 を使用する" class="current">Hadoop 2.4</a>
<a href="../hdinsight-get-started-30" title="HDInsight で Hadoop 2.2 を使用する">Hadoop 2.2</a>
<!--a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a-->
</div>

HDInsight により、MapReduce ソフトウェア フレームワークの Apache Hadoop を、拡張性とコスト効率に優れたシンプルな Azure 環境で利用できます。また、HDInsight は Azure BLOB ストレージを使用して低コストでデータを管理および保存するしくみも備えています。

> [WACOM.NOTE] Hadoop とビッグ データを初めて扱う方は、[Apache Hadoop][]、[MapReduce][]、[HDFS][]、[Hive][] に関するトピックを参照してください。HDInsight によって Azure でどのように Hadoop を利用できるかについては、「[Introduction to Hadoop in HDInsight (HDInsight の Hadoop 入門)][]」を参照してください。

Azure の HDInsight を一般に利用可能にすると共に、Microsoft は HDInsight Emulator for Azure (旧称 *Microsoft HDInsight 開発者プレビュー*) もリリースしました。このエミュレーターは開発者シナリオを対象としており、単一ノード デプロイメントのみをサポートします。HDInsight Emulator の使用法については、「[HDInsight Emulator の概要][]」を参照してください。

> [WACOM.NOTE] HBase クラスターをプロビジョニングする方法については、「[Get started using HBase with Hadoop in HDInsight (HDInsight で HBase と Hadoop を使用する)][]」を参照してください。2 つのうちのどちらかを選ぶ理由については、「[What Is The Difference Between HBase and Hadoop/HDFS? (HBase と Hadoop/HDFS の違い)][]」を参照してください。

## このチュートリアルで目的とする操作

構造化されていないデータ セットが大量にあり、このデータ セットに対してクエリを実行して、意味のある情報を抽出したいとします。これが、このチュートリアルで目的としていることです。これを実現するには、次の手順を実行します。

![HDI.GetStartedFlow][]

このチュートリアルのデモ ビデオも用意されています。

<center>
<iframe width="560" height="315" src="http://www.youtube.com/embed/v=Y4aNjnoeaHA?list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" frameborder="0" allowfullscreen>
</iframe>
</center>
<!--center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target = "_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center-->

**前提条件:**

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

-   Azure サブスクリプション。サブスクリプションの入手方法の詳細については、[購入オプション][]、[メンバー プラン][]、または[無料評価版][]に関するページを参照してください。
-   Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone、または Office 2010 Professional Plus がインストールされたコンピューター。

**所要時間:** 30 分

## このチュートリアルの内容

-   [Azure のストレージ アカウントの作成][]
-   [HDInsight クラスターをプロビジョニングする][]
-   [Hive ジョブを実行する][]
-   [Microsoft Business Intelligence ツールに接続する][]
-   [次のステップ][]

## <a name="storage"></a>Azure ストレージ アカウントの作成

HDInsight では、データを格納するために Azure BLOB ストレージを使用します。これは *WASB* または *Azure ストレージ - BLOB* と呼ばれています。WASB は、HDFS を Azure BLOB ストレージ上で Microsoft が実装したものです。詳細については、「[HDInsight での Azure BLOB ストレージの使用][]」を参照してください。

HDInsight クラスターをプロビジョニングするときは、Azure Storage アカウントを指定します。HDFS と同様、このアカウントの特定の BLOB ストレージ コンテナーが、既定のファイル システムとして設定されます。既定では、HDInsight クラスターは、指定されたストレージ アカウントと同じデータ センターにプロビジョニングされます。

このストレージ アカウントに加えて、HDInsight クラスターの構成をカスタマイズするときに他のストレージ アカウントを追加できます。この追加のストレージ アカウント用には、同じ Azure サブスクリプションを使用することも異なる Azure サブスクリプションを使用することもできます。手順については、「[Provision HDInsight clusters using custom options (カスタム オプションを使用した HDInsight クラスターのプロビジョニング)][]」を参照してください。

このチュートリアルでは、わかりやすくするために、既定の BLOB コンテナーと既定のストレージ アカウントが使用されています。実際には、データ ファイルを専用のストレージ アカウントに格納するのが一般的です。

**Azure ストレージ アカウントを作成するには**

1.  [Azure 管理ポータル][]にサインインします。
2.  左下隅にある **[新規]** をクリックし、**[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

    ![HDI.StorageAccount.QuickCreate][]

3.  **[URL]**、**[場所]**、および **[レプリケーション]** に値を入力し、**[ストレージ アカウントの作成]** をクリックします。アフィニティ グループはサポートされていません。新しいストレージ アカウントがストレージ一覧に表示されます。

    > このチュートリアルで使用したような、HDInsight クラスターをプロビジョニングする簡易作成オプションでは、クラスターをプロビジョニングする際に場所をたずねるメッセージが表示されません。クラスターは、既定でストレージ アカウントと同じデータ センターに配置されます。そのため、ストレージ アカウントは、クラスターでサポートされる場所 (**東アジア**、**東南アジア**、**北ヨーロッパ**、**西ヨーロッパ**、**米国東部**、**米国西部**、**米国中北部**、**米国中南部**) に作成してください。

4.  新しいストレージ アカウントの **[状態]** 列が **[オンライン]** になるまで待ちます。
5.  一覧の新しいストレージ アカウントを選択し、ページの下部の **[アクセス キーの管理]** をクリックします。
6.  **[ストレージ アカウント名]** と **[プライマリ アクセス キー]** (または **[セカンダリ アクセス キー]**) の値をメモします。どちらのキーでもかまいません。この情報は後で必要になります。

詳細については、「
[How to Create a Storage Account (ストレージ アカウントの作成方法)][]」および「[Use Azure Blob Storage with HDInsight (HDInsight での Azure BLOB ストレージの使用)][HDInsight での Azure BLOB ストレージの使用]」を参照してください。

## <a name="provision"></a>HDInsight クラスターをプロビジョニングする

HDInsight クラスターをプロビジョニングすると、Hadoop と関連アプリケーションを含む Azure コンピューティング リソースがプロビジョニングされます。このセクションでは、Hadoop Version 2.4 に基づく HDInsight クラスター Version 3.1 をプロビジョニングします。HDInsight クラスターと Hadoop Version 2.2 をプロビジョニングする場合は、この記事の先頭にある目的のバージョンのタブをクリックしてください。また、HDInsight PowerShell コマンドレットまたは HDInsight .NET SDK を使用して、他のバージョン用の Hadoop クラスターを作成することもできます。手順については、「[Provision HDInsight clusters using custom options (カスタム オプションを使用した HDInsight クラスターのプロビジョニング)][]」を参照してください。異なる HDInsight バージョンとその SLA については、[HDInsight コンポーネントのバージョン][]に関するページを参照してください。

**HDInsight クラスターをプロビジョニングするには**

1.  [Azure 管理ポータル][]にサインインします。

2.  左側にある **[HDInsight]** をクリックして、アカウント内のクラスターの状態を一覧表示します。次のスクリーンショットには、既存の HDInsight クラスターが 1 つもありません。

    ![HDI.ClusterStatus][]

3.  左下にある **[新規]** をクリックし、**[データ サービス]**、**[HDInsight]**、**[Hadoop]** の順にクリックします。

    ![HDI.QuickCreateCluster][]

4.  次の値を入力または選択します。

	<table border="1">
    <tr><th>名前                 </th><th>値     </th></tr>                                                                                                                                                                                                                                                                                                                                                           
	<tr><td>クラスター名         </td><td>クラスターの名前です。 </td></tr>
    <tr><td>クラスター サイズ    </td><td>デプロイするデータ ノードの数です。既定値は 4 ですが、ボックスの一覧で 1 つまたは 2 つのデータ ノードを使用するオプションも選択できます。<strong>[カスタム作成]</strong> オプションを使用すれば、データ ノードの数を自由に指定できます。各種クラスター サイズの料金を確認するには、このボックスのすぐ上にある <strong>[?]</strong> マークをクリックして、表示されるリンクをクリックします。</td></tr>
    <tr><td>パスワード           </td><td>  <i>admin</i> アカウントのパスワードです。[カスタム作成] オプションを使用していないときは、クラスター ユーザー名 "admin" が指定されます。これはクラスターがプロビジョニングされた VM の Windows 管理者アカウントではないことに注意してください。このアカウント名は、<strong>カスタム作成</strong>ウィザードを使用して変更できます。</td></tr>
    <tr><td>ストレージ アカウント</td><td>作成したストレージ アカウントをボックスの一覧から選択します。 <br/>ストレージ アカウントは、いったん選択すると、変更することはできません。ストレージ アカウントを削除すると、関連付けたクラスターを使用できなくなります。 HDInsight クラスターは、ストレージ アカウントと同じデータセンターに配置されます。</td></tr>
	</table>

    クラスター名をメモに記録しておきます。この情報は後で必要になります。

5.  **[HDInsight クラスターの作成]** をクリックします。プロビジョニングが完了すると、[状態] 列に **[実行中]** と表示されます。

    > [WACOM.NOTE] 上記の手順では、HDInsight クラスター Version 3.1 のクラスターが作成されます。他のクラスター バージョンを作成するには、管理ポータルで [カスタム作成] オプションを使用するか、または Azure PowerShell を使用します。各クラスター バージョンの相違については、「[What's new in the Hadoop cluster versions provided by HDInsight? (HDInsight で提供される Hadoop クラスター バージョンの新機能)][]」を参照してください。**[カスタム作成]** オプションの使用法については、「[Provision HDInsight clusters using custom options (カスタム オプションを使用した HDInsight クラスターのプロビジョニング)][]」を参照してください。

## <a name="sample"></a>Hive ジョブを実行する

HDInsight クラスターがプロビジョニングされたら、HDInsight クラスターに付属するサンプルの Hive テーブル *hivesampletable* を照会する Hive ジョブを実行します。このテーブルには、モバイル デバイスの製造元、プラットフォーム、モデルに関するデータが格納されています。ここでは、このテーブルを照会して、特定の製造元のモバイル デバイスのデータを取得します。

**クラスター ダッシュボードから Hive ジョブを実行するには**

1.  [Azure 管理ポータル][]にサインインします。
2.  左側ウィンドウの **[HDINSIGHT]** をクリックします。先のセクションで作成したクラスターを含む、作成済みのクラスターの一覧が表示されます。
3.  Hive ジョブを実行するクラスターの名前をクリックし、ページの下部にある **[クラスターの管理]** をクリックします。
4.  これにより、別のブラウザー タブで Web ページが開きます。Hadoop ユーザー アカウントとパスワードを入力します。既定のユーザー名は **admin** で、パスワードはプロビジョニング処理中に入力したパスワードです。ダッシュボードは、次のようになります。

    ![hdi.dashboard][]

    上部には、いくつかのタブが用意されています。既定のタブは **[Hive エディター]** です。これ以外に、**[ジョブ履歴]** タブと **[ファイル ブラウザー]** タブがあります。ダッシュボードでは、Hive クエリの送信、Hadoop ジョブ ログの確認、WASB ファイルの参照などの操作を実行できます。

    > [WACOM.NOTE] Web ページの URL は *\<ClusterName\>.azurehdinsight.net* です。管理ポータルからダッシュボードを開く代わりに、URL を使用して Web ブラウザーからダッシュボードを開くこともできます。

5.  **[Hive エディター]** タブで、**[クエリ名]** に「**HTC20**」と入力します。クエリ名は、ジョブのタイトルです。

6.  クエリ ウィンドウに、次のクエリを入力します。

        SELECT * FROM hivesampletable
            WHERE devicemake LIKE "HTC%"
            LIMIT 20;

    ![hdi.dashboard.query.select][]

7.  **[Submit]** をクリックします。結果が返されるまでしばらく時間がかかります。画面は 30 秒ごとに更新されます。**[最新の情報に更新]** をクリックして画面を更新することもできます。

    処理が完了すると、次のような画面になります。

    ![hdi.dashboard.query.select.result][]

8.  画面のクエリ名をクリックして、出力を表示します。**[ジョブの開始時刻 (UTC)]** の値をメモしておきます。この情報は後で必要になります。

    ![hdi.dashboard.query.select.result.output][]

    このページには、**ジョブの出力**と**ジョブのログ**も表示されます。出力ファイル (\_stdout) とログ ファイル (\_stderr) をダウンロードすることもできます。

    > [WACOM.NOTE] **[Hive エディター]** タブの **[ジョブ セッション]** テーブルには、このタブを表示している限り、完了したジョブまたは実行中のジョブの一覧が表示されます。このページから他のページに移動した場合は、テーブルにジョブは表示されません。**[ジョブ履歴]** タブには、すべてのジョブ (完了したジョブ、実行中のジョブ) の一覧が保持されます。

**出力ファイルを参照するには**

1.  クラスターのダッシュボードで、上部にある **[ファイル ブラウザー]** をクリックします。
2.  ストレージ アカウント名、コンテナー名 (クラスター名と同じ名前)、**[ユーザー]** の順にクリックします。
3.  [admin] をクリックし、以前書き留めたジョブの開始時刻よりも少し後の最終変更時刻の GUID 番号をクリックします。この GUID をメモしておきます。これは、次のセクションで必要になります。

    ![hdi.dashboard.query.browse.output][]

## <a name="powerquery"></a>Microsoft Business Intelligence ツールに接続する

Microsoft Excel 用 Power Query アドインを使用すると、HDInsight からのジョブ出力を Excel にインポートして、Microsoft Business Intelligence (BI) ツールで結果をさらに分析することができます。

チュートリアルのこの部分を完了するには、Excel 2010 または 2013 がインストールされている必要があります。

**Microsoft Power Query for Excel をダウンロードするには**

-   [Microsoft ダウンロード センター][]から Microsoft Power Query for Microsoft Excel をダウンロードして、インストールします。

**HDInsight データをインポートするには**

1.  Excel を開き、新しい空のブックを作成します。
2.  **[Power Query]** メニューをクリックし、**[その他のデータ ソース]**、**[Azure HDInsight から]** の順にクリックします。

    ![HDI.GettingStarted.PowerQuery.ImportData][]

3.  クラスターに関連付けられた Azure BLOB ストレージ アカウントの名前を **[アカウント名]** ボックスに入力し、**[OK]** をクリックします。これは、このチュートリアルで先に作成したストレージ アカウントです。
4.  Azure BLOB ストレージ アカウントのアカウント キーを **[アカウント キー]** ボックスに入力し、**[保存]** をクリックします。
5.  右側にある [ナビゲーター] ウィンドウで、BLOB ストレージ コンテナーの名前をダブルクリックします。既定で、コンテナー名はクラスター名と同じです。

6.  **[名前]** 列で **stdout** を見つけます。対応する [フォルダーのパス] 列の GUID が前の手順でメモした GUID と一致していることを確認します。**stdout** の左側の **[バイナリ]** をクリックします。

    ![HDI.GettingStarted.PowerQuery.ImportData2][]

7.  左上隅にある **[閉じて読み込む]** をクリックして、Hive ジョブ出力を Excel にインポートします。

## <a name="nextsteps"></a>次のステップ

このチュートリアルでは、HDInsight を使用してクラスターをプロビジョニングした後、そのクラスター上で MapReduce ジョブを実行し、結果を Excel にインポートする方法を説明しました。このデータは、BI ツールを使用してさらに処理し、グラフィカルに表示することができます。詳細については、次の記事を参照してください。

-   [HDInsight Emulator の概要][]
-   [HDInsight での Azure BLOB ストレージの使用][]
-   [PowerShell を使用した HDInsight の管理][]
-   [HDInsight へのデータのアップロード][]
-   [HDInsight での MapReduce の使用][]
-   [HDInsight での Hive の使用][]
-   [HDInsight での Pig の使用][]
-   [Use Oozie with HDInsight (HDInsight での Oozie の使用)][]
-   [Develop C# Hadoop streaming programs for HDInsight (HDInsight 用 C# Hadoop ストリーミング プログラムの開発)][]
-   [Develop Java MapReduce programs for HDInsight (HDInsight 用 Java MapReduce プログラムの開発)][]

  [Hadoop 2.4]: ../hdinsight-get-started "HDInsight で Hadoop 2.4 を使用する"
  [Hadoop 2.2]: ../hdinsight-get-started-30 "HDInsight で Hadoop 2.2 を使用する"
  [Apache Hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
  [MapReduce]: http://go.microsoft.com/fwlink/?LinkId=510086
  [HDFS]: http://go.microsoft.com/fwlink/?LinkId=510087
  [Hive]: http://go.microsoft.com/fwlink/?LinkId=510085
  [Introduction to Hadoop in HDInsight (HDInsight の Hadoop 入門)]: ../hdinsight-introduction/
  [HDInsight Emulator の概要]: ../hdinsight-get-started-emulator/
  [Get started using HBase with Hadoop in HDInsight (HDInsight で HBase と Hadoop を使用する)]: http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/
  [What Is The Difference Between HBase and Hadoop/HDFS? (HBase と Hadoop/HDFS の違い)]: http://go.microsoft.com/fwlink/?LinkId=510237
  [HDI.GetStartedFlow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png
  [購入オプション]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [メンバー プラン]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [無料評価版]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Azure のストレージ アカウントの作成]: #storage
  [HDInsight クラスターをプロビジョニングする]: #provision
  [Hive ジョブを実行する]: #sample
  [Microsoft Business Intelligence ツールに接続する]: #powerquery
  [次のステップ]: #nextsteps
  [HDInsight での Azure BLOB ストレージの使用]: ../hdinsight-use-blob-storage/
  [Provision HDInsight clusters using custom options (カスタム オプションを使用した HDInsight クラスターのプロビジョニング)]: ../hdinsight-provision-clusters/
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  [HDI.StorageAccount.QuickCreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
  [How to Create a Storage Account (ストレージ アカウントの作成方法)]: ../storage-create-storage-account/
  [HDInsight コンポーネントのバージョン]: http://azure.microsoft.com/en-us/documentation/articles/hdinsight-component-versioning/
  [HDI.ClusterStatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
  [HDI.QuickCreateCluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png
  [What's new in the Hadoop cluster versions provided by HDInsight? (HDInsight で提供される Hadoop クラスター バージョンの新機能)]: ../hdinsight-component-versioning/
  [hdi.dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
  [hdi.dashboard.query.select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
  [hdi.dashboard.query.select.result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
  [hdi.dashboard.query.select.result.output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
  [hdi.dashboard.query.browse.output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png
  [Microsoft ダウンロード センター]: http://www.microsoft.com/en-us/download/details.aspx?id=39379
  [HDI.GettingStarted.PowerQuery.ImportData]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
  [HDI.GettingStarted.PowerQuery.ImportData2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
  [PowerShell を使用した HDInsight の管理]: ../hdinsight-administer-use-powershell/
  [HDInsight へのデータのアップロード]: ../hdinsight-upload-data/
  [HDInsight での MapReduce の使用]: ../hdinsight-use-mapreduce
  [HDInsight での Hive の使用]: ../hdinsight-use-hive/
  [HDInsight での Pig の使用]: ../hdinsight-use-pig/
  [Use Oozie with HDInsight (HDInsight での Oozie の使用)]: ../hdinsight-use-oozie/
  [Develop C# Hadoop streaming programs for HDInsight (HDInsight 用 C# Hadoop ストリーミング プログラムの開発)]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Develop Java MapReduce programs for HDInsight (HDInsight 用 Java MapReduce プログラムの開発)]: ../hdinsight-develop-deploy-java-mapreduce/
