<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop" urlDisplayName="Get Started" pageTitle="Get started using Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using Hadoop in HDInsight, a big data solution. Learn how to provision clusters, run hive jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# HDInsight で Hadoop 1.2 を使用する

<div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="HDInsight で Hadoop 2.2 を使用する">Hadoop 2.2</a>
<a href="../hdinsight-get-started-31" title="HDInsight で Hadoop 2.4 を使用する">Hadoop 2.4</a>
<a href="../hdinsight-get-started-21" title="HDInsight で Hadoop 1.2 を使用する" class="current">Hadoop 1.2</a>
</div>

HDInsight は、[Apache Hadoop][Apache Hadoop] をクラウドのサービスとして利用するためのツールです。HDInsight により、拡張性とコスト効率に優れたシンプルな Azure 環境で MapReduce ソフトウェア フレームワークを利用できます。また、HDInsight は Azure BLOB ストレージを使用して低コストでデータを管理および保存するしくみも備えています。

このチュートリアルでは、Azure 管理ポータルを使用して HDInsight で Hadoop クラスターをプロビジョニングし、クラスター ダッシュボードを使用してサンプル Hive テーブルを照会する Hive ジョブを送信します。次に、調査のために Hive ジョブ出力データを Excel にインポートします。

> [WACOM.NOTE] このチュートリアルでは、HDInsight での Hadoop 1.2 クラスターの使用について説明します。他のサポートされているバージョンについては、ページ上部にあるセレクターをクリックします。バージョン情報については、「[HDInsight で提供されるクラスター バージョンの新機能][HDInsight で提供されるクラスター バージョンの新機能]」を参照してください。

この記事のライブ デモ:
<center>
    ![img-hdi-getstarted-video][img-hdi-getstarted-video]
</center>
Azure の HDInsight を一般に利用可能にすると共に、Microsoft は HDInsight Emulator for Azure (旧称 *Microsoft HDInsight 開発者プレビュー*) もリリースしました。この製品は開発者シナリオを対象としており、そのため単一ノード展開のみをサポートします。HDInsight Emulator の使用法については、「[HDInsight Emulator の概要][HDInsight Emulator の概要]」を参照してください。

**前提条件:**

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

-   Azure サブスクリプション。サブスクリプションの入手方法の詳細については、[購入オプション][購入オプション]、[メンバー プラン][メンバー プラン]、または[無料評価版][無料評価版]に関するページを参照してください。
-   Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone、または Office 2010 Professional Plus がインストールされたコンピューター。

**所要時間:** 30 分

## このチュートリアルの内容

-   [HDInsight クラスターをプロビジョニングする][HDInsight クラスターをプロビジョニングする]
-   [Hive ジョブを実行する][Hive ジョブを実行する]
-   [Microsoft Business Intelligence ツールに接続する][Microsoft Business Intelligence ツールに接続する]
-   [次のステップ][次のステップ]

## <a name="provision"></a>HDInsight クラスターをプロビジョニングする

HDInsight では、データを格納するために Azure BLOB ストレージを使用します。これは *WASB* または *Azure ストレージ - BLOB* と呼ばれています。WASB は、HDFS を Azure BLOB ストレージ上で Microsoft が実装したものです。詳細については、「[HDInsight での Azure BLOB ストレージの使用][HDInsight での Azure BLOB ストレージの使用]」を参照してください。

HDInsight クラスターをプロビジョニングするときに、HDFS と同じように、Azure Storage アカウントと、そのアカウントに対応する特定の BLOB ストレージ コンテナーを、既定のファイル システムとして指定します。ストレージ アカウントは、HDInsight コンピューティング リソースと同じデータ センターに置く必要があります。現在、HDInsight クラスターのプロビジョニングができるのは次のデータ センターだけです。

-   東南アジア
-   北ヨーロッパ
-   西ヨーロッパ
-   米国東部
-   米国西部

このストレージ アカウントに加えて、同じ Azure サブスクリプションまたは別の Azure サブスクリプションに属する付加的なストレージ アカウントを追加することもできます。付加的なストレージ アカウントを追加する方法の詳細については、「[HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]」を参照してください。

このチュートリアルでは、わかりやすくするために、既定の BLOB コンテナーおよび既定のストレージ アカウントのみが使用され、ファイルはすべて既定のファイル システム コンテナー (*/tutorials/getstarted/*) に格納されています。実際には、データ ファイルを専用のストレージ アカウントに格納するのが一般的です。

**Azure ストレージ アカウントを作成するには**

1.  [Azure 管理ポータル][Azure 管理ポータル]にサインインします。
2.  左下隅にある **[新規]** をクリックし、**[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

    ![HDI.StorageAccount.QuickCreate][HDI.StorageAccount.QuickCreate]

3.  **[URL]**、**[場所]**、および **[レプリケーション]** に値を入力し、**[ストレージ アカウントの作成]** をクリックします。アフィニティ グループはサポートされていません。新しいストレージ アカウントがストレージ一覧に表示されます。
4.  新しいストレージ アカウントの **[状態]** 列が **[オンライン]** になるまで待ちます。
5.  一覧の新しいストレージ アカウントをクリックして選択します。
6.  ページの下部にある **[アクセス キーの管理]** をクリックします。
7.  **[ストレージ アカウント名]** と **[プライマリ アクセス キー]** (または **[セカンダリ アクセス キー]**) の値をメモします。どちらのキーでもかまいません。この情報は後で必要になります。

詳細については、「
[How to Create a Storage Account (ストレージ アカウントの作成方法)][How to Create a Storage Account (ストレージ アカウントの作成方法)]」および「[Use Azure Blob Storage with HDInsight (HDInsight での Azure BLOB ストレージの使用)][HDInsight での Azure BLOB ストレージの使用]」を参照してください。

**HDInsight クラスターをプロビジョニングするには**

1.  [Azure 管理ポータル][Azure 管理ポータル]にサインインします。

2.  左側にある **[HDInsight]** をクリックすると、アカウントの下に HDInsight クラスターが一覧表示されます。次のスクリーンショットには、既存の HDInsight クラスターが 1 つもありません。

    ![HDI.ClusterStatus][HDI.ClusterStatus]

3.  左下にある **[新規]** をクリックし、**[データ サービス]**、**[HDInsight]**、**[カスタム作成]** の順にクリックします。

    ![HDI.CustomCreateCluster][HDI.CustomCreateCluster]

4.  [クラスターの詳細] タブで、次の値を入力または選択します。
<table border="1">
<tr><th>名前</th><th>値</th></tr>
<tr><td><strong>クラスター名</strong></td><td>クラスターの名前です。</td></tr>
<tr><td><strong>データ ノード</strong></td><td>デプロイするデータ ノードの数です。テストでは、単一ノード クラスターを作成します。<br />クラスター サイズの制限は、Azure サブスクリプションによって変わります。制限値を上げるには、Azure の課金サポートにお問い合わせください。</td></tr>
<tr><td><strong>HDInsight のバージョン</strong></td><td><strong>[2.1]</strong> を選択し、HDInsight で Hadoop 1.2 クラスターを作成します。</td></tr>
<tr><td><strong>リージョン</strong></td><td>最後の手順で作成したストレージ アカウントと同じリージョンを選択します。HDInsight は、同じリージョンに配置されたストレージ アカウントを必要とします。これ以後の構成作業では、ここで指定した地域と同じリージョンにあるストレージ アカウントしか選択できません。
</td></tr>
</table>

5.  右下隅にある右矢印をクリックして、クラスター ユーザーを構成します。
6.  [クラスター ユーザーの構成] タブで、HDInsight クラスターのユーザー アカウントの **[ユーザー名]** と **[パスワード]** を入力します。クラスターをプロビジョニングした後、このアカウントに加えて、RDP ユーザー アカウントも作成できるため、クラスターにリモート デスクトップ接続することができます。手順については、「[管理ポータルを使用した HDInsight の管理][hdinsight-admin-portal]」を参照してください。
7.  右下隅にある右矢印をクリックして、ストレージ アカウントを構成します。
8.  [ストレージ アカウント] タグで、次の値を入力または選択します。

    | 名前                        | 値                                                                                                                                                                             |
    |-----------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | ストレージ アカウント       | **[既存のストレージを使用する]** を選択します。新しいストレージ アカウントを作成していない場合は、管理ポータルに作成させることもできます。                                     |
    | アカウント名                | このチュートリアルの 1 つ前の手順で作成したストレージ アカウントを指定します。リスト ボックスには同じリージョンのストレージ アカウントのみが表示されることに注意してください。 |
    | 既定のコンテナー            | **[既定のコンテナーの作成]** を選択します。このオプションを選択した場合、既定のコンテナー名はクラスター名と同じになります。                                                    |
    | 追加のストレージ アカウント | **[0]** を選択します。クラスターは最大 7 個の追加のストレージ アカウントに接続することができます。                                                                             |

9.  右下隅にあるチェック アイコンをクリックして、クラスターを作成します。プロビジョニング処理が完了すると、[状態] 列に **[実行中]** と表示されます。

## <a name="sample"></a>Hive ジョブを実行する

HDInsight クラスターのプロビジョニングが終わりました。次の手順は、Hive ジョブを実行して、HDInsight クラスターで提供されるサンプル Hive テーブルを照会することです。テーブル名は *hivesampletable* です。

**クラスターのダッシュボードを開くには**

1.  [Azure 管理ポータル][Azure 管理ポータル]にサインインします。
2.  左側ウィンドウの **[HDINSIGHT]** をクリックします。先のセクションで作成したクラスターなど、作成したクラスターが一覧表示されます。
3.  Hive ジョブを実行するクラスター名をクリックします。
4.  ページの下部にある **[クラスターの管理]** をクリックし、クラスターのダッシュボードを開きます。これにより、別のブラウザー タブで Web ページが開きます。
5.  Hadoop ユーザー アカウントとパスワードを入力します。デフォルトのユーザー名は **admin** で、パスワードはプロビジョニング処理中に入力したパスワードです。ダッシュボードは、次のようになります。

    ![hdi.dashboard][hdi.dashboard]

    上部には、いくつかのタブが用意されています。既定のタブは *[Hive エディター]* で、その他のタブには [ジョブ] や [ファイル] があります。ダッシュボードを使用して、Hive クエリを送信し、Hadoop ジョブ ログを確認して、WASB ファイルを参照することができます。

> [wacom.note] URL が *\<ClusterName\>.azurehdinsight.net* であることに注意してください。管理ポータルからダッシュボードを開く代わりに、URL を使用して Web ブラウザーからダッシュボードを開くこともできます。

**Hive クエリを実行するには**

1.  クラスターのダッシュボードで、上部にある **[Hive エディター]** をクリックします。
2.  **[クエリ名]** に「**HTC20**」と入力します。クエリ名は、ジョブのタイトルです。
3.  クエリ ウィンドウに、次のクエリを入力します。

        SELECT * FROM hivesampletable
            WHERE devicemake LIKE "HTC%"
            LIMIT 20;

    ![hdi.dashboard.query.select][hdi.dashboard.query.select]

4.  **[Submit]** をクリックします。結果が返されるまでしばらく時間がかかります。画面は 30 秒ごとに更新されます。**[最新の情報に更新]** をクリックして画面を更新することもできます。

    処理が完了すると、次のような画面になります。

    ![hdi.dashboard.query.select.result][hdi.dashboard.query.select.result]

    **[ジョブの開始時刻 (UTC)]** の値をメモしておきます。この情報は後で必要になります。

    もう少し下にスクロールすると、**[ジョブのログ]** が表示されます。[ジョブの出力] は stdout で、[ジョブのログ] は stderr です。

5.  後でログ ファイルをもう一度開く場合は、画面上部にある **[ジョブ]** をクリックし、ジョブ タイトル (クエリ名) をクリックします。たとえば、この場合は **[HTC20]** です。

**出力ファイルを参照するには**

1.  クラスターのダッシュボードで、上部にある **[ファイル]** をクリックします。
2.  **[Templeton-Job-Status]** をクリックします。
3.  以前書き留めたジョブの開始時刻よりも少し後の最終変更時刻の GUID 番号をクリックします。この GUID をメモしておきます。これは、次のセクションで必要になります。
4.  **stdout** ファイルには、次のセクションで必要なデータが含まれています。必要な場合は、**stdout** をクリックして、このデータ ファイルのコピーをダウンロードできます。

## <a name="powerquery"></a>Microsoft Business Intelligence ツールに接続する

Power Query for Excel アドインを使用すると、Excel に HDInsight の出力をエクスポートして、Microsoft Business Intelligence (BI) ツールで結果をさらに処理し表示することができます。

チュートリアルのこの部分を完了するには、Excel 2010 または 2013 がインストールされている必要があります。

**Microsoft Power Query for Excel をダウンロードするには**

-   [Microsoft ダウンロード センター][Microsoft ダウンロード センター]から Microsoft Power Query for Excel をダウンロードして、インストールします。

**HDInsight データをインポートするには**

1.  Excel を開き、新しい空のブックを作成します。
2.  **[Power Query]** メニューをクリックし、**[その他のデータ ソース]**、**[Azure HDInsight から]** の順にクリックします。

    ![HDI.GettingStarted.PowerQuery.ImportData][HDI.GettingStarted.PowerQuery.ImportData]

3.  クラスターに関連付けられた Azure BLOB ストレージ アカウントの名前を **[アカウント名]** ボックスに入力し、**[OK]** をクリックします。これは、このチュートリアルで先に作成したストレージ アカウントです。
4.  Azure BLOB ストレージ アカウントのアカウント キーを **[アカウント キー]** ボックスに入力し、**[保存]** をクリックします。
5.  右側にある [ナビゲーター] ウィンドウで、BLOB ストレージ コンテナーの名前をダブルクリックします。既定で、コンテナー名はクラスター名と同じです。

6.  **[名前]** 列の **[stdout]** (パスは *.../Templeton-Job-Status/<guid>*) を見つけて、**[stdout]** の左側にある **[バイナリ]** をクリックします。<guid> は、前のセクションで書いたメモと一致する必要があります。

    ![HDI.GettingStarted.PowerQuery.ImportData2][HDI.GettingStarted.PowerQuery.ImportData2]

7.  左上隅にある **[適用して閉じる]** をクリックします。このクエリは、Hive ジョブ出力を Excel にインポートします。

## <a name="nextsteps"></a>次のステップ

このチュートリアルでは、HDInsight を使用してクラスターをプロビジョニングした後、そのクラスター上で MapReduce ジョブを実行し、結果を Excel にインポートする方法を説明しました。このデータは、BI ツールを使用してさらに処理し、グラフィカルに表示することができます。詳細については、次の記事を参照してください。

-   [HDInsight Emulator の概要][HDInsight Emulator の概要]
-   [HDInsight での Azure BLOB ストレージの使用][HDInsight での Azure BLOB ストレージの使用]
-   [PowerShell を使用した HDInsight の管理][PowerShell を使用した HDInsight の管理]
-   [HDInsight へのデータのアップロード][HDInsight へのデータのアップロード]
-   [HDInsight での MapReduce の使用][HDInsight での MapReduce の使用]
-   [HDInsight での Hive の使用][HDInsight での Hive の使用]
-   [HDInsight での Pig の使用][HDInsight での Pig の使用]
-   [Use Oozie with HDInsight (HDInsight での Oozie の使用)][Use Oozie with HDInsight (HDInsight での Oozie の使用)]
-   [Develop C# Hadoop streaming programs for HDInsight (HDInsight 用 C# Hadoop ストリーミング プログラムの開発)][Develop C# Hadoop streaming programs for HDInsight (HDInsight 用 C# Hadoop ストリーミング プログラムの開発)]
-   [Develop Java MapReduce programs for HDInsight (HDInsight 用 Java MapReduce プログラムの開発)][Develop Java MapReduce programs for HDInsight (HDInsight 用 Java MapReduce プログラムの開発)]

  [Apache Hadoop]: http://hadoop.apache.org/
  [HDInsight で提供されるクラスター バージョンの新機能]: ../hdinsight-component-versioning/
  [img-hdi-getstarted-video]: https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS
  [HDInsight Emulator の概要]: ../hdinsight-get-started-emulator/
  [購入オプション]: http://azure.microsoft.com/ja-jp/pricing/purchase-options/
  [メンバー プラン]: http://azure.microsoft.com/ja-jp/pricing/member-offers/
  [無料評価版]: http://azure.microsoft.com/ja-jp/pricing/free-trial/
  [HDInsight クラスターをプロビジョニングする]: #provision
  [Hive ジョブを実行する]: #sample
  [Microsoft Business Intelligence ツールに接続する]: #powerquery
  [次のステップ]: #nextsteps
  [HDInsight での Azure BLOB ストレージの使用]: ../hdinsight-use-blob-storage/
  [HDInsight クラスターのプロビジョニング]: ../hdinsight-provision-clusters/
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  [HDI.StorageAccount.QuickCreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
  [HDI.ClusterStatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [hdi.dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
  [hdi.dashboard.query.select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
  [hdi.dashboard.query.select.result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
  [Microsoft ダウンロード センター]: http://www.microsoft.com/ja-jp/download/details.aspx?id=39379
  [HDI.GettingStarted.PowerQuery.ImportData]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
  [HDI.GettingStarted.PowerQuery.ImportData2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
  [PowerShell を使用した HDInsight の管理]: ../hdinsight-administer-use-powershell/
  [HDInsight へのデータのアップロード]: ../hdinsight-upload-data/
  [HDInsight での MapReduce の使用]: ../hdinsight-use-mapreduce
  [HDInsight での Hive の使用]: ../hdinsight-use-hive/
  [HDInsight での Pig の使用]: ../hdinsight-use-pig/
