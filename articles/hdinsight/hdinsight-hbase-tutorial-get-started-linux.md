<properties
	pageTitle="HBase のチュートリアル: Hadoop での HBase の使用 | Microsoft Azure"
	description="HDInsight の Hadoop で Apache HBase を使用するには、この HBase チュートリアルの手順に従ってください。HBase シェルからテーブルを作成し、Hive を使用したクエリを実行します。"
	keywords="apache hbase,hbase,hbase shell,hbase tutorial"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/18/2015"
	ms.author="jgao"/>



# HBase チュートリアル: HDInsight の Hadoop で Apache HBase を使用する

HDInsight で HBase クラスターをプロビジョニングする方法、HBase テーブルを作成する方法、Hive を使用してテーブルを照会する方法について説明します。HBase の概要については、[HDInsight HBase の概要][hdinsight-hbase-overview]に関するページを参照してください。

> [AZURE.NOTE]このドキュメントの情報は、Linux ベースの HDInsight クラスターに固有のものです。Windows ベースのクラスターについては、HDInsight で Apache HBase と Hadoop を使用する方法の概要 (Windows) に関するページを参照してください。

##前提条件

この HBase のチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
- PuTTY と PuTTYGen (Windows クライアント)。これらのユーティリティは [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) から入手できます。
- [curl](http://curl.haxx.se/download.html)。

## HBase クラスターのプロビジョニング

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Azure プレビュー ポータルを使用して HBase クラスターをプロビジョニングするには**


1. [Azure プレビュー ポータル][azure-portal]にサインインします。
2. 左上の **[新規]** をクリックし、**[データ + 分析]**、**[HDInsight]** の順にクリックします。
3. 次の値を入力します。

	- **クラスター名**: このクラスターを識別するための名前を入力します
	- **クラスターの種類**: **[HBase]** を選択します
	- **のオペレーティング システム**: **[Ubuntu]** を選択します。
	- **サブスクリプション**: このクラスターをプロビジョニングするために使用する Azure サブスクリプションを選択します
	- **リソース グループ**: Azure リソース グループを追加するか選択します。詳細については、「[Azure リソース マネージャーの概要](resource-group-overview.md)」を参照してください。
	- **資格情報**。HTTP Web サービス ユーザーのパスワードを入力します。既定のユーザー名は **admin** です。さらに、SSH ユーザーを認証するために使用される **[SSH ユーザー名]** と、**[パスワード]** または **[公開キー]** のどちらかを入力する必要があります。公開キーを使用することをお勧めします。HDInsight での SSH の使用方法の詳細については、次の記事をご覧ください。

		- [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
		- [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md) **[選択]** をクリックして変更を保存します。
	- **データ ソース**: クラスターの既定のファイル システムとして使用する既存の Azure ストレージ アカウントを指定するか、新しく作成します。ストレージ アカウントの場所によってクラスターの場所が決まります。HDInsight クラスターと依存する Azure ストレージ アカウントは、同じデータ センター内にある必要があります。**既定のコンテナー**の既定の名前は、クラスター名です。  
	- **ノード価格レベル**: HBase クラスター用のリージョン サーバーの数を選択します。

		> [AZURE.WARNING]HBase サービスの高可用性には、最低 **3 つ** のノードを含むクラスターをプロビジョニングする必要があります。これで 1 つのノードがダウンしても、HBase データ領域は他のノードで利用できます。

	- **オプションの構成**: クラスターのバージョンの選択、Azure virtual network の構成、Hive/oozie メタストアの構成、スクリプト アクションの構成、および追加ストレージ アカウントの追加を行います。

4. **[作成]** をクリックします。

>[AZURE.NOTE]HBase クラスターを削除したら、同じ既定の BLOB コンテナーを使用して別の HBase クラスターを作成できます。新しいクラスターでは、元のクラスターで作成した HBase テーブルを選択します。

## HBase シェルの使用

多くの場合、データは次のような表形式で表示されます。

![hdinsight hbase tabular data][img-hbase-sample-data-tabular]

BigTable の実装である HBase では、同じデータが次のように表示されます。

![hdinsight hbase bigtable data][img-hbase-sample-data-bigtable]

次の手順を完了すると、この操作をよく理解できます。


**HBase シェルを使用するには**

>[AZURE.NOTE]ここで説明する手順は、Windows コンピューターの手順です。Linux、Unix、または OS X から Linux ベースの HDInsight クラスターに接続する手順については、「[Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する (プレビュー)](hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。1. **PuTTY** を開きます。記事の冒頭に掲載されている前提条件を参照してください。2.プロビジョニング プロセスのユーザー アカウントの作成時に SSH キーを指定した場合は、次の手順に従って、クラスターへの認証に使用するプライベート キーを選択する必要があります。

	In **Category**, expand **Connection**, expand **SSH**, and select **Auth**. Finally, click **Browse** and select the .ppk file that contains your private key.

3. **[Category]** で、**[Session]** をクリックします。
4. PuTTY セッション画面の基本オプションで、次の値を入力します。

	- ホスト名: [ホスト名 (または IP アドレス)] フィールドの HDInsight サーバーの SSH アドレス。SSH アドレスは、クラスター名に続けて「**-ssh.azurehdinsight.net**」と入力します (*mycluster-ssh.azurehdinsight.net* など)
	- ポート: 22。headnode0 の SSH ポートは 22 です。「[Linux での HDInsight の使用方法 (プレビュー)](hdinsight-hadoop-linux-information.md#remote-access-to-services)」を参照してください。
4. **[開く]** をクリックして、クラスターに接続します。
5. プロンプトが表示されたら、クラスターの作成時に入力したユーザーを入力します。ユーザーにパスワードを指定している場合は、その入力も求められます。
6. 次のコマンドを実行します。

		hbase shell

4. 2 つの列ファミリを持つ HBase を作成します。

		create 'Contacts', 'Personal', 'Office'
		list
5. いくつかのデータを挿入します。

		put 'Contacts', '1000', 'Personal:Name', 'John Dole'
		put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
		put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
		put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
		scan 'Contacts'

	![hdinsight hadoop hbase shell][img-hbase-shell]

6. 1 つの行を取得します。

		get 'Contacts', '1000'

	行は 1 行のみのため、スキャン コマンドを使用した場合と同じ結果が得られます。

	Hbase テーブル スキーマの詳細については、「[Introduction to HBase Schema Design (HBase スキーマの設計の概要)][hbase-schema]」を参照してください。HBase コマンドについての詳細は、「[Apache HBase reference guide (Apache HBase リファレンス ガイド)][hbase-quick-start]」をご覧ください。


6. シェルを終了します。

		exit

**Contacts HBase テーブルにデータを一括で読み込むには**

HBase では、いくつかの方法でテーブルにデータを読み込ことができます。詳細については、[一括読み込み](http://hbase.apache.org/book.html#arch.bulk.load)に関するページを参照してください。


サンプルのデータ ファイルがパブリック BLOB コンテナー wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt にアップロードされています。このデータ ファイルの内容は次のとおりです。

	8396	Calvin Raji		230-555-0191	230-555-0191	5415 San Gabriel Dr.
	16600	Karen Wu		646-555-0113	230-555-0192	9265 La Paz
	4324	Karl Xie		508-555-0163	230-555-0193	4912 La Vuelta
	16891	Jonn Jackson	674-555-0110	230-555-0194	40 Ellis St.
	3273	Miguel Miller	397-555-0155	230-555-0195	6696 Anchor Drive
	3588	Osa Agbonile	592-555-0152	230-555-0196	1873 Lion Circle
	10272	Julia Lee		870-555-0110	230-555-0197	3148 Rose Street
	4868	Jose Hayes		599-555-0171	230-555-0198	793 Crawford Street
	4761	Caleb Alexander	670-555-0141	230-555-0199	4775 Kentucky Dr.
	16443	Terry Chander	998-555-0171	230-555-0200	771 Northridge Drive

必要に応じて、自分でテキスト ファイルを作成し、そのファイルを自分のストレージ アカウントにアップロードできます。手順については、「[HDInsight での Hadoop ジョブ用データのアップロード][hdinsight-upload-data]」をご覧ください。

> [AZURE.NOTE]この手順では、前回の手順で作成した Contacts HBase テーブルを使用します。

1. **PuTTY** を開き、クラスターに接続します。前の手順の指示を参照してください。
3. 次のコマンドを実行して、データ ファイルをストア ファイルに変換し、Dimporttsv.bulk.output で指定された相対パスに格納します。

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. 次のコマンドを実行して、/example/data/storeDataFileOutput から HBase テーブルにデータをアップロードします。

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. HBase シェルを開き、スキャン コマンドを使用して、テーブルの内容の一覧を表示することができます。



## Hive を使用して HBase テーブルを照会する

Hive を使用して HBase テーブルのデータを照会できます。このセクションでは、HBase テーブルにマッピングする Hive テーブルを作成し、作成した Hive テーブルを使用して HBase テーブルのデータを照会します。


1. **PuTTY** を開き、クラスターに接続します。前の手順の指示を参照してください。
2. Hive シェルを開きます。

	hive
3. 次の HiveQL スクリプトを使用して、HBase テーブルにマッピングされる Hive テーブルを作成します。ここで、HBase シェルを使用して、先ほど参照したサンプル テーブルが HBase に作成されたことを確認してから、このステートメントを実行してください。

		CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. 次の HiveQL スクリプトを実行します。次のように Hive クエリが HBase テーブル内のデータを照会します。

     	SELECT count(*) FROM hbasecontacts;

## Curl を使用して HBase REST API を使用する

> [AZURE.NOTE]Curl、または WebHCat を使用したその他の REST 通信を使用する場合は、HDInsight クラスター管理者のユーザー名とパスワードを指定して要求を認証する必要があります。また、サーバーへの要求の送信に使用する Uniform Resource Identifier (URI) にクラスター名を含める必要があります。
>
> このセクションのコマンドでは、**USERNAME** をクラスターを認証するユーザーの名前に、**PASSWORD** をユーザー アカウントのパスワードに置き換えてください。**CLUSTERNAME** はクラスターの名前に置き換えます。
>
> REST API のセキュリティは、[基本認証](http://en.wikipedia.org/wiki/Basic_access_authentication)を通じて保護されています。資格情報をサーバーに安全に送信するには、必ずセキュア HTTP (HTTPS) を使用して要求を行う必要があります。

1. コマンド ラインで次のコマンドを使用して、HDInsight クラスターに接続できることを確認します。

		curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

	次のような応答を受け取ります。

    {"status":"ok","version":"v1"}

  このコマンドで使用されるパラメーターの意味は次のとおりです。

    * **-u** - The user name and password used to authenticate the request.
    * **-G** - Indicates that this is a GET request.

2. 次のコマンドを使用して、既存の HBase テーブルを一覧表示します。

		curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. 次のコマンドを使用して、2 つの列ファミリがある新しい HBase テーブルを作成します。

		curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{"@name":"test","ColumnSchema":[{"name":"Personal"},{"name":"Office"}]}"

	スキーマは、JSon 形式で提供されます。

4. 次のコマンドを使用して、一部のデータを挿入します。

		curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{"Row":{"key":"1000","Cell":{"column":"Personal:Name", "$":"John Dole"}}}"

5. 次のコマンドを使用して、1 行を取得します。

		curl -u <UserName>:<Password> -v -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" -H "Accept: application/json"

## クラスターの状態の確認

HDInsight の HBase には、クラスターを監視するための Web UI が付属します。この Web UI を使用すると、統計情報やリージョンに関する情報を要求できます。

SSH を使用して、Web 要求などのローカルの要求を HDInsight クラスターにトンネリングすることもできます。ここでは、最初から HDInsight クラスター ヘッド ノード上にあったかのように、要求が要求済みリソースにルーティングされます。詳細については、「[HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する (プレビュー)](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel)」を参照してください。

**SSH トンネリング セッションを確立するには**

1. **PuTTY** を開きます。  
2. プロビジョニング プロセスのユーザー アカウントの作成時に SSH キーを指定した場合は、次の手順に従って、クラスターへの認証に使用するプライベート キーを選択する必要があります。

	**[カテゴリ]** で **[接続]**、**[SSH]** の順に展開し、**[認証]** を選択します。最後に、**[参照]** をクリックし、プライベート キーが含まれた .ppk ファイルを選択します。

3. **[Category]** で、**[Session]** をクリックします。
4. PuTTY セッション画面の基本オプションで、次の値を入力します。

	- **ホスト名**: [ホスト名 (または IP アドレス)] フィールドの HDInsight サーバーの SSH アドレス。SSH アドレスは、クラスター名に続けて「**-ssh.azurehdinsight.net**」と入力します (*mycluster-ssh.azurehdinsight.net* など)。
	- **ポート**: 22。headnode0 の SSH ポートは 22 です。  
5. ダイアログの左にある **[カテゴリ]** セクションで、**[接続]**、**[SSH]** の順に展開し、**[トンネル]** をクリックします。
6. [SSH ポートの転送を管理するオプション] フォームに次の情報を入力します。

	- **[ソース ポート]** - 転送するクライアント上のポート(9876 など)。
	- **[動的]** - 動的な SOCKS プロキシを有効にします。
7. **[追加]** をクリックして設定を追加します。
8. ダイアログの下部にある **[開く]** をクリックして SSH 接続を開きます。
9. メッセージが表示されたら、SSH アカウントを使用してサーバーにログインします。これにより、SSH セッションが確立され、トンネルが有効になります。

**Ambari を使用して Zookeeper の FQDN を確認するには**

1. https://<ClusterName>.azurehdinsight.net/ にアクセスします。
2. クラスター ユーザー アカウントの資格情報を 2 回入力します。
3. 左側のメニューにある **[Zookeeper]** をクリックします。
4. [概要] リストにある 3 つの **[Zookeeper サーバー]** リンクのいずれかをクリックします。
5. **[ホスト名]** をコピーします。たとえば、zookeepernode0.zookeepernode-0-xxxxxxxxxxxxxxxxxxxx.c7.internal.cloudapp.net をコピーします。

** プログラム (Fireｆox) を構成してクラスターの状態を確認するには**

1. FireFox を開きます。
2. **[メニューを開く]** ボタンをクリックします。
3. **[オプション]** をクリックします。
4. **[詳細]**、**[ネットワーク]**、**[インターネット接続]** の順にクリックします。
5. **[手動でプロキシを設定する]** を選択します。
6. 次の値を入力します。

	- **SOCKS ホスト**: localhost
	- **ポート**: Putty SSH トンネリングで構成したものと同じポートを使用します(9876 など)。
	- **SOCKS v5**: (オンにします)
	- **リモート DNS**: (オンにします)
7. **[OK]** をクリックして変更を保存します。
8. http://<TheFQDN of a ZooKeeper>:60010/master-status にアクセスします。

高可用性クラスターの場合は、Web UI をホストしている現在アクティブな HBase マスター ノードへのリンクがあります。



## 次の手順
この HDInsight の HBase のチュートリアルでは、HBase クラスターのプロビジョニング方法、テーブルの作成方法、作成したテーブルのデータを HBase シェルから表示する方法について学習しました。また、Hive を使用して HBase テーブルのデータを照会する方法、HBase C# REST API を使用して HBase テーブルを作成し、テーブルからデータを取得する方法についても学習しました。

詳細については、次を参照してください。

- 「[HDInsight HBase の概要][hdinsight-hbase-overview]」: HBase は、Hadoop 上に構築された Apache オープン ソースの NoSQL データベースです。大量の非構造化データおよび半構造化データに対するランダム アクセスと強力な一貫性を実現します。


[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png

<!---HONumber=Oct15_HO3-->