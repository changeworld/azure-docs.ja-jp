<properties
   pageTitle="ポータルを使用して Azure Data Lake Store で HDInsight Hadoop クラスターを作成する | Azure"
   description="Azure ポータルを使用して、Azure Data Lake Store で HDInsight Hadoop クラスターを作成し、使用します。"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/11/2016"
   ms.author="nitinme"/>

# Azure ポータルを使用して、Data Lake Store を使用する HDInsight クラスターを作成する

> [AZURE.SELECTOR]
- [ポータルの使用](data-lake-store-hdinsight-hadoop-use-portal.md)
- [PowerShell の使用](data-lake-store-hdinsight-hadoop-use-powershell.md)


Azure ポータルを使用して、Azure Data Lake Store にアクセスするように HDInsight クラスター (Hadoop、HBase、または Storm) を作成する方法について説明します。このリリースに関する重要な考慮事項をいくつか以下に示します。

* **Hadoop クラスター (Windows および Linux) の場合**、Data Lake Store は、追加のストレージ アカウントとしてのみ使用できます。このようなクラスターの既定のストレージ アカウントは、Azure Storage BLOB (WASB) のままです。

* **Storm クラスター (Windows および Linux) の場合**、Data Lake Store は、Storm トポロジからデータを書き込むために使用できます。Data Lake Store は、Storm トポロジから読み取ることができる、参照データを格納するために使用することもできます。詳細については、「[Storm トポロジで Data Lake Store を使用する](#use-data-lake-store-in-a-storm-topology)」を参照してください。

* **HBase クラスター (Windows および Linux) の場合**、Data Lake Store を既定のストレージまたは追加ストレージとして使用できます。Data Lake Store にアクセスできる HBase クラスターを作成するオプションは、HDI バージョン 3.1、または 3.2 (Windows用) または HDI バージョン 3.2 (Linux 用) を使用している場合のみあります。詳細については、「[HBase クラスターで Data Lake Store を使用する](#use-data-lake-store-with-hbase-clusters)」を参照してください。


## 前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
- Data Lake Store パブリック プレビューに対して、**Azure サブスクリプションを有効にする**。[手順](data-lake-store-get-started-portal.md#signup)を参照してください。
- **Azure Data Lake Store アカウント**。「[Azure ポータルで Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。アカウントを作成したら、次のタスクを実行しいくつかサンプル データをアップロードします。このデータは、チュートリアルの後半で Data Lake Store 内のデータにアクセスする HDInsight クラスターからジョブを実行するために必要です。

	* [Data Lake Store にフォルダーを作成する](data-lake-store-get-started-portal.md#createfolder)。
	* [Data Lake Store にファイルをアップロードする](data-lake-store-get-started-portal.md#uploaddata)。アップロードするサンプル データを探している場合は、[Azure Data Lake Git リポジトリ](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData)から **Ambulance Data** フォルダーを取得できます。


## Azure Data Lake Store にアクセスできる HDInsight クラスターを作成する

このセクションでは、Data Lake Store を追加のストレージとして使用する HDInsight Hadoop クラスターを作成します。このリリースでは、Hadoop クラスターの場合、Data Lake Store はクラスターの追加のストレージとしてのみ使用できます。既定のストレージは、Azure Storage BLOB (WASB) のままです。そのため、クラスターに必要なストレージ アカウントとストレージ コンテナーを最初に作成します。

1. 新しい [Azure ポータル](https://portal.azure.com)にサインオンします。

2. 「[HDInsight で Hadoop クラスターを作成する](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal)」の手順に従って、HDInsight クラスターのプロビジョニングを開始します。

3. **[オプションの構成]** ブレードで、**[データ ソース]** をクリックします。**[データ ソース]** ブレードでストレージ アカウントおよびストレージ コンテナーの詳細を指定し、**[場所]** を **[East US 2]** として指定して、**[クラスター AAD ID]** をクリックします。

	![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "HDInsight クラスターにサービス プリンシパルを追加する")

4. **[クラスター AAD ID]** ブレードで、既存のサービス プリンシパルを選択するか、または新しいサービス プリンシパルを作成することができます。

	* **新しいサービス プリンシパルを作成する**

		* **[クラスター AAD ID]** ブレードで **[新規作成]**、**[サービス プリンシパル]** の順にクリックし、**[サービス プリンシパルの作成]** ブレードで、新しいサービス プリンシパルを作成するための値を指定します。その一環として、証明書と Azure Active Directory アプリケーションも作成されます。**[作成]** をクリックします。

			![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "HDInsight クラスターにサービス プリンシパルを追加する")

		* **[クラスター AAD ID]** ブレードで、**[ADLS アクセスを管理する]** をクリックします。ウィンドウに、サブスクリプションに関連付けられている Data Lake Store アカウントが表示されます。ただし、権限を設定できるのは作成したアカウントのみです。HDInsight クラスターに関連付けるアカウントに、読み取り、書き込み、実行権限を選択し、**[アクセス許可の保存]** をクリックします。

			![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "HDInsight クラスターにサービス プリンシパルを追加する")

		* **[クラスター AAD ID]** ブレードで、**[証明書のダウンロード]** をクリックし、作成したサービス プリンシパルに関連付けられている証明書をダウンロードします。これは、HDInsight クラスターを他にも作成しているが、後でその同じサービス プリンシパルを使用したい場合に役立ちます。**[選択]** をクリックします。

			![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "HDInsight クラスターにサービス プリンシパルを追加する")


	* **既存のサービス プリンシパルを選択する:**

		* **[クラスター AAD ID]** ブレードで、**[既存のものを使用]**、**[サービス プリンシパル]** の順にクリックし、**[サービス プリンシパルの選択]** ブレードで既存のサービス プリンシパルを探します。サービス プリンシパルの名前をクリックし、**[選択]** をクリックします。

			![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "HDInsight クラスターにサービス プリンシパルを追加する")

		* **[クラスター AAD ID]** ブレードで、選択したサービス プリンシパルに関連付けられている証明書 (.pfx) をアップロードし、証明書のパスワードを指定します。

		* **[ADLS アクセスを管理する]** をクリックします。ウィンドウに、サブスクリプションに関連付けられている Data Lake Store アカウントが表示されます。ただし、権限を設定できるのは作成したアカウントのみです。HDInsight クラスターに関連付けるアカウントに、読み取り、書き込み、実行権限を選択し、**[アクセス許可の保存]** をクリックします。

			![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.existing.save.png "HDInsight クラスターにサービス プリンシパルを追加する")

		* **[アクセス許可の保存]**、**[選択]** の順にクリックします。

6. **[データ ソース]** ブレードで **[選択]** をクリックし、「[HDInsight で Hadoop クラスターを作成する](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal)」の説明に従ってクラスターのプロビジョニングに進みます。

7. クラスターがプロビジョニングされたら、サービス プリンシパルが HDInsight クラスターに関連付けられていることを確認できます。そのためには、クラスター ブレードで **[設定]** をクリックし、**[クラスター AAD ID]** をクリックして、関連付けられているサービス プリンシパルを確認します。

	![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "HDInsight クラスターにサービス プリンシパルを追加する")

## Azure Data Lake Store を使用する HDInsight クラスターでテスト ジョブを実行する

HDInsight クラスターを構成したら、クラスターでテスト ジョブを実行して、HDInsight クラスターが Azure Data Lake Store のデータにアクセス可能であるかどうかをテストできます。そのためには、Data Lake Store を対象とするいくつかの Hive クエリを実行します。

### Linux クラスターの場合

1. プロビジョニングしたクラスターのクラスター ブレードを開き、**[ダッシュボード]** をクリックします。これにより、Linux クラスターの Ambari が開きます。Ambari にアクセスすると、サイトに対する認証が求められます。クラスターを作成するときに使用した管理者アカウント名 (既定値は admin) とパスワードを入力します。

	![クラスター ダッシュボードの起動](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "クラスター ダッシュボードの起動")

	Web ブラウザーで https://CLUSTERNAME.azurehdinsight.net にアクセスして Ambari に直接移動することもできます (ここで **CLUSTERNAME** は HDInsight クラスターの名前です)。

2. Hive ビューを開きます。ページ メニュー (ページの右側の **Admin** リンク ボタンの横) から四角形のセットを選択して使用可能なビューを一覧表示します。**[Hive]** ビューを選択します。

	![Selecting ambari views](./media/data-lake-store-hdinsight-hadoop-use-portal/selecthiveview.png)

3. 次のようなページが表示されます。

	![Image of the hive view page, containing a query editor section](./media/data-lake-store-hdinsight-hadoop-use-portal/hiveview.png)

4. ページの **[Query Editor]** セクションで、次の HiveQL ステートメントをワークシートに貼り付けます。

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

5. **クエリ エディター**の下部にある **[Execute]** ボタンをクリックしてクエリを開始します。**クエリ エディター**の下に **[Query Process Results]** セクションが表示され、ジョブに関する情報が表示されます。

6. クエリが完了すると、**[Query Process Results]** セクションに操作の結果が表示されます。**[Results]** タブには次の情報が表示されます。

7. 次のクエリを実行して、テーブルが作成されたことを確認します。

		SHOW TABLES;

	**[Results]** タブには以下が表示されます。

		hivesampletable
		vehicles

	**vehicles** は、前に作成したテーブルです。**hivesampletable** は、既定ですべての HDInsight クラスターで使用できるサンプル テーブルです。

8. **vehicles** テーブルからデータを取得するクエリを実行することもできます。

		SELECT * FROM vehicles LIMIT 5;

### Windows クラスターの場合

1. プロビジョニングしたクラスターのクラスター ブレードを開き、**[ダッシュボード]** をクリックします。

	![クラスター ダッシュボードの起動](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "クラスター ダッシュボードの起動")

	入力を求められたら、クラスターの管理者資格情報を入力します。

2. これにより、Microsoft Azure HDInsight クエリ コンソールが開きます。**[Hive エディター]** をクリックします。

	![Hive エディターを開く](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster2.png "Hive エディターを開く")

3. Hive エディターで次のクエリを入力し、**[送信]** をクリックします。

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

	この Hive クエリで、`adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder` の Data Lake Store に格納されているデータからテーブルを作成します。この場所には、前にアップロードしたサンプル データ ファイルが含まれます。

	下部にある **[ジョブ セッション]** テーブルに、ジョブの状態が **[初期化中]** から **[実行中]**、**[完了]** へと変化していくのが示されます。また、**[詳細の表示]** をクリックして、完了したジョブの詳細を確認することもできます。

	![テーブルを作成する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster3.png "テーブルを作成する")

4. 次のクエリを実行して、テーブルが作成されたことを確認します。

		SHOW TABLES;

	このクエリに対応する **[詳細の表示]** をクリックすると、出力が次のように表示されます。

		hivesampletable
		vehicles

	**vehicles** は、前に作成したテーブルです。**hivesampletable** は、既定ですべての HDInsight クラスターで使用できるサンプル テーブルです。

5. **vehicles** テーブルからデータを取得するクエリを実行することもできます。

		SELECT * FROM vehicles LIMIT 5;


## HDFS コマンドを使用して Data Lake Store にアクセスする

Data Lake Store を使用するように HDInsight クラスターを構成したら、HDFS シェル コマンドを使用してストアにアクセスできます。

### Linux クラスターの場合

このセクションでは、SSH をクラスターに入れて、HDFS コマンドを実行します。Windows ではビルトイン SSH クライアントは提供されません。[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) からダウンロードできる **PuTTY** を使用することをお勧めします。

PuTTY の使用については、「[HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)」をご覧ください。

接続されたら、次の HDFS ファイル システム コマンドを使用して、Data Lake Store 内のファイルを一覧表示します。

	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

これにより、以前に Data Lake Store にアップロードしたファイルが一覧表示されます。

	15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
	Found 1 items
	-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

`hdfs dfs -put` コマンドを使用して Data Lake Store にいくつかのファイルをアップロードし、`hdfs dfs -ls` を使用してファイルが正常にアップロードされたかどうかを確認することもできます。


### Windows クラスターの場合

1. 新しい [Azure ポータル](https://portal.azure.com)にサインオンします。

2. **[参照]**、**[HDInsight クラスター]** の順にクリックし、作成した HDInsight クラスターをクリックします。

3. クラスター ブレードで **[リモート デスクトップ]** をクリックし、**[リモート デスクトップ]** ブレードで **[接続]** をクリックします。

	![HDI クラスターにリモートから接続する](./media/data-lake-store-hdinsight-hadoop-use-portal/ADL.HDI.PS.Remote.Desktop.png "Azure リソース グループを作成する")

	メッセージが表示されたら、リモート デスクトップ ユーザーに対して指定した資格情報を入力します。

4. リモート セッションで、Windows PowerShell を起動し、HDFS ファイル システムのコマンドを使用して、Azure Data Lake Store のファイルを一覧表示します。

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	これにより、以前に Data Lake Store にアップロードしたファイルが一覧表示されます。

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

	`hdfs dfs -put` コマンドを使用して Data Lake Store にいくつかのファイルをアップロードし、`hdfs dfs -ls` を使用してファイルが正常にアップロードされたかどうかを確認することもできます。


## Storm トポロジで Data Lake Store を使用する

Data Lake Store を使用して、Storm トポロジからデータを書き込むことができます。このシナリオを実現する方法については、「[HDInsight で Apache Storm によって Azure Data Lake Store を使用する](../hdinsight/hdinsight-storm-write-data-lake-store.md)」を参照してください。

## HBase クラスターで Data Lake Store を使用する

HBase クラスターでは、Data Lake Store を既定のストレージとして、また追加のストレージとして使用できます。そのためには、次の操作を実行します。

1.  **[データ ソース]** ブレードの **[HBase データの場所]** で **[Data Lake Store]** を選択します。
2.  使用する Data Lake Store の名前を選択するか、または新しい Data Lake Store を作成します。
3.  最後に、Data Lake Store 内の **[HBase ルート フォルダー]** を指定します。Data Lake Store アカウントにルート フォルダーが含まれていない場合は、新規に作成してください。

	![Data Lake Store を使用した HBase](./media/data-lake-store-hdinsight-hadoop-use-portal/hbase-data-lake-store.png "Azure リソース グループを作成する")

### Data Lake Store を HBase クラスターの既定のストレージとして使用する場合の考慮事項

* 複数の HBase クラスターに対して同じ Data Lake Store アカウントを使用することができます。ただし、クラスターに指定する **[HBase ルート フォルダー]** は一意である必要があります (上の画面キャプチャの手順 4)。2 つの異なる HBase クラスターに同じルート フォルダーを使用**しないでください**。
* 既定のストレージとして Data Lake Store アカウントを使用したとしても、HBase クラスターのログ ファイルが保存されるのは、そのクラスターに関連付けられた Azure Storage BLOB (WASB) となります。上の画面キャプチャでは、青のボックスで強調表示されています。



## 関連項目

* [Azure PowerShell を使用して、Data Lake Store を使用する HDInsight クラスターをプロビジョニングする](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0316_2016-->