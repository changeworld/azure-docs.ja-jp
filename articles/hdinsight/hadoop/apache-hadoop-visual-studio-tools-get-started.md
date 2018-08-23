---
title: Data Lake Tools for Visual Studio を使用した Azure HDInsight への接続
description: Data Lake Tools for Visual Studio をインストールし、それを使用して Azure HDInsight の Hadoop クラスターに接続し、Hive クエリを実行する方法について説明します。
keywords: Hadoop ツール, Hive クエリ, Visual Studio, Visual Studio Hadoop
services: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive, hdiseo17may2017
ms.topic: get-started-article
ms.date: 05/16/2018
ms.openlocfilehash: d4f444133e8fe6f5ba1a8c12ce08d5c66e8fc18e
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "41920625"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-hive-queries"></a>Data Lake Tools for Visual Studio を使用して Azure HDInsight に接続し、Hive クエリを実行する

Data Lake Tools for Visual Studio (Azure Data Lake and Stream Analytics Tools for Visual Studio とも呼ばれます) を使用して [Azure HDInsight](../hdinsight-hadoop-introduction.md) の Hadoop クラスターに接続し、Hive クエリを送信する方法について説明します。 

HDInsight の使用に関する詳細については、[HDInsight の概要](../hdinsight-hadoop-introduction.md)と [HDInsight の使用](apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。 

Storm クラスターへの接続に関する詳細については、[Visual Studio を使用した HDInsight での Apache Storm の C# トポロジの開発](../storm/apache-storm-develop-csharp-visual-studio-topology.md)に関するページを参照してください。

Data Lake Tools for Visual Studio を使用して、Azure Data Lake Analytics と HDInsight にアクセスできます。 Data Lake Tools の詳細については、「[Data Lake Tools for Visual Studio を使用して U-SQL スクリプトを開発する](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了して、Data Lake Tools for Visual Studio を使用するには、次のものが必要です。

* Azure HDInsight クラスター。 HDInsight クラスターを作成するには、「[Hadoop チュートリアル: HDInsight で Hadoop を使用する](apache-hadoop-linux-tutorial-get-started.md)」をご覧ください。 対話型 Hive クエリを実行するには、[HDInsight 対話型クエリ](../interactive-query/apache-interactive-query-get-started.md) クラスターが必要です。
* Visual Studio 2017、2015、または 2013 がインストールされているコンピューター。
    
    > [!NOTE]
    > 現時点では、Data Lake Tools for Visual Studio の英語版のみを使用できます。
    > 
    > 

## <a name="install-or-update-data-lake-tools-for-visual-studio"></a>Azure Data Lake Tools for Visual Studio をインストールまたは更新する

### <a name="install-data-lake-tools"></a>Data Lake Tools のインストール

Visual Studio 2017 では、Data Lake Tools が既定でインストールされています。 前のバージョンの Visual Studio では、[Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) を使用して Data Lake Tools をインストールできます。 お使いの Visual Studio バージョンに対応する Data Lake Tools のバージョンを選択します。 

### <a name="install-visual-studio"></a>Visual Studio のインストール

Visual Studio がインストールされていない場合は、[Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) を使用して、最新バージョンの Visual Studio Community と Azure SDK をインストールできます。

![Data Lake Tools for Visual Studio Web Platform Installer のスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png "Web Platform Installer を使用して Data Lake Tools for Visual Studio をインストールする")

### <a name="update-the-tools"></a>ツールを更新する

1. Visual Studio を開きます。
2. **[ツール]** メニューの **[拡張機能と更新プログラム]** を選択します。
3. **[更新]** を展開し、**[Azure Data Lake and Stream Analytics Tools]** を選択します (インストールされている場合)。

> [!NOTE]
>
> 対話型クエリ クラスターに接続して対話型 Hive クエリを実行するには、Data Lake Tools のバージョン 2.3.0.0 以降のみを使用できます。

## <a name="connect-to-azure-subscriptions"></a>Azure サブスクリプションに接続する
Data Lake Tools for Visual Studio を使用して、HDInsight クラスターへの接続、いくつかの基本的な管理操作の実行、および Hive クエリの実行が可能です。

> [!NOTE]
> 汎用の Hadoop クラスターに接続する方法の詳細については、「[Write and submit Hive queries by using Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx)」(Visual Studio を使用した Hive クエリの書き込みと送信) をご覧ください。
> 
> 

Azure サブスクリプションに接続するには:

1. Visual Studio を開きます。
2. **[表示]** メニューの **[サーバー エクスプローラー]** を選択します。
3. サーバー エクスプローラーで、**[Azure]**、**[HDInsight]** の順に展開します。
   
   > [!NOTE]
   > **[HDInsight タスク一覧]** ウィンドウが開きます。 ウィンドウが表示されない場合は、**[表示]** メニューの **[その他のウィンドウ]** を選択し、**[HDInsight タスク一覧ウィンドウ]** を選択します。  
   > 
   > 
4. Azure サブスクリプションの資格情報を入力し、**[サインイン]** を選択します。 認証は、このコンピューターで、まだ一度も Visual Studio から Azure サブスクリプションに接続していない場合にのみ必要です。
5. サーバー エクスプローラーに、既存の HDInsight クラスターの一覧が表示されます。 クラスターが 1 つもない場合は、Azure ポータル、Azure PowerShell、または HDInsight SDK を使用して作成できます。 詳細については、[HDInsight クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。
   
   ![サーバー エクスプローラーの Data Lake Tools for Visual Studio クラスター一覧のスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png "サーバー エクスプローラーの Data Lake Tools for Visual Studio クラスター一覧")
6. HDInsight クラスターを展開します。 **Hive データベース**、既定のストレージ アカウント、リンクされたストレージ アカウント、および **Hadoop サービス ログ**が表示されます。 さらに、エンティティを展開できます。

Azure サブスクリプションに接続した後で、次のタスクを実行できます。

Visual Studio から Azure Portal に接続するには:

1. サーバー エクスプローラーで、**[Azure]** > **[HDInsight]** を選択します。
2. HDInsight クラスターを右クリックし、**[Azure Portal でのクラスターの管理]** を選択します。

Visual Studio から質問をしたりフィードバックを提供したりするには:

1. **[ツール]** メニューの **[HDInsight]** を選択します。
2. 質問を送信するには、**[MSDN フォーラム]** を選択します。 フィードバックを提供するには、**[フィードバックを送る]** を選択します。

## <a name="explore-linked-resources"></a>リンクされたリソースを調べる
サーバー エクスプローラーで、既定のストレージ アカウントとリンクされたストレージ アカウントを確認できます。 既定のストレージ アカウントを展開すると、そのストレージ アカウントのコンテナーを表示できます。 既定のストレージ アカウントと既定のコンテナーがマークされます。 コンテナーのコンテンツを表示するには、コンテナーを右クリックします。

![サーバー エクスプローラーの Data Lake Tools for Visual Studio クラスターのリンクされたリソース一覧のスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png "リンクされたリソース一覧")

コンテナーを開くと、次のボタンを使用して、BLOB をアップロード、削除、およびダウンロードすることができます。

![サーバー エクスプローラーでの Data Lake Tools for Visual Studio の BLOB 操作のスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png "サーバー エクスプローラーでの BLOB のアップロード、削除、およびダウンロード")

## <a name="run-interactive-hive-queries"></a>対話型 Hive クエリの実行
[Apache Hive](http://hive.apache.org) は、Hadoop に組み込まれているデータ ウェアハウス インフラストラクチャです。 Hive は、データの概要、クエリ、および分析に使用されます。 Data Lake Tools for Visual Studio を使用して、Visual Studio から Hive クエリを実行できます。 Hive の詳細については、[HDInsight での Hive の使用](hdinsight-use-hive.md)に関するページを参照してください。

[対話型クエリ](../interactive-query/apache-interactive-query-get-started.md)では、Apache Hive 2.1 の [Hive on LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) を使用します。 対話型クエリでは、格納されている大規模なデータセットに対する複雑なデータ ウェアハウス スタイルのクエリを対話形式で実行できます。 対話型クエリは、従来の Hive バッチ ジョブと比べ、Hive クエリの実行速度が格段に速くなっています。 詳細については、「[Hive バッチ ジョブの実行](#run-hive-batch-jobs)」を参照してください。

> [!NOTE]
>
> 対話型 Hive クエリは、[HDInsight 対話型クエリ](../interactive-query/apache-interactive-query-get-started.md) クラスターに接続している場合のみ実行できます。

Data Lake Tools for Visual Studio を使用して、Hive ジョブの内容を表示することもできます。 Data Lake Tools for Visual Studio は、特定の Hive ジョブを収集し、Yarn ログを表示できます。

### <a name="view-hivesampletable"></a>**hivesampletable** を表示する
すべての HDInsight クラスターには、hivesampletable という名前の既定のサンプル Hive テーブルが付属します。 この Hive テーブルは、Hive テーブルの一覧表示、テーブル スキーマの表示、Hive テーブル内の行の一覧表示を行う方法を定義します。

Hive テーブルと Hive テーブル スキーマを表示するには:

1. テーブル スキーマを表示するには、**サーバー エクスプローラー**で、**[Azure]** > **[HDInsight]** を選択します。 クラスターを選択し、**[Hive データベース]** > **[既定]** > **[hivesampletable]** を選択します。
2. **[hivesampletable]** を右クリックし、**[上位 100 行を表示]** をクリックして、行を一覧表示します。 これは、Hive ODBC ドライバーを使用して、次の Hive クエリを実行することと同等です。
   
     `SELECT * FROM hivesampletable LIMIT 100`
   
   行カウントをカスタマイズできます。
   
   ![HDInsight Hive Visual Studio スキーマ クエリのスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png "Hive クエリの結果")

### <a name="create-hive-tables"></a>Hive テーブルの作成
Hive テーブルを作成するには、GUI または Hive クエリを使用できます。 Hive クエリの使用については、 [Hive クエリの実行](#run.queries)をご覧ください。

Hive テーブルを作成するには:

1. **サーバー エクスプローラー**で、**[Azure]** > **[HDInsight クラスター]** を選択します。 HDInsight クラスターを選択し、**[Hive データベース]** を選択します。
2. **[既定]** を右クリックし、**[テーブルの作成]** を選択します。
3. テーブルを構成します。  
4. **[テーブルの作成]** を選択して、新しい Hive テーブルを作成するためのジョブを送信します。
   
    ![HDInsight Visual Studio Tools の テーブル作成ウィンドウのスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png "Hive テーブルの作成")

### <a name="run.queries"></a>Hive クエリの検証と実行
Hive クエリを作成して実行するためのオプションは 2 つあります。

* アドホック クエリを作成する
* Hive アプリケーションを作成する

アドホック クエリを作成、検証、実行するには:

1. **サーバー エクスプローラー**で、**[Azure]** > **[HDInsight クラスター]** を選択します。
2. クエリを実行するクラスターを右クリックし、**[Hive クエリの作成]** を選択します。  
3. Hive クエリを入力します。 

    Hive エディターは IntelliSense をサポートしています。 Data Lake Tools for Visual Studio では、Hive スクリプトの編集時にリモート メタデータの読み込みをサポートします。 たとえば、**SELECT * FROM** と入力すると、IntelliSense によって提案されるテーブル名が一覧表示されます。 テーブル名を指定すると、Intellisense によって列名が一覧表示されます。 このツールは、Hive の DML ステートメント、サブクエリ、および組み込みの UDF の大半をサポートします。
   
    ![HDInsight Visual Studio Tools での IntelliSense の例 1 のスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png "U-SQL IntelliSense")
   
    ![HDInsight Visual Studio Tools での IntelliSense の例 2 のスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png "U-SQL IntelliSense")
   
   > [!NOTE]
   > IntelliSense は、HDInsight のツール バーで選択されているクラスターのメタデータのみを推奨します。
   > 
   
4. (省略可能) スクリプトの構文エラーをチェックするには、**[スクリプトの検証]** を選択します。
   
    ![Data Lake Tools for Visual Studio でのローカル検証のスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png "スクリプトの検証")
5. **[送信]** または **[Submit (Advanced)]\(送信 (詳細設定)\)** を選択します。 [Submit (Advanced)]\(送信 (詳細設定)\) オプションを選択した場合は、スクリプトの **[ジョブ名]**、**[引数]**、**[追加の構成]**、**[状態ディレクトリ]** を構成します。
   
    ![HDInsight Hadoop の Hive クエリのスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "クエリの送信")
   
    ジョブを送信すると、**[Hive ジョブの概要]** ウィンドウが表示されます。
   
    ![HDInsight Hadoop の Hive クエリの概要のスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png "Hive ジョブの概要")
6. **[更新]** ボタンを使用して、ジョブのステータスが **[完了]** に変更されるまで、ステータスを更新します。
7. **[ジョブ クエリ]**、**[ジョブ出力]**、**[ジョブのログ]** または **[Yarn ログ]** を表示するには、ウィンドウ下部の該当するリンクを選択します。

Hive ソリューションを作成して実行するには:

1. **[ファイル]** メニューから **[新規]**、**[プロジェクト]** の順に選択します。
2. 左側のウィンドウで、**[HDInsight]** を選択します。 中央のウィンドウで、**[Hive アプリケーション]** を選択します。 プロパティを入力し、**[OK]** を選択します。
   
    ![HDInsight Visual Studio Tools の新しい Hive プロジェクトのスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Visual Studio から Hive アプリケーションを作成する")
3. **ソリューション エクスプローラー**で、**Script.hql** をダブルクリックしてスクリプトを開きます。
4. Hive スクリプトを検証するには、**[スクリプトの検証]** ボタンを選択します。 または、Hive エディターでスクリプトを右クリックし、コンテキスト メニューから **[スクリプトの検証]** を選択します。

### <a name="view-hive-jobs"></a>Hive ジョブの表示
Hive ジョブのジョブ クエリ、ジョブ出力、ジョブのログ、Yarn ログを表示できます。 詳細については、前のスクリーンショットをご覧ください。

最新版のツールでは、Yarn ログを収集して表示することで、Hive ジョブの内容を確認できます。 Yarn ログは、パフォーマンス問題の検証に役立ちます。 HDInsight での Yarn ログの収集に関する詳細については、[プログラムによる HDInsight アプリケーション ログへのアクセス](../hdinsight-hadoop-access-yarn-app-logs.md)に関するページを参照してください。

Hive ジョブを表示するには:

1. **サーバー エクスプローラー**で、**[Azure]**、**[HDInsight]** の順に展開します。
2. HDInsight クラスターを右クリックし、**[ジョブの表示]** を選択します。 クラスターで実行された Hive ジョブの一覧が表示されます。  
3. ジョブを選択します。 **[Hive ジョブの概要]** ウィンドウで、次のいずれかを選択します。
    - **ジョブ クエリ**
    - **ジョブの出力**
    - **ジョブのログ**  
    - **Yarn ログ**
   
    ![HDInsight Visual Studio Tools の [Hive ジョブの表示] ウィンドウのスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png "Hive ジョブの表示")

### <a name="faster-path-hive-execution-via-hiveserver2"></a>HiveServer2 を使用した Hive 実行の高速パス
> [!NOTE]
> この機能は、HDInsight バージョン 3.2 以降のクラスターでのみ動作します。
 
Data Lake Tools for Visual Studio は、以前は [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (Templeton とも呼ばれます) を介して Hive ジョブを送信していました。 この方法での Hive ジョブの送信では、ジョブの詳細とエラー情報を返すのに長い時間がかかっていました。

このパフォーマンス上の問題を解決するため、Data Lake Tools for Visual Studio では HiveServer2 の使用によって、RDP/SSH をバイパスしてクラスターで直接 Hive ジョブを実行できるようになりました。

パフォーマンスの向上に加え、この方法では、Apache Tez グラフやタスクの詳細で Hive を表示することもできます。

HDInsight クラスター バージョン 3.2 以降では、**[HiveServer2 から実行]** ボタンが表示されます。

![Data Lake Tools for Visual Studio の HiveServer2 オプションを使用した実行のスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png "HiveServer2 を使用した Hive クエリの実行")

ログをリアルタイムでストリーミングして表示することもできます。 Hive クエリが Tez で実行されている場合は、ジョブ グラフも表示できます。

![Data Lakes Tools for Visual Studio の HiveServer2 を使用した Hive 実行の高速パスのスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png "ジョブ グラフの表示")

### <a name="execute-queries-via-hiveserver2-vs-submit-queries-via-webhcat"></a>HiveServer2 を使用したクエリの実行と WebHCat を使用したクエリの送信

HiveServer2 を使用してクエリを実行した場合、パフォーマンス上の利点は多数ありますが、この方法にはいくつかの制限があります。 一部の制限によって、この方法は運用環境での使用に適さないことがあります。 

次の表は、HiveServer2 でのクエリの実行と WebHCat でのクエリの送信の違いを示しています。

|  | HiveServer2 での実行 | WebHCat での送信 |
| --- | --- | --- |
| クエリの実行 |WebHCat のオーバーヘッドが解消されます (これにより、TempletonControllerJob という名前の MapReduce ジョブが開始されます)。 |クエリが WebHCat を使用して実行される間、WebHCat によって MapReduce ジョブが開始されるため、待機時間が長くなります。 |
| ログの再ストリーミング |ほぼリアルタイムで行われます。 |ジョブの実行ログを利用できるのは、ジョブが完了したときのみです。 |
| ジョブ履歴の表示 |クエリが HiveServer2 を使用して実行される場合、そのジョブ履歴 (ジョブのログ、ジョブの出力) は保持されません。 Yarn UI でアプリケーションを表示できますが、情報は限定的です。 |クエリが WebHCat を使用して実行される場合、そのジョブ履歴 (ジョブのログ、ジョブの出力) は保持されます。 Visual Studio、HDInsight SDK、または PowerShell を使用してジョブ履歴を表示できます。 |
| ウィンドウを閉じる |HiveServer2 を使用した実行は "*同期*" しています。 ウィンドウが閉じた場合、クエリの実行は取り消されます。 |WebHCat を使用した送信は、"*非同期*" で行われます。 WebHCat を使用してクエリを送信した後、Visual Studio を閉じることができます。 結果は、いつでも戻って確認できます。 |

### <a name="tez-hive-job-performance-graph"></a>Tez Hive ジョブのパフォーマンス グラフ
Data Lake Tools for Visual Studio では、Tez 実行エンジンが実行している Hive ジョブのパフォーマンス グラフを表示できます。 Tez の有効化の情報については、[HDInsight での Hive の使用](hdinsight-use-hive.md)に関するページを参照してください。 

Visual Studio で Hive ジョブを送信した後、ジョブが完了すると、Visual Studio にグラフが表示されます。 最新のジョブの状態を表示するには、**[更新]** ボタンを選択する必要があります。

> [!NOTE]
> この機能は、HDInsight バージョン 3.2.4.593 以降のクラスターでのみ使用できます。 この機能は、完了したジョブに対してのみ機能します。 この機能を使用するには、WebHCat を使用してジョブを送信する必要があります。 HiveServer2 を使用してクエリを実行した場合は、次の図が表示されます。 
> 
> ![Hadoop Hive Tez パフォーマンス グラフのスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png "ジョブの状態")

Hive クエリを理解しやすくするために、今回のリリースで Hive 演算子表示機能が追加されました。 頂点内のすべての演算子を表示するには、ジョブ グラフの頂点をダブルクリックします。 特定の演算子をポイントして、演算子の詳細を表示することもできます。

### <a name="task-execution-view-for-hive-on-tez-jobs"></a>Hive on Tez ジョブのタスク実行ビュー
Hive on Tez ジョブのタスク実行ビューを使用して、構造化および視覚化された Hive ジョブの情報を取得できます。 ジョブの詳細を取得することもできます。 パフォーマンスの問題が発生した場合に、このビューを使用して、問題の詳細を取得できます。 たとえば、各タスクの動作の情報と、各タスクの詳細情報 (データの読み取り/書き込み、開始時刻/終了時刻のスケジュールなど) を取得できます。 これらの情報を使用して、視覚化された情報に基づくジョブの構成やシステム アーキテクチャを調整します。

![Data Lake Visual Studio Tools のタスク実行ビュー ウィンドウのスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png "タスク実行ビュー")

## <a name="run-hive-batch-jobs"></a>Hive バッチ ジョブの実行
HDInsight クラスターに対する Hive スクリプトのテストは、(対話型クエリ クラスター以外は) 時間がかかる可能性があります。 このプロセスには、数分以上かかることがあります。 Data Lake Tools for Visual Studio では、ライブ クラスターに接続しなくても、Hive スクリプトをローカルで検証できます。 対話型クエリの実行の詳細については、「[対話型 Hive クエリの実行](#run-interactive-hive-queries)」を参照してください。

Data Lake Tools for Visual Studio では、特定の Hive ジョブの Yarn ログを収集して表示することで、Hive ジョブの内容を確認できます。

Hive バッチ ジョブの実行方法の詳細については、「[対話型 Hive クエリの実行](#run-interactive-hive-queries)」を参照してください。 そのセクションの情報は、実行に時間がかかる Hive バッチ ジョブに適用されます。

## <a name="run-pig-scripts"></a>Pig のスクリプトを実行する
Data Lake Tools for Visual Studio を使用して、Pig スクリプトの作成と HDInsight クラスターへの送信を行うことができます。 最初に、テンプレートから Pig プロジェクトを作成します。 次に、スクリプトを HDInsight クラスターに送信します。

## <a name="feedback-and-known-issues"></a>フィードバックと既知の問題
* 現在 HiveServer2 の結果は、プレーンテキスト形式で表示され、これは最適とは言えません。 Microsoft では、この問題の解決に取り組んでいます。
* null 値で始まる結果が表示されない問題は修正されています。 この問題が発生している場合は、サポート チームに問い合わせてください。
* ユーザーのローカルのリージョン設定によっては、Visual Studio によって作成された HQL スクリプトがエンコードされます。 このスクリプトをバイナリ ファイルとしてクラスターにアップロードした場合、スクリプトは正しく実行されません。

## <a name="next-steps"></a>次の手順
この記事では、Data Lake Tools for Visual Studio パッケージを使用して、Visual Studio から HDInsight クラスターに接続する方法を説明しました。 Hive クエリを実行する方法についても説明しました。 詳細と例については、次の記事をご覧ください。

* [HDInsight での Hadoop Hive の使用](hdinsight-use-hive.md)
* [HDInsight での Hadoop の使用](apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight での Hadoop ジョブの送信](submit-apache-hadoop-jobs-programmatically.md)
* [HDInsight での Hadoop を使用した Twitter データの分析](../hdinsight-analyze-twitter-data.md)

