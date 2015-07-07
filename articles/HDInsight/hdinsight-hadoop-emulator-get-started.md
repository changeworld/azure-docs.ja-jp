<properties
	pageTitle="HDInsight 用の Hadoop Emulator の概要 |Microsoft Azure"
	description="MapReduce チュートリアルでインストールされるエミュレーターおよび他のサンプルを使用して、Hadoop エコシステムについて学びます。HDInsight Emulator は、Hadoop サンドボックスに似た動作をします。"
	keywords="emulator,hadoop ecosystem,hadoop sandbox,mapreduce tutorial"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	authors="nitinme"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="05/07/2015"
	ms.author="nitinme"/>

# HDInsight Emulator (Hadoop サンドボックス) を使用した Hadoop エコシステム入門

このチュートリアルでは、Microsoft HDInsight Emulator for Azure (旧 HDInsight サーバー開発者プレビュー) における Hadoop クラスターの概要を紹介します。HDInsight Emulator には、Azure の HDInsight と同じ Hadoop エコシステムのコンポーネントが付属します。デプロイされたバージョンの情報も含めて、詳細については、「[Azure HDInsight でサポートされている Hadoop のバージョン](hdinsight-component-versioning.md)」を参照してください。

エミュレーターがインストールされたら、ワード カウントの MapReduce のチュートリアルに従い、サンプルを実行します。

> [AZURE.NOTE]HDInsight Emulator には Hadoop クラスターのみが含まれます。HBase や Storm は含まれません。


HDInsight Emulator は、Hadoop サンドボックスによく似たローカル開発環境を提供します。Hadoop に慣れている場合は、Hadoop 分散ファイル システム (HDFS) を使用して HDInsight Emulator を使い始めることができます。HDInsight では、既定のファイル システムが Azure BLOB ストレージです。そのため、最終的には Azure BLOB ストレージを使用してジョブを開発することになります。Azure BLOB ストレージを HDInsight Emulator で使用するには、エミュレーターの構成を変更する必要があります。

> [AZURE.NOTE]HDInsight Emulator では 1 つのノードでの展開のみが使用できます。


## 前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- HDInsight Emulator には 64 ビット版 Windows が必要です。次の要件のうち 1 つを満たしている必要があります。

	- Windows 7 Service Pack 1
	- Windows Server 2008 R2 Service Pack 1
	- Windows 8
	- Windows Server 2012

- **Azure PowerShell**[Azure PowerShell のインストールおよび使用](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)に関するページを参照してください。


##<a name="install"></a>HDInsight Emulator のインストール

Microsoft HDInsight Emulator は、Microsoft Web Platform Installer を使用してインストールします。

> [AZURE.NOTE]現時点で HDInsight Emulator がサポートしているのは英語版 OS のみです。エミュレーターの以前のバージョンがインストールされている場合は、エミュレーターの最新バージョンをインストールする前に、次の 2 つのコンポーネントをコントロール パネルの [プログラムと機能] からアンインストールする必要があります。<ul> <li>Microsoft HDInsight Emulator for Azure または HDInsight 開発者プレビュー (インストールされているどちらかのコンポーネント)</li> <li>Hortonworks Data Platform</li> </ul>


**HDInsight Emulator をインストールするには**

1. Internet Explorer を開き、[Microsoft HDInsight Emulator for Azure インストール ページ][hdinsight-emulator-install]にアクセスします。
2. **[今すぐインストール]** をクリックします。
3. ページ下部に HDINSIGHT.exe のインストールに関するメッセージが表示されたら、**[実行]** をクリックします。
4. **[ユーザー アカウント制御]** ウィンドウで **[はい]** をクリックして、インストールを完了します。Web Platform Installer のウィンドウが表示されます。
6. ページの下部にある **[インストール]** をクリックします。
7. **[同意する]** をクリックして、ライセンス条項に同意します。
8. Web Platform Installer に **[次の製品が正常にインストールされました]** と表示されるのを確認して、**[完了]** をクリックします。
9. **[終了]** をクリックして Web Platform Installer のウィンドウを閉じます。

**HDInsight Emulator のインストールを確認するには**

インストールが終わると、デスクトップにアイコンが 3 つインストールされます。3 つのアイコンは次のようにリンクされています。

- **Hadoop Command Line** - HDInsight Emulator で MapReduce、Pig、Hive のジョブを実行する Hadoop コマンド プロンプト。

- **Hadoop NameNode Status** - NameNode は HDFS のすべてのファイルを含むツリー式ディレクトリを維持します。また、Hadoop クラスターに保持されたすべてのファイルのデータの場所も追跡されます。クライアントは、すべてのファイルのデータ ノードがどこに格納されているか見つけるために、NameNode と通信します。

- **Hadoop Yarn のステータス** - クラスターのノードに MapReduce タスクを配分するジョブ トラッカー。

インストールが終わると、いくつかのローカル サービスもインストールされます。[サービス] ウィンドウのスクリーンショットは次のようになります。

![エミュレーター ウィンドウに一覧表示された Hadoop エコシステムのサービス。][image-hdi-emulator-services]

HDInsight Emulator に関連するサービスは既定で自動的に開始されません。サービスを開始するには、Hadoop コマンド ラインで、C:\hdp の **start_local_hdp_services.cmd** (既定の場所) を実行します。コンピューターの再起動後にサービスが自動的に開始されるようにするには、**set-onebox-autostart.cmd** を実行します。

HDInsight Emulator のインストールと実行に関する既知の問題については、「[HDInsight Emulator のリリース ノート](hdinsight-emulator-release-notes.md)」をご覧ください。インストール ログは **C:\HadoopFeaturePackSetup\HadoopFeaturePackSetupTools\gettingStarted.winpkg.install.log** にあります。

##<a name="vstools"></a>Visual Studio の HDInsight ツールでのエミュレーターの使用

Visual Studio の HDInsight ツールを使用して、HDInsight Emulator に接続できます。Azure の HDInsight クラスターで Visual Studio ツールを使用する方法については、「[HDInsight Hadoop Tools for Visual Studio の使用開始](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)」をご覧ください。

### エミュレーター用の HDInsight ツールのインストール

HDInsight Visual Studio ツールのインストール方法については、[こちら](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md#installation)をご覧ください。

### HDInsight Emulator への接続

1. Visual Studio を開きます。
2. **[ビュー]** メニューで、**[サーバー エクスプローラー]** をクリックして、サーバー エクスプローラー ウィンドウを開きます。
3. **[Azure]** を展開し、**[HDInsight]** を右クリックして、**[Connect to HDInsight Emulator]** をクリックします。

	 ![Visual Studio ビュー:　メニューの [Connect to HDInsight emulator]。](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.connect.vs.png)

4. [Connect to HDInsight Emulator] ダイアログ ボックスで、WebHCat、HiveServer2、WebHDFS のエンドポイントの値を確認し、**[次へ]** をクリックします。エミュレーターの既定の構成を変更しなかった場合は、既定値が有効になります。変更を加えた場合は、ダイアログ ボックスの値を更新し、[次へ] をクリックします。

	![設定されている [Connect to HDInsight emulator] ダイアログ ボックス。](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.connect.vs.dialog.png)

5. 接続が正常に確立されたら、**[完了]** をクリックします。サーバー エクスプローラーに HDInsight Emulator が表示されるようになります。

	![接続されている HDInsight ローカル エミュレーター (Hadoop サンド ボックス) が表示されているサーバー エクスプローラー。](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.vs.connected.png)

接続が正常に確立されたら、Azure HDInsight クラスターで使用するのと同じようにエミュレーターで HDInsight VS ツールを使用できます。Azure HDInsight クラスターで VS ツールを使用する方法の詳細については、「[HDInsight Hadoop Tools for Visual Studio の使用開始](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)」に関するページをご覧ください。

## トラブルシューティング: HDInsight Emulator への HDInsight ツールの接続

1. HDInsight Emulator の接続中に、ダイアログ ボックスに HiveServer2 の接続成功が表示された場合でも、C:\hdp\hive-*version*\conf\hive-site.xml にある Hive 構成ファイルの **hive.security.authorization.enabled プロパティ**を手動で **false** に設定し、ローカル エミュレーターを再起動する必要があります。HDInsight Tools for Visual Studio は、テーブルの上位 100 行をプレビューしている場合のみ、HiveServer2 に接続します。このようなクエリを使用しない場合は、Hive 構成をそのままにすることができます。

2. HDInsight Emulator を実行するコンピュータで動的 IP の割り当て (DHCP) を使用している場合は、C:\hdp\hadoop-*version*\etc\hadoop\core-site.xml を更新し、**hadoop.proxyuser.hadoop.hosts** プロパティの値を (*) に変更する必要がある場合があります。これにより、Hadoop ユーザーは、すべてのホストから接続して Visual Studio で入力されたユーザーに偽装することができます。

		<property>
			<name>hadoop.proxyuser.hadoop.hosts</name>
			<value>*</value>
		</property>

3. Visual Studio が WebHCat サービスに接続しようとすると、エラーが発生する可能性があります ("エラー": “ジョブ job_XXXX_0001 が見つかりませんでした”)。この場合は、WebHCat サービスを再起動してもう一度実行してください。WebHCat サービスを再起動するには、**サービス** MMC を起動し、**[Apache Hadoop Templeton]** (これは WebHCat サービスの前の名前です) を右クリックして、**[再起動]** をクリックします。

##<a name="runwordcount"></a>ワード カウント MapReduce のチュートリアル

これで、ワークステーション上に HDInsight Emulator が構成されました。インストールをテストするために MapReduce のチュートリアルを試してみましょう。まず、HDFS にデータ ファイルをいくつかアップロードした後、それらのファイルに特定の単語が出現する回数を数えるワード カウント MapReduce ジョブを実行します。

ワード カウント MapReduce プログラムは *hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar* にパッケージ化されています。jar ファイルは *C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce* フォルダーにあります。

MapReduce ジョブは、次の 2 つの引数を受け取ります。

- 入力フォルダー。*hdfs://localhost/user/HDIUser* を入力フォルダーとして使用します。- 出力フォルダー。*hdfs://localhost/user/HDIUser/WordCount_Output* を出力フォルダーとして使用します。出力フォルダーは既存のフォルダーにすることはできません。既存のフォルダーの場合、MapReduce ジョブは失敗します。2 回目に MapReduce ジョブを実行する場合は、別の出力フォルダーを指定するか、既存の出力フォルダーを削除します。
**ワード カウント MapReduce ジョブを実行するには**

1. デスクトップで **[Hadoop Command Line]** をダブルクリックして、Hadoop コマンド ライン ウィンドウを開きます。現在のフォルダーは次のようになっているはずです。

		c:\hdp\hadoop-2.4.0.2.1.3.0-1981

	そうでない場合は、次のコマンドを実行します。

		cd %hadoop_home%

2. 次の Hadoop コマンドを実行して、入力ファイルと出力ファイルを格納する HDFS フォルダーを作成します。

		hadoop fs -mkdir /user
		hadoop fs -mkdir /user/HDIUser

3. 次の Hadoop コマンドを実行して、いくつかのローカル テキスト ファイルを HDFS にコピーします。

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common*.txt /user/HDIUser

4. 次のコマンドを実行して、/user/HDIUser フォルダーにあるファイルを一覧表示します。

		hadoop fs -ls /user/HDIUser

	次のようにファイルが表示されます。

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -ls /user/HDIUser
		Found 4 items
		-rw-r--r--   1 username hdfs     574261 2014-09-08 12:56 /user/HDIUser/CHANGES.txt
		-rw-r--r--   1 username hdfs      15748 2014-09-08 12:56 /user/HDIUser/LICENSE.txt
		-rw-r--r--   1 username hdfs        103 2014-09-08 12:56 /user/HDIUser/NOTICE.txt
		-rw-r--r--   1 username hdfs       1397 2014-09-08 12:56 /user/HDIUser/README.txt

5. 次のコマンドを実行して、ワード カウント MapReduce ジョブを実行します。

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop jar C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6. 次のコマンドを実行して、出力ファイルから "windows" を含む単語の個数を表示します。

		hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

	出力は次のようになります。

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"
		windows 4
		windows.        2
		windows/cygwin. 1

Hadoop コマンドの詳細については、[Hadoop コマンド マニュアル][hadoop-commands-manual]を参照してください。

##<a name="rungetstartedsamples"></a> サンプル Web ログ データの分析

HDInsight Emulator をインストールすると、Windows 上の Apache Hadoop ベースのサービスについてユーザーが学習できるサンプルもインストールされます。これらのサンプルは、通常、ビッグ データ セットを処理する際に必要なタスクを対象としています。上記 MapReduce のチュートリアルでビルドされたサンプルを試すと、MapReduce プログラミング モデルとそのエコシステムに慣れることができます。

サンプル データは、処理する IIS World Wide Web Consortium (W3C) ログ データを軸として整理されています。データ生成ツールが用意されていて、さまざまなサイズのデータ セットを作成して HDFS や Azure BLOB ストレージにインポートできます(詳細については、「[HDInsight での Azure BLOB ストレージの使用](../hdinsight-use-blob-storage.md)」に関するページをご覧ください)。その後、Azure PowerShell スクリプトによって生成されたデータのページ上で MapReduce、Pig、Hive ジョブを実行できます。Pig スクリプトと Hive スクリプトは MapReduce 上の抽象化レイヤーであり、最終的に MapReduce プログラムにコンパイルされます。一連のジョブを実行して、これらの異なる技術を使用する効果と、処理タスクの実行にデータ サイズが及ぼす影響を自分の目で確認することができます。

### このセクションの内容

- [IIS W3C ログ データのシナリオ](#scenarios)
- [サンプル W3C ログ データの読み込み](#loaddata)
- [Java MapReduce ジョブの実行](#javamapreduce)
- [Hive ジョブの実行](#hive)
- [Pig ジョブの実行](#pig)
- [サンプルのリビルド](#rebuild)

###<a name="scenarios"></a>IIS W3C ログ データのシナリオ

W3C シナリオでは、IIS W3C ログ データを、1 MB (小)、500 MB (中)、2 GB (大) の 3 つのサイズで生成し、HDFS または Azure BLOB ストレージにインポートします。3 種類のジョブがあり、それぞれを C#、Java、Pig、および Hive で実装します。

- **totalhits** - 所定のページに対する要求の総数を計算します。
- **avgtime** - ページあたりの要求にかかかった平均時間 (秒単位) を計算します。
- **errors** - 状態が 404 または 500 であった要求について、ページあたり、時間あたりのエラー数を計算します。

これらのサンプルとそのドキュメントは、主要な Hadoop テクノロジを詳細に調査したり全面的に実装したりするものではありません。使用するクラスターはノードを 1 つしかもたないため、ノードを増やす効果は、今回のリリースでは確認できません。

###<a name="loaddata"></a>サンプル W3C ログ データの読み込み

Azure PowerShell スクリプトの importdata.ps1 を使用して、データを生成し HDFS にインポートします。

**サンプル W3C ログ データをインポートするには**

1. デスクトップから Hadoop コマンド ラインを開きます。
2. ディレクトリを **C:\hdp\GettingStarted** に変更します。
3. 次のコマンドを実行して、データを生成し HDFS にインポートします。

		powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted

	代わりに Azure BLOB ストレージにデータを読み込む場合は、[Azure BLOB ストレージへの接続](#blobstorage)をご覧ください。

4. Hadoop コマンド ラインから次のコマンドを実行して、HDFS にインポートしたファイルの一覧を表示します。

		hadoop fs -ls -R /w3c

	出力は次のようになります。

		C:\hdp\GettingStarted>hadoop fs -ls -R /w3c
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:41 /w3c/input/large
		-rw-r--r--   1 username hdfs  543683503 2014-09-08 15:41 /w3c/input/large/data_w3c_large.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input/medium
		-rw-r--r--   1 username hdfs  272435159 2014-09-08 15:40 /w3c/input/medium/data_w3c_medium.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:39 /w3c/input/small
		-rw-r--r--   1 username hdfs    1058423 2014-09-08 15:39 /w3c/input/small/data_w3c_small.txt

5. ファイルの内容を確認するには、次のコマンドを実行して、1 つのデータ ファイルの内容をコンソール ウィンドウに表示します。

		hadoop fs -cat /w3c/input/small/data_w3c_small.txt

これでデータ ファイルが作成されて HDFS にインポートされました。別の Hadoop ジョブの実行を開始できます。

###<a name="javamapreduce"></a>Java MapReduce ジョブの実行

MapReduce は Hadoop の基本的計算エンジンです。既定で Java で実装されていますが、C# を使用する .NET および Hadoop Streaming を活用した例もあります。MapReduce ジョブを実行する構文は次のとおりです。

	hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

jar ファイルとソース ファイルは C:\Hadoop\GettingStarted\Java フォルダーにあります。

**Web ページのヒット数を計算する MapReduce ジョブを実行するには**

1. Hadoop コマンド ラインを開きます。
2. ディレクトリを **C:\hdp\GettingStarted** に変更します。
3. 次のコマンドを実行して、出力ディレクトリが存在している場合は削除します。出力ディレクトリが既に存在していると、MapReduce ジョブは失敗します。

		hadoop fs -rm -r /w3c/output

3. 次のコマンドを実行します。

		hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

	コマンドの要素を次の表に示します。<table border="1"> <tr><td>パラメーター</td><td>注</td></tr> <tr><td>w3c_scenarios.jar</td><td>この jar ファイルは C:\hdp\GettingStarted\Java フォルダーにあります。</td></tr> <tr><td>microsoft.hadoop.w3c.TotalHitsForPage</td><td>この型は、次のどちらかに置き換えることができます。<ul> <li>microsoft.hadoop.w3c.AverageTimeTaken</li> <li>microsoft.hadoop.w3c.ErrorsByPage</li> </ul></td></tr> <tr><td>/w3c/input/small/data_w3c_small.txt</td><td>この入力ファイルは、次のどちらかに置き換えることができます。<ul> <li>/w3c/input/medium/data_w3c_medium.txt</li> <li>/w3c/input/large/data_w3c_large.txt</li> </ul></td></tr> <tr><td>/w3c/output</td><td>これは、出力フォルダー名です。</td></tr> </table>

4. 次のコマンドを実行して、出力ファイルの内容を表示します。

		hadoop fs -cat /w3c/output/part-00000

	次のように出力されます。

		c:\Hadoop\GettingStarted>hadoop fs -cat /w3c/output/part-00000
		/Default.aspx   3380
		/Info.aspx      1135
		/UserService    1126

	たとえば Default.aspx ページは 3360 ヒットということがわかります。上の表に示されている値を入れ替えてコマンドをもう一度実行します。ジョブの種類とデータのサイズに応じて出力が変わることに注目してください。

### <a name="hive"></a>Hive ジョブの実行
Hive クエリ エンジンは、構造化照会言語 (SQL) の達人であれば戸惑うことはありません。SQL と同様のインターフェイスと HDFS のリレーショナル データ モデルを提供します。Hive では、SQL によく似た HiveQL と呼ばれる言語を使用します。Hive は、Java ベースの MapReduce フレームワーク上に抽象化レイヤーを提供します。Hive クエリは、実行時に MapReduce にコンパイルされます。

**Hive ジョブを実行するには**

1. Hadoop コマンド ラインを開きます。
2. ディレクトリを **C:\hdp\GettingStarted** に変更します。
3. 次のコマンドを実行して、**/w3c/hive/input** フォルダーが存在している場合は削除します。このフォルダーが存在していると、Hive ジョブは失敗します。

		hadoop fs -rmr /w3c/hive/input

4. 次のコマンドを実行して、**/w3c/hive/input** フォルダーを作成し、データ ファイルを /hive/input フォルダーにコピーします。

        hadoop fs -mkdir /w3c/hive
		hadoop fs -mkdir /w3c/hive/input

		hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5. 次のコマンドを実行して、**w3ccreate.hql** スクリプト ファイルを実行します。このスクリプトは Hive テーブルを作成し、データを Hive テーブルに読み込みます。

	> [AZURE.NOTE]この段階で、HDInsight Visual Studio ツールを使用して Hive クエリを実行することもできます。Visual Studio を開いて新しいプロジェクトを作成し、HDInsight テンプレートから **[Hive アプリケーション]** を選択します。プロジェクトが開いたら、クエリを新しいアイテムとして追加します。クエリは **C:/hdp/GettingStarted/Hive/w3c** にあります。クエリがプロジェクトに追加されたら、**${hiveconf:input}** を **/w3c/hive/input** に置き換え、**[送信]** を押します。

		C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

	次のように出力されます。

		Logging initialized using configuration in file:/C:/hdp/hive-0.13.0.2.1.3.0-1981	/conf/hive-log4j.properties
		OK
		Time taken: 1.137 seconds
		OK
		Time taken: 4.403 seconds
		Loading data to table default.w3c
		Moved: 'hdfs://HDINSIGHT02:8020/hive/warehouse/w3c' to trash at: hdfs://HDINSIGHT02:8020/user/<username>/.Trash/Current
		Table default.w3c stats: [numFiles=1, numRows=0, totalSize=1058423, rawDataSize=0]
		OK
		Time taken: 2.881 seconds

6. 次のコマンドを実行して、**w3ctotalhitsbypage.hql** HiveQL スクリプト ファイルを実行します。

	> [AZURE.NOTE]前に説明したように、HDInsight Visual Studio ツールを使用してこのクエリを実行することもできます。

        C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

	コマンドの要素を次の表に示します。<table border="1"> <tr><td>ファイル</td><td>説明</td></tr> <tr><td>C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd</td><td>Hive コマンド スクリプト。</td></tr> <tr><td>C:\hdp\GettingStarted\Hive\w3c\w3ctotalhitsbypage.hql</td><td> この Hive スクリプト ファイルは、次のいずれかに置き換えることができます。<ul> <li>C:\hdp\GettingStarted\Hive\w3c\w3caveragetimetaken.hql</li> <li>C:\hdp\GettingStarted\Hive\w3c\w3cerrorsbypage.hql</li> </ul> </td></tr>

	</table>

	w3ctotalhitsbypage.hql HiveQL スクリプトの内容は次のとおりです。

		SELECT filtered.cs_uri_stem,COUNT(*)
		FROM (
		  SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
		) filtered
		GROUP BY (filtered.cs_uri_stem);

	出力の最後は次のようになります。

		MapReduce Total cumulative CPU time: 5 seconds 391 msec
		Ended Job = job_1410201800143_0008
		MapReduce Jobs Launched:
		Job 0: Map: 1  Reduce: 1   Cumulative CPU: 5.391 sec   HDFS Read: 1058638 HDFS Write: 53 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 391 msec
		OK
		/Default.aspx   3380
		/Info.aspx      1135
		/UserService    1126
		Time taken: 49.304 seconds, Fetched: 3 row(s)

各ジョブの最初の手順として、テーブルが作成され、先に作成したファイルからそのテーブルにデータが読み込まれています。作成されたファイルは、次のコマンドを使用して HDFS の /Hive ノードの下を探すと見つかります。

	hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Pig ジョブの実行

Pig 処理では、*Pig Latin* と呼ばれるデータ フロー言語が使用されます。Pig Latin 抽象化は MapReduce より高度なデータ構造を提供し、SQL でリレーショナル データベース管理システムを操作する場合と同じように Hadoop を操作できます。


**Pig ジョブを実行するには**

1. Hadoop コマンド ラインを開きます。
2. ディレクトリを **C:\hdp\GettingStarted** フォルダーに変更します。
3. 次のコマンドを実行して、Pig ジョブを送信します。

		C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

	コマンドの要素を次の表に示します。<table border="1"> <tr><td>ファイル</td><td>説明</td></tr> <tr><td>C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd</td><td>Pig コマンド スクリプト。</td></tr> <tr><td>C:\hdp\GettingStarted\Pig\w3c\TotalHitsForPage.pig</td><td>この Pig Latin スクリプト ファイルは、次のいずれかに置き換えることができます。<ul> <li>C:\hdp\GettingStarted\Pig\w3c\AverageTimeTaken.pig</li> <li>C:\hdp\GettingStarted\Pig\w3c\ErrorsByPage.pig</li> </ul> </td></tr> <tr><td>/w3c/input/small/data_w3c_small.txt</td><td> このパラメーターは次のようなもっと大きなファイルに置き換えることができます。

	<ul>
<li>/w3c/input/medium/data_w3c_medium.txt</li>
<li>/w3c/input/large/data_w3c_large.txt</li>
</ul></td></tr> </table>

	出力は次のようになります。

		(/Info.aspx,1135)
		(/UserService,1126)
		(/Default.aspx,3380)

Pig スクリプトはコンパイルすると MapReduce ジョブになり、その際、複数のジョブが生成されることがあります。そのため、Pig ジョブの実行中に複数の MapReduce ジョブが実行されることがあります。

<!---
### <a name="rebuild"></a>Rebuild the samples
The samples currently contain all the required binaries, so building is not required. If you'd like to make changes to the Java or .NET samples, you can rebuild them by using either the Microsoft Build Engine (MSBuild) or the included Azure PowerShell script.


**To rebuild the samples**

1. Open a Hadoop command line.
2. Run the following command:

		powershell -F buildsamples.ps1
--->

##<a name="blobstorage"></a>Azure BLOB ストレージへの接続
HDInsight Emulator は、既定のファイル システムとして HDFS を使用します。ただし、Azure HDInsight は、既定のファイル システムとして Azure BLOB ストレージを使用します。ローカル ストレージの代わりに Azure BLOB ストレージを使用するように HDInsight Emulator を構成できます。次の手順に従って Azure にストレージ コンテナーを作成し、それを HDInsight Emulator に接続します。

>[AZURE.NOTE]HDInsight で Azure BLOB ストレージを使用する方法の詳細については、[「HDInsight での Azure BLOB ストレージの使用」](../hdinsight-use-blob-storage.md)に関するページをご覧ください。

次の手順を開始する前に、ストレージ アカウントを作成している必要があります。詳しい手順については、「[ストレージ アカウントの作成方法](../storage-create-storage-account.md)」を参照してください。

**コンテナーを作成するには**

1. [Azure ポータル][azure-management-portal]にサインインします。
2. 左側の **[ストレージ]** をクリックします。サブスクリプションの下にストレージ アカウントの一覧が表示されます。
3. コンテナーを作成するストレージ アカウントを一覧からクリックします。
4. ページの上部にある **[コンテナー]** をクリックします。
5. ページの下部にある **[追加]** をクリックします。
6. **[名前]** に値を入力して、**[アクセス]** をクリックします。3 つのアクセス レベルのどれでも使用できます。既定値は **[プライベート]** です。
7. **[OK]** をクリックして変更を保存します。ポータルの一覧に新しいコンテナーが表示されます。

Azure ストレージ アカウントにアクセスする前に、構成ファイルにアカウント名とアカウント キーを追加する必要があります。

**Azure ストレージ アカウントへの接続を構成するには**

1. メモ帳で **C:\hdp\hadoop-2.4.0.2.1.3.0-1981\etc\hadoop\core-site.xml** を開きます。
2. 次の <property> タグを他の <property> タグの横に追加します。

		<property>
		    <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
		    <value><StorageAccountKey></value>
		</property>

	<StorageAccountName> と <StorageAccountKey> は、ストレージ アカウント情報に一致する値に置き換える必要があります。

3. 変更を保存します。Hadoop サービスを再起動する必要はありません。

ストレージ アカウントにアクセスするには次の構文を使用します。

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

次に例を示します。

	hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/


##<a name="powershell"></a>Azure PowerShell の実行
Azure PowerShell コマンドレットの一部は HDInsight Emulator でもサポートされています。サポートされているコマンドレットは次のとおりです。

- HDInsight ジョブ定義コマンドレット

	- New-AzureHDInsightSqoopJobDefinition
	- New-AzureHDInsightStreamingMapReduceJobDefinition
	- New-AzureHDInsightPigJobDefinition
	- New-AzureHDInsightHiveJobDefinition
	- New-AzureHDInsightMapReduceJobDefinition
- Start-AzureHDInsightJob
- Get-AzureHDInsightJob
- Wait-AzureHDInsightJob

Hadoop ジョブを送信する例を次に示します。

	$creds = Get-Credential (hadoop as username, password can be anything)
	$hdinsightJob = <JobDefinition>
	Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

Get-Credential を呼び出すと入力画面が表示されます。ユーザー名として **hadoop** を使用する必要があります。パスワードは任意の文字列でかまいません。クラスター名は、常に **http://localhost:50111** です。

Hadoop ジョブの送信方法の詳細については、「[プログラムによる Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)」に関するページをご覧ください。Azure HDInsight PowerShell コマンドレットの詳細については、「[HDInsight PowerShell コマンドレット リファレンス ドキュメント][hdinsight-powershell-reference]」をご覧ください。


##<a name="remove"></a>HDInsight Emulator の削除
Emulator をインストールしたコンピューターでコントロール パネルを開き、**[プログラム]** で **[プログラムのアンインストール]** をクリックします。インストールされたプログラムの一覧から、**[Microsoft HDInsight Emulator for Azure]** を右クリックし、**[アンインストール**] をクリックします。


##<a name="nextsteps"></a>次のステップ
この MapReduce チュートリアルでは、HDInsight Emulator (Hadoop サンド ボックス) をインストールして、Hadoop ジョブをいくつか実行しました。詳細については、次の記事を参照してください。

- [Azure HDInsight の概要](../hdinsight-get-started.md)
- [HDInsight 用 Java MapReduce プログラムの開発](hdinsight-develop-deploy-java-mapreduce.md)
- [HDInsight 用 C# Hadoop ストリーミング MapReduce プログラムの開発](hdinsight-hadoop-develop-deploy-streaming-jobs.md)
- [HDInsight Emulator リリース ノート](hdinsight-emulator-release-notes.md)
- [HDInsight について議論する MSDN フォーラム](http://social.msdn.microsoft.com/Forums/hdinsight)



[azure-sdk]: http://azure.microsoft.com/downloads/
[azure-create-storage-account]: ../storage-create-storage-account.md
[azure-management-portal]: https://manage.windowsazure.com/
[netstat-url]: http://technet.microsoft.com/library/ff961504.aspx

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[hdinsight-emulator-install]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
[hdinsight-emulator-release-notes]: hdinsight-emulator-release-notes.md

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-develop-deploy-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-versions]: hdinsight-component-versioning.md

[Powershell-install-configure]: ../install-configure-powershell.md

[hadoop-commands-manual]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html

[image-hdi-emulator-services]: ./media/hdinsight-hadoop-emulator-get-started/HDI.Emulator.Services.png
 

<!---HONumber=62-->