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
	ms.date="03/09/2016"
	ms.author="larryfr"/>

#HDInsight の Hadoop での Sqoop の使用 (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Linux ベースの HDInsight クラスターと Azure SQL Database または SQL Server データベースの間のインポートとエクスポートに Sqoop を使用する方法について説明します。

> [AZURE.NOTE] この記事の手順では、Linux ベースの HDInsight クラスターへの接続に SSH を使用します。Windows クライアントでは、「[HDInsight の Hadoop での Sqoop の使用 (PowerShell)](hdinsight-use-sqoop.md)」で説明されているように、Azure PowerShell を使用して Linux ベースのクラスターで Sqoop を使用することもできます。

##Sqoop とは

Hadoop はログやファイルなどの非構造化データおよび半構造化データを処理する場合に自然な選択ですが、リレーショナル データベースに格納された構造化データを処理する必要が生じることもあります。

[Sqoop][sqoop-user-guide-1.4.4] は、Hadoop クラスターとリレーショナル データベース間でデータを転送するためのツールです。このツールを使用して、SQL Server、MySQL、Oracle などのリレーショナル データベース管理システム (RDBMS) から Hadoop 分散ファイル システム (HDFS) へデータをインポートしたり、MapReduce または Hive を使用して Hadoop のデータを変換してから、そのデータを RDBMS にエクスポートして戻したりできます。このチュートリアルでは、リレーショナル データベースとして SQL Server データベースを使用します。

HDInsight クラスターでサポートされている Sqoop のバージョンについては、「[HDInsight で提供されるクラスター バージョンの新機能][hdinsight-versions]」を参照してください。


##前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **ワークステーション**: SSH クライアントを使用しているコンピューター。

- **Azure CLI**: 詳細については、[Azure CLI のインストールおよび構成](../xplat-cli-install.md)に関するページを参照してください。

##シナリオの理解

HDInsight クラスターにはサンプル データがいくつか付属しています。****wasb:///hive/warehouse/hivesampletable** にあるデータ ファイルを参照する **hivesampletable** という名前の Hive テーブルを使用します。テーブルはモバイル デバイスのデータを含んでいます。Hive テーブルのスキーマは次のとおりです。

| フィールド | データ型 |
| ----- | --------- |
| clientid | 文字列 |
| querytime | 文字列 |
| market | 文字列 |
| deviceplatform | 文字列 |
| devicemake | 文字列 |
| devicemodel | 文字列 |
| state | 文字列 |
| country | 文字列 |
| querydwelltime | double |
| sessionid | bigint |
| sessionpagevieworder | bigint |

まず、**hivesampletable** を Azure SQL Database、または SQL Server の **mobiledata** という名前のテーブルにエクスポートしてから、そのテーブルを **wasb:///tutorials/usesqoop/importeddata** の HDInsight にインポートします。


## クラスターと SQL Database を作成します。

