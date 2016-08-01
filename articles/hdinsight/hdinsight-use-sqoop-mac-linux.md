<properties
	pageTitle="Linux ベースの HDInsight での Hadoop Sqoop の使用 | Microsoft Azure"
	description="Sqoop を使用して、HDInsight クラスター上の Linux ベースの Hadoop と Azure SQL データベース間でインポートおよびエクスポートする方法について説明します。"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/20/2016"
	ms.author="larryfr"/>

#HDInsight の Hadoop での Sqoop の使用 (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Linux ベースの HDInsight クラスターと Azure SQL Database または SQL Server データベースの間のインポートとエクスポートに Sqoop を使用する方法について説明します。

> [AZURE.NOTE] この記事の手順では、Linux ベースの HDInsight クラスターへの接続に SSH を使用します。Windows クライアントでは、Azure PowerShell および HDInsight .NET SDK を使用して、Linux ベースのクラスターで Sqoop を使用することもできます。これらの記事を表示するには、タブ セレクターをクリックしてください。

##前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **HDInsight の Hadoop クラスター**と __Azure SQL Database__: このドキュメント内の手順は、[クラスターと SQL Database の作成](hdinsight-use-sqoop.md#create-cluster-and-sql-database)に関するドキュメントに沿って作成されたクラスターとデータベースを前提としています。既に HDInsight クラスターと SQL Database がある場合は、このドキュメントで使用されている値を適宜置き換えてください。
- **ワークステーション**: SSH クライアントを使用しているコンピューター。

##FreeTDS のインストール

1. SSH を使用して、Linux ベースの HDInsight クラスターに接続します。接続するときに使用するアドレスは `CLUSTERNAME-ssh.azurehdinsight.net` で、ポートは `22` です。

	SSH を使用して HDInsight に接続する方法の詳細については、次のドキュメントを参照してください。

    * **Linux、Unix または OS X クライアント**: 「[Linux、OS X または Unix からの Linux ベースの HDInsight クラスターへの接続](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)」を参照してください。

    * **Windows クライアント**: 「[Windows からの Linux ベースの HDInsight クラスターへの接続](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)」を参照してください。

3. 次のコマンドを使用して FreeTDS をインストールします。

        sudo apt-get --assume-yes install freetds-dev freetds-bin

    FreeTDS は、SQL Database に接続する際のいくつかの手順で使用します。

##SQL Database へのテーブルの作成

> [AZURE.IMPORTANT] [クラスターと SQL Database の作成](hdinsight-use-sqoop.md)の手順に沿って作成した HDInsight クラスターと SQL Database を使用している場合、データベースとテーブルはそのドキュメントの手順の中で作成しているため、このセクションの手順は無視してください。

1. HDInsight への SSH 接続から、次のコマンドを使用して SQL Database サーバーに接続し、以降の手順で使用するテーブルを作成します。

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    次のような出力が返されます。

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. `1>` プロンプトで、以下の行を入力します。

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
        GO

    `GO` ステートメントを入力すると、前のステートメントが評価されます。最初に、**mobiledata** テーブルが作成され、次にクラスター化インデックスがそのテーブルに追加されます (SQL Database が必要)。

    次を使用して、テーブルが作成されたことを確認します。

        SELECT * FROM information_schema.tables
        GO

    次のような出力が表示されます。

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

8. `1>` プロンプトで「`exit`」と入力して、tsql ユーティリティを終了します。

##Sqoop のエクスポート

3. HDInsight への SSH 接続から、次のコマンドを使用して、Sqoop が SQL Database を認識できることを確認します。

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    これは、先ほど作成した **sqooptest** データベースを含むデータベースの一覧を返します。

4. 次のコマンドを使用して、**hivesampletable** から **mobiledata** テーブルにデータをエクスポートします。

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    これにより、SQL Database と **sqooptest** データベースに接続して **wasb:///hive/warehouse/hivesampletable** (*hivesampletable* の物理ファイル) から** mobiledata テーブルにデータをエクスポートするよう Sqoop に指示します。

5. コマンドが完了したら、次を使用して、TSQL によってデータベースに接続します。

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    接続されたら、次のステートメントを使用して、データが **mobiledata** テーブルにエクスポートされたことを確認します。

        SELECT * FROM mobiledata
        GO

    テーブル内のデータの一覧が表示されます。「`exit`」と入力して、tsql ユーティリティを終了します。

##Sqoop のインポート

1. 次を使用して、SQL Database の **mobiledata** テーブルから HDInsight の **wasb:///tutorials/usesqoop/importeddata** ディレクトリにデータをインポートします。

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    インポートされたデータには、タブ文字で区切られたフィールドと、改行文字で終了する行が含まれます。

2. インポートが完了したら、次のコマンドを使用して、新しいディレクトリのデータを列挙します。

        hadoop fs -text wasb:///tutorials/usesqoop/importeddata/part-m-00000

##SQL Server の使用

Sqoop を使用すると、Azure でホストされているデータ センターまたは仮想マシンで、SQL Server からデータをインポートしたり、エクスポートしたりすることもできます。SQL Database と SQL Server の使用方法には、次の違いがあります。

* HDInsight と SQL Server の両方が、同じ Azure Virtual Network に存在する必要があります。

    > [AZURE.NOTE] HDInsight は場所ベースの仮想ネットワークのみをサポートし、アフィニティ グループ ベースの仮想ネットワークは現在扱っていません。

    SQL Server をデータセンター内で使用している場合は、仮想ネットワークを*サイト間*または*ポイント対サイト*として構成する必要があります。

    > [AZURE.NOTE] **ポイント対サイト**仮想ネットワークの場合、SQL Server が VPN クライアント構成アプリケーションを実行している必要があります。このアプリケーションは、Azure 仮想ネットワーク構成の**ダッシュボード**から入手できます。

    仮想ネットワークの作成と構成方法の詳細については、「[仮想ネットワークの構成タスク](../services/virtual-machines/)」を参照してください。

* SQL 認証を許可するよう、SQL Server を構成する必要があります。詳細については、「[認証モードの選択](https://msdn.microsoft.com/ms144284.aspx)」を参照してください。

* リモート接続を許可するよう、SQL Server を構成する必要がある場合があります。詳細については、[SQL Server データベース エンジンへの接続に関するトラブルシューティングの方法](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)に関するページを参照してください。

* **SQL Server Management Studio** または **tsql** などのユーティリティを使用して SQL Server データベースに **sqooptest** テーブルを作成する必要があります。Azure CLI を使用する手順は、Azure SQL Database でのみ機能します。

    **mobiledata** テーブルを作成する TSQL ステートメントは、SQL Database に使用する TSQL ステートメントに似ています。ただし、クラスター化インデックスの作成は例外です。SQL Server の場合、これは必要ありません。

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])

* Azure Virtual Network で名前を解決するためにドメイン ネーム システム (DNS) を構成していないと、HDInsight から SQL Server に接続するときに、SQL Server の IP アドレスを使用する必要がある場合があります。次に例を示します。

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##制限事項

* 一括エクスポート - Linux ベースの HDInsight では、Microsoft SQL Server または Azure SQL Database にデータをエクスポートするために使用する Sqoop コネクタは、一括挿入を現在サポートしていません。

* バッチ処理 - Linux ベースの HDInsight で、挿入処理実行時に `-batch` スイッチを使用すると、Sqoop は挿入操作をバッチ処理するのではなく、複数の挿入処理を実行します。

##次のステップ

ここでは Sqoop の使用方法を説明しました。詳細については、次を参照してください。

- [HDInsight での Oozie の使用][hdinsight-use-oozie]\: Oozie ワークフローで Sqoop アクションを使用します。
- [HDInsight を使用したフライト遅延データの分析][hdinsight-analyze-flight-data]\: Hive を使用してフライト遅延データを分析し、Sqoop を使用して Azure SQL データベースにデータをエクスポートします。
- [HDInsight へのデータのアップロード][hdinsight-upload-data]\: HDInsight/Azure BLOB ストレージにデータをアップロードするその他の方法を説明します。



[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!---HONumber=AcomDC_0720_2016-->