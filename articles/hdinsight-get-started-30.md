<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="概要" pageTitle="HDInsight での Hadoop 2.2 クラスター使用の概要 | Azure" metaKeywords="" description="HDInsight での Hadoop 2.2 クラスター使用の概要と Big Data ソリューション。クラスターのプロビジョニングと MapReduce ジョブを実行し、データを Excel に出力して分析する方法を説明します。" metaCanonical="" services="hdinsight" documentationCenter="" title="Azure HDInsight の概要" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />




#HDInsight での Hadoop 2.2 クラスター使用の概要

HDInsight は、[Apache Hadoop][apache-hadoop] をクラウドのサービスとして利用するためのツールです。HDInsight により、拡張性とコスト効率に優れたシンプルな Azure 環境で MapReduce ソフトウェア フレームワークを利用できます。また、HDInsight は Azure BLOB ストレージを使用して低コストでデータを管理および保存するしくみも備えています。

このチュートリアルでは、Azure 管理ポータルを使用して HDInsight クラスターをプロビジョニングし、PowerShell を使用して Hadoop MapReduce ジョブを送信し、MapReduce ジョブの出力データを Excel にインポートして調査します。

> [WACOM.NOTE] このチュートリアルでは、HDInsight での Hadoop 2.2 クラスターの使用について説明します。HDInsight で Hadoop 1.2 クラスターを使用するチュートリアルについては、「[Azure HDInsight の概要][hdinsight-get-started]」を参照してください。

> [WACOM.NOTE]	*asv://* 構文は HDInsight クラスター バージョン 3.0 でサポートされていません。また、それ以降のバージョンでもサポートされません。代わりに、*wasb://* 構文を使用する必要があります。

Azure の HDInsight を一般に利用可能にすると共に、Microsoft は HDInsight Emulator for Azure (旧称 Microsoft HDInsight 開発者プレビュー) もリリースしました。この製品は開発者シナリオを対象としており、そのため単一ノード展開のみをサポートします。HDInsight Emulator の使用法については、「[HDInsight Emulator の概要][hdinsight-emulator]」を参照してください。

**前提条件:**

このチュートリアルを読み始める前に、次の項目を用意する必要があります。


- Azure サブスクリプションが必要です。サブスクリプションの入手方法の詳細については、[購入オプション][azure-purchase-options]、[メンバー プラン][azure-member-offers]、または[無料評価版][azure-free-trial]に関するページを参照してください。
- Windows 8、Windows 7、Windows Server 2012、Windows Server 2008 R2 のいずれかを実行しているコンピューターが必要です。このコンピューターは、MapReduce ジョブを送信するために使用します。
- Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone、Office 2010 Professional Plus のいずれかが必要です。

**所要時間: ** 30 分

##このチュートリアルの内容

* [PowerShell を実行するローカル環境をセットアップする](#setup)
* [HDInsight クラスターをプロビジョニングする](#provision)
* [ワードカウント MapReduce プログラムを実行する](#sample)
* [Microsoft Business Intelligence ツールに接続する](#powerquery)
* [次のステップ](#nextsteps)



##<a id="setup"></a> PowerShell を実行するローカル環境をセットアップする

MapReduce ジョブは、いくつかの方法で HDInsight に送信できます。このチュートリアルでは、Azure PowerShell を使用します。Azure PowerShell をインストールするには、[Microsoft Web プラットフォーム インストーラー][powershell-download]を実行します。メッセージが表示されたら **[実行]**、**[インストール]** の順にクリックして、指示に従います。詳細については、[Azure PowerShell のインストールおよび構成に関するページ][powershell-install-configure]を参照してください。

PowerShell コマンドレットでサービスを管理するために、サブスクリプション情報が必要です。

**Azure AD を使用してサブスクリプションに接続するには**

1. Azure PowerShell コンソール ウィンドウを開きます。手順は「[方法: Azure PowerShell をインストールするには][powershell-open]」を参照してください。
2. 次のコマンドを実行します。

		Add-AzureAccount

3. ウィンドウで、アカウントに関連付けられている電子メール アドレスとパスワードを入力します。Azure により資格情報が認証および保存され、ウィンドウが閉じます。

サブスクリプションに接続するもう 1 つの方法は、証明書方式の使用です。手順については、[Azure PowerShell のインストールおよび構成に関するページ][powershell-install-configure]を参照してください。
	
##<a name="provision"></a>HDInsight クラスターをプロビジョニングする

HDInsight のプロビジョン プロセスでは、Azure のストレージ アカウントを既定のファイル システムとして使用する必要があります。ストレージ アカウントは、HDInsight コンピューティング リソースと同じデータ センターに置く必要があります。現在、HDInsight クラスターのプロビジョニングができるのは次のデータ センターだけです。

- 東南アジア
- 北ヨーロッパ
- 西ヨーロッパ
- 米国東部
- 米国西部

Azure のストレージ アカウント用に、5 か所のデータ センターのうちいずれかを選択する必要があります。

**Azure ストレージ アカウントを作成するには**

1. [Azure 管理ポータル][azure-management-portal]にサインインします。
2. 左下にある **[新規]** をクリックし、**[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

	![HDI.StorageAccount.QuickCreate][image-hdi-storageaccount-quickcreate]

3. **[URL]**、**[場所]**、および **[レプリケーション]** に値を入力し、**[ストレージ アカウントの作成]** をクリックします。アフィニティ グループはサポートされていません。新しいストレージ アカウントがストレージ一覧に表示されます。
4. 新しいストレージ アカウントの **[状態]** 列が **[オンライン]** になるまで待ちます。
5. 一覧の新しいストレージ アカウントをクリックして選択します。
6. ページの下部にある **[アクセス キーの管理]** をクリックします。
7. **[ストレージ アカウント名]** と **[プライマリ アクセス キー]** の値をメモします。この情報は後で必要になります。


詳細な手順については、「[ストレージ アカウントの作成方法][azure-create-storageaccount]」および「[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]」を参照してください。

















HDInsight 3.0 クラスターのプロビジョニングは、現在、カスタム作成オプションを使用した方法だけがサポートされています。

**HDInsight クラスターをプロビジョニングするには**

1. [Azure 管理ポータル][azure-management-portal]にサインインします。

2. 左側にある **[HDInsight]** をクリックして、アカウントにある HDInsight クラスターを一覧表示します。次のスクリーンショットには、既存の HDInsight クラスターが 1 つもありません。

	![HDI.ClusterStatus][image-hdi-clusterstatus]

3. 左下にある **[新規]** をクリックし、**[データ サービス]**、**[HDInsight]**、**[簡易作成]** の順にクリックします。

	![HDI.CustomCreateCluster][image-hdi-customcreatecluster]

4. [クラスターの詳細] タブで、次の値を入力または選択します。

	<table border="1">
	<tr><th>名前</th><th>値</th></tr>
	<tr><td><strong>クラスター名</strong></td><td>クラスターの名前。</td></tr>
	<tr><td><strong>データ ノード</strong></td><td>展開するデータ ノードの数。テストでは、単一ノード クラスターを作成します。<br />クラスター サイズの制限は、Azure サブスクリプションによって変わります。制限値を上げるには、Azure の課金サポートにお問い合わせください。</td></tr>
	<tr><td><strong>HDInsight のバージョン</strong></td><td>HDInsight 上に Hadoop 2.2 クラスターを作成するために <strong>[3.0]</strong> を選択します。</td></tr>
	<tr><td><strong>リージョン</strong></td><td>最後の手順で作成したストレージ アカウントと同じリージョンを選択します。HDInsight は、同じリージョンに配置されたストレージ アカウントを必要とします。これ以後の構成作業では、ここで指定した地域と同じリージョンにあるストレージ アカウントしか選択できません。
	</td></tr>
	</table>

4. ページの右下隅にある右矢印をクリックして、クラスター ユーザーを構成します。
4. [クラスター ユーザーの構成] タブで、HDInsight クラスターのユーザー アカウントの **[ユーザー名]** と **[パスワード]** を入力します。クラスターをプロビジョニングした後、このアカウントに加えて、RDP ユーザー アカウントも作成できるため、クラスターにリモート デスクトップ接続することができます。手順については、「[管理ポータルを使用した HDInsight の管理][hdinsight-admin-portal]」を参照してください。
4. ページの右下隅にある右矢印をクリックして、ストレージ アカウントを構成します。
5. [ストレージ アカウント] タブで、次の値を入力または選択します。

	<table border="1">
	<tr><th>名前</th><th>値</th></tr>
	<tr><td>ストレージ アカウント</td><td><strong>[既存のストレージを使用する]</strong> を選択します。新しいストレージ アカウントを作成していない場合は、管理ポータルに作成させることもできます。</td></tr>
	<tr><td>アカウント名</td><td>このチュートリアルの最後の手順で作成したストレージ アカウントを指定します。リスト ボックスには同じリージョンのストレージ アカウントだけが表示されることに注意してください。</td></tr>
	<tr><td>既定のコンテナー</td><td> <strong>[既定のコンテナーの作成]</strong> を選択します。このオプションを選択した場合、既定のコンテナー名はクラスター名と同じになります。</td></tr>
	<tr><td>追加のストレージ アカウント</td><td><strong>[0]</strong> を選択します。クラスターは最大 7 個の追加のストレージ アカウントに接続することができます。</td></tr>
	</table>

5. 右下隅にあるチェック アイコンをクリックして、クラスターを作成します。プロビジョニング処理が完了すると、[状態] 列に **[実行中]** と表示されます。








##<a name="sample"></a>ワードカウント MapReduce ジョブを実行する

HDInsight クラスターのプロビジョニングが終わりました。次は、MapReduce ジョブを実行してテキスト ファイルの単語数を計算します。

MapReduce ジョブを実行するには次の要素が必要です。

* MapReduce プログラム。このチュートリアルでは、HDInsight クラスター ディストリビューションに付属するワードカウント サンプルを使用するため、自分で書く必要はありません。プログラムは */example/jars/hadoop-mapreduce-examples.jar* に格納されています。独自の MapReduce ジョブの作成手順については、「[HDInsight 用 Java MapReduce プログラムの開発][hdinsight-develop-MapReduce]」を参照してください。

* 入力ファイル。*/example/data/gutenberg/davinci.txt* を入力ファイルとして使用します。ファイルのアップロードについては、「[データを HDInsight へアップロードする方法][hdinsight-upload-data]」を参照してください。
* 出力ファイル フォルダー。*/example/data/WordCountOutput* を出力ファイル フォルダーとして使用します。フォルダーが存在しない場合は自動的に作成されます。

BLOB ストレージ内のファイルにアクセスするための URI スキームは次のとおりです。

	wasb[s]://<containername>@<storageaccountname>.blob.core.windows.net/<path>

> [WACOM.NOTE] 既定で、既定のファイル システムに使用される Blob コンテナーの名前は、HDInsight クラスターと同じです。

この URI スキームは、暗号化なしのアクセス (*wasb:* プレフィックス) と SSL で暗号化されたアクセス (wasbs:) の両方に対応しています。同じ Azure データ センター内のデータにアクセスする場合でも、できる限り wasbs を使用することをお勧めします。

HDInsight は既定のファイル システムとして BLOB ストレージ コンテナーを使用するので、相対パスまたは絶対パスを使用して、既定のファイル システム内のファイルとディレクトリを指定できます。

たとえば、hadoop-mapreduce-examples.jar にアクセスする場合、次のいずれかを使用できます。

	● wasb://<containername>@<storageaccountname>.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	● wasb:///example/jars/hadoop-mapreduce-examples.jar
	● /example/jars/hadoop-mapreduce-examples.jar
				
これらのファイルのパスの先頭には *wasb://* が使用されています。これは、入力ファイルと出力ファイルで Azure BLOB ストレージを使用することを示しています。出力ディレクトリの既定のパスは、*wasb:///user/&lt;username&gt;* フォルダーを基準とした相対パスです。

詳細については、「[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]」を参照してください。





















**ワードカウント サンプルを実行するには**

1. **Azure PowerShell** を開きます。Azure PowerShell コンソール ウィンドウを開く手順については、[Azure PowerShell のインストールと構成に関するページ][powershell-install-configure]を参照してください。

3. 次のコマンドを実行して、変数を設定します。
		
		$subscriptionName = "<SubscriptionName>" 
		$clusterName = "<HDInsightClusterName>"        
		
5. 次のコマンドを実行して、MapReduce ジョブ定義を作成します。

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	hadoop-mapreduce-examples.jar ファイルは HDInsight クラスターのディストリビューションに付属しています。MapReduce ジョブには引数が 2 つあります。最初の引数はソース ファイル名で、2 つ目の引数は出力ファイル パスです。ソース ファイルは HDInsight クラスターのディストリビューションに付属しており、出力ファイル パスは実行時に作成されます。

6. 次のコマンドを実行して、MapReduce ジョブを送信します。

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 
		
	MapReduce ジョブ定義に加えて、MapReduce ジョブを実行する HDInsight クラスター名も指定する必要があります。

	*Start-AzureHDInsightJob* は非同期呼び出しです。ジョブの完了を確認するには、*Wait-AzureHDInsightJob* コマンドレットを使用します。

6. 次のコマンドを実行して、MapReduce ジョブの完了を確認します。

		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 
		
8. 次のコマンドを実行して、MapReduce ジョブの実行中に発生したエラーを確認します。	
	
		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError
		
	次のスクリーンショットは、正常実行時の出力を示しています。正常でない場合は、エラー メッセージが表示されます。

	![HDI.GettingStarted.RunMRJob][image-hdi-gettingstarted-runmrjob]
































**MapReduce ジョブの結果を取得するには**

1. **Azure PowerShell** を開きます。
2. 次のコマンドを実行して、C:\Tutorials フォルダーを作成し、そのフォルダーに移動します。

		mkdir \Tutorials
		cd \Tutorials
	
	Azure Powershell の既定のディレクトリは *C:\Windows\System32\WindowsPowerShell\v1.0* です。既定では、このフォルダーに対する書き込みアクセス許可がありません。書き込みアクセス許可があるフォルダーに移動する必要があります。
	
2. 次のコマンドの 3 つの変数を設定し、コマンドを実行します。

		$subscriptionName = "<SubscriptionName>"       
		$storageAccountName = "<StorageAccountName>"   
		$containerName = "<ContainerName>"			   

	Azure のストレージ アカウントは、このチュートリアルで先に作成したアカウントです。ストレージ アカウントは、既定の HDInsight クラスター ファイル システムとして使用する BLOB コンテナーをホストするために使用されます。BLOB ストレージ コンテナー名は、クラスターのプロビジョニング時に別の名前を指定しない限り、通常、HDInsight クラスターと同じ名前です。

3. 次のコマンドを実行して、Azure のストレージ コンテキスト オブジェクトを作成します。
		
		# Create the storage account context object
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	*Select-AzureSubscription* は、サブスクリプションが複数あり、使用するサブスクリプションが既定のサブスクリプションではない場合に、現在のサブスクリプションを設定するために使用します。

4. 次のコマンドを実行して、MapReduce ジョブの出力を BLOB コンテナーからワークステーションにダウンロードします。

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	*example/data/WordCountOutput* フォルダーは、MapReduce ジョブの実行時に指定した出力フォルダーです。*part-r-00000* は MapReduce ジョブの出力の既定のファイル名です。ファイルはフォルダー構造を保ったままローカル フォルダーにダウンロードされます。たとえば、次のスクリーンショットでは、現在のフォルダーが C ドライブのルート フォルダーです。ファイルは *C:\example\data\WordCountOutput\#92;* フォルダーにダウンロードされます。

5. 次のコマンドを実行して、MapReduce ジョブの出力ファイルの内容を表示します。

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]

	MapReduce ジョブは、単語と出現回数が記録された *part-r-00000* という名前のファイルを作成します。スクリプトでは findstr コマンドを使用して、*"there"* を含む単語をすべて表示しています。


> [WACOM.NOTE]MapReduce ジョブで出力された複数行を含む <i>./example/data/WordCountOutput/part-r-00000</i> ファイルをメモ帳で開いた場合、改行が正しく表示されません。これは予期されることです。


	
##<a name="powerquery"></a>Microsoft Business Intelligence ツールに接続する

Power Query for Excel アドインを使用すると、Excel に HDInsight の出力をエクスポートして、Microsoft Business Intelligence (BI) ツールで結果をさらに処理し表示することができます。HDInsight クラスターを作成したときに、クラスターと同じ名前を持つ既定のコンテナーが、クラスターに関連付けられたストレージ アカウントに作成されています。コンテナーにはファイル セットが自動的に配置されます。そのうちの 1 つがサンプル Hive テーブルです。このセクションでは、このテーブルに格納されているデータを Excel にインポートして表示し、追加の処理を実行する方法を説明します。

チュートリアルのこの部分を完了するには、Excel 2010 または 2013 がインストールされている必要があります。ここでは HDInsight に付属する既定の Hive テーブルをインポートします。

**Microsoft Power Query for Excel をダウンロードするには**

- [Microsoft ダウンロード センター](http://www.microsoft.com/ja-jp/download/details.aspx?id=39379)から Microsoft Power Query for Excel をダウンロードして、インストールします。

**HDInsight データをインポートするには**

1. Excel を開き、新しい空のブックを作成します。
3. **[Power Query]** メニューをクリックし、**[その他のデータ ソース]**、**[Azure の HDInsight]** の順にクリックします。

	![HDI.GettingStarted.PowerQuery.ImportData][image-hdi-gettingstarted-powerquery-importdata]

3. クラスターに関連付けられた Azure BLOB ストレージ アカウントの名前を **[アカウント名]** ボックスに入力し、**[OK]** をクリックします。これは、このチュートリアルで先に作成したストレージ アカウントです。
4. Azure BLOB ストレージ アカウントのアカウント キーを **[アカウント キー]** ボックスに入力し、**[保存]** をクリックします 
5. 右側にある [ナビゲーター] ウィンドウで、BLOB ストレージ コンテナーの名前をダブルクリックします。既定で、コンテナー名はクラスター名と同じです。

6. **[名前]** 列にある **[part-r-00000]** (パスは *.../example/data/WordCountOutput*) を見つけて、**[part-r-00000]** の左側にある **[バイナリ]** をクリックします。

	![HDI.GettingStarted.PowerQuery.ImportData2][image-hdi-gettingstarted-powerquery-importdata2]

8. **[Column1.1]** を右クリックし、**[名前の変更]** をクリックします。
9. 名前を「**Word**」に変更します。
10. 同様にして **[Column1.2]** を「**Count**」に変更します。

	![HDI.GettingStarted.PowerQuery.ImportData3][image-hdi-gettingstarted-powerquery-importdata3]

9. 左上隅にある **[適用して閉じる]** をクリックします。このクエリは Excel に Hive テーブルをインポートします。

##<a name="nextsteps"></a>次のステップ
このチュートリアルでは、HDInsight を使用してクラスターをプロビジョニングした後、そのクラスター上で MapReduce ジョブを実行し、結果を Excel にインポートする方法を説明しました。このデータは、BI ツールを使用してさらに処理し、グラフィカルに表示することができます。詳細については、次の記事を参照してください。

- [HDInsight の使用][hdinsight-get-started]
- [HDInsight Emulator の概要][hdinsight-emulator]
- [HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]
- [PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]
- [HDInsight へのデータのアップロード][hdinsight-upload-data]
- [HDInsight での Hive の使用][hdinsight-hive]
- [HDInsight での Pig の使用][hdinsight-pig]
- [Use Oozie with HDInsight (HDInsight での Oozie の使用)][hdinsight-oozie]
- [HDInsight 用 C# Hadoop ストリーミング MapReduce プログラムの開発][hdinsight-develop-streaming]
- [Develop Java MapReduce programs for HDInsight (HDInsight 用 Java MapReduce プログラムの開発)][hdinsight-develop-mapreduce]


[hdinsight-get-started]: /ja-jp/documentation/articles/hdinsight-get-started/
[hdinsight-get-started-3.0]: /ja-jp/documentation/articles/hdinsight-get-started-30/
[hdinsight-provision]: /ja-jp/documentation/articles/hdinsight-provision-clusters/
[hdinsight-admin-powershell]: /ja-jp/documentation/articles/hdinsight-administer-use-powershell/
[hdinsight-upload-data]: /ja-jp/documentation/articles/hdinsight-upload-data/
[hdinsight-mapreduce]: /ja-jp/documentation/articles/hdinsight-use-mapreduce
[hdinsight-hive]: /ja-jp/documentation/articles/hdinsight-use-hive/
[hdinsight-pig]: /ja-jp/documentation/articles/hdinsight-use-pig/
[hdinsight-oozie]: /ja-jp/documentation/articles/hdinsight-use-oozie/
[hdinsight-storage]: /ja-jp/documentation/articles/hdinsight-use-blob-storage/
[hdinsight-emulator]: /ja-jp/documentation/articles/hdinsight-get-started-emulator/
[hdinsight-develop-streaming]: /ja-jp/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: /ja-jp/documentation/articles/hdinsight-develop-deploy-java-mapreduce/
[hdinsight-admin-portal]: /ja-jp/documentation/articles/hdinsight-administer-use-management-portal/
[hdinsight-develop-streaming]: /ja-jp/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/

[azure-purchase-options]: https://www.windowsazure.com/ja-jp/pricing/purchase-options/
[azure-member-offers]: https://www.windowsazure.com/ja-jp/pricing/member-offers/
[azure-free-trial]: https://www.windowsazure.com/ja-jp/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: /ja-jp/documentation/articles/storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: /ja-jp/documentation/articles/install-configure-powershell/
[powershell-open]: /ja-jp/documentation/articles/install-configure-powershell/#install

[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-get-started-3.0/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started-3.0/HDI.ClusterStatus.png
[image-hdi-customcreatecluster]: ./media/hdinsight-get-started-3.0/HDI.CustomCreateCluster.png
[image-hdi-wordcountdiagram]: ./media/hdinsight-get-started-3.0/HDI.WordCountDiagram.gif
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-get-started-3.0/HDI.GettingStarted.MRJobOutput.png
[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-get-started-3.0/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData2.png
[image-hdi-gettingstarted-powerquery-importdata3]: ./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData3.png