1. 次の画像をクリックして Azure ポータルで ARM テンプレートを開きます。         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    ARM テンプレートはパブリック BLOB コンテナー内にあります (**https://hditutorialdata.blob.core.windows.net/usesqoop/create-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json*)。
    
    ARM テンプレートでは、SQL Database にテーブル スキーマをデプロイするために bacpac パッケージを呼び出します。bacpac パッケージは、パブリック BLOB コンテナー内にもあります (https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac)。bacpac ファイルのプライベート コンテナーを使用する場合は、テンプレートで、次の値を使用します。
    
        "storageKeyType": "Primary",
        "storageKey": "<TheAzureStorageAccountKey>",
    
2. [パラメーター] ブレードで、次の各項目を入力します。

    - **ClusterName**: 作成する Hadoop クラスターの名前を入力します。
    - **クラスターのログイン名とパスワード**: 既定のログイン名は admin です。
    - **SSH のユーザー名とパスワード**。
    - **SQL Database サーバーのログイン名およびパスワード**。

    次の値は、変数セクションにハードコードされています。
    
    |既定のストレージ アカウント名|<CluterName>ストア|
    |----------------------------|-----------------|
    |Azure SQL データベース サーバー名|<ClusterName>dbserver|
    |Azure SQL データベース名|<ClusterName>db|
    
    これらの値を書き留めておいてください。この情報は後で必要になります。
    
3\. **[OK]** をクリックしてパラメーターを保存します。

4\. **[カスタム デプロイ]** ブレードで **[リソース グループ]** ボックスをクリックし、**[新規]** をクリックして新しいリソース グループを作成します。リソース グループとは、クラスター、依存するストレージ アカウント、その他のリンクされたリソースをグループ化しているコンテナーです。

5\. **[法律条項]** をクリックし、**[作成]** をクリックします。

6\. **[作成]** をクリックします。"Submitting deployment for Template deployment" という新しいタイルが表示されます。クラスターと SQL Database の作成には約 20 分かかります。

既存の Azure SQL Database または Microsoft SQL Server を使用する場合

- **Azure SQL データベース**: ワークステーションから Azure SQL データベース サーバーに対するアクセスを許可するようにファイアウォール ルールを構成する必要があります。Azure SQL データベースを作成して、ファイアウォールを構成する手順については、「[Azure SQL データベースの概要][sqldatabase-get-started]」を参照してください。 

    > [AZURE.NOTE] 既定では、Azure SQL データベースは Azure HDinsight などの Azure サービスからの接続を許可します。このファイアウォール設定が無効になっている場合は、Azure ポータルから有効にする必要があります。Azure SQL データベースの作成方法とファイアウォール ルールの構成方法については、「[SQL データベースの作成と構成][sqldatabase-create-configue]」を参照してください。

- **SQL Server**: HDInsight クラスターが SQL Server と同じ Azure の仮想ネットワーク上にある場合は、この記事の手順を使用して、SQL Server データベースとの間でデータをインポートおよびエクスポートできます。

    > [AZURE.NOTE] HDInsight は場所ベースの仮想ネットワークのみをサポートし、アフィニティ グループ ベースの仮想ネットワークは現在扱っていません。

    * 仮想ネットワークを作成および構成する場合は、「[Virtual Network の構成タスク](../services/virtual-machines/)」を参照してください。

        * SQL Server をデータセンター内で使用している場合は、仮想ネットワークを*サイト間*または*ポイント対サイト*として構成する必要があります。

            > [AZURE.NOTE] **ポイント対サイト**仮想ネットワークの場合、SQL Server が VPN クライアント構成アプリケーションを実行している必要があります。このアプリケーションは、Azure 仮想ネットワーク構成の**ダッシュボード**から入手できます。

        * SQL Server を Azure 仮想マシンで使用する際に、SQL Server をホストする仮想マシンが HDInsight と同じ仮想ネットワークに属している場合は、任意の仮想ネットワーク構成を使用できます。

    * 仮想ネットワークに HDInsight クラスターを作成する場合は、「[カスタム オプションを使用した HDInsight での Hadoop クラスターの作成](hdinsight-provision-clusters.md)」を参照してください。

    > [AZURE.NOTE] SQL Server では認証を許可する必要もあります。この記事の手順を実行するには、SQL Server ログインを使用する必要があります。
	

##Sqoop のエクスポート

2. 次のコマンドを使用して、Sqoop lib ディレクトリから、SQL Server JDBC ドライバーへのリンクを作成します。これにより、Sqoop がこのドライバーを使用して SQL Database と対話できます。

        sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /usr/hdp/current/sqoop-client/lib/sqljdbc41.jar

3. 次のコマンドを使用して、Sqoop が SQL Database を認識できることを確認します。

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    これは、先ほど作成した **sqooptest** データベースを含むデータベースの一覧を返します。

4. 次のコマンドを使用して、**hivesampletable** から **mobiledata** テーブルにデータをエクスポートします。

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    これにより、SQL Database と **sqooptest** データベースに接続して ****wasb:///hive/warehouse/hivesampletable** (*hivesampletable* の物理ファイル) から **mobiledata** テーブルにデータをエクスポートするよう Sqoop に指示します。

5. コマンドが完了したら、次を使用して、TSQL によってデータベースに接続します。

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    接続されたら、次のステートメントを使用して、データが **mobiledata** テーブルにエクスポートされたことを確認します。

        SELECT * FROM mobiledata
        GO

    テーブル内のデータの一覧が表示されます。「`exit`」と入力して、tsql ユーティリティを終了します。

##Sqoop のインポート

1. 次を使用して、SQL Database の **mobiledata** テーブルから HDInsight の ****wasb:///tutorials/usesqoop/importeddata** ディレクトリにデータをインポートします。

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

<!---HONumber=AcomDC_0323_2016-->