<properties 
	pageTitle="Script Action を使って HDInsight クラスターをカスタマイズする| Azure" 
	description="Script Action を使って HDInsight クラスターをカスタマイズする方法について説明します。" 
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
	ms.date="01/15/2015" 
	ms.author="nitinme"/> 

# Script Action を使って HDInsight クラスターをカスタマイズする

Azure HDInsight クラスターをカスタマイズして、クラスター上に追加のソフトウェアをインストールしたり、クラスター上のアプリケーションの構成を変更できます。HDInsight は、カスタム スクリプトを呼び出す**Script Action**という構成オプションを提供します。Script Action はクラスター上で実行されるカスタマイズを定義します。これらのスクリプトを使って *as it is being deployed* クラスターをカスタマイズできます。  

HDInsight クラスターは、その他さまざまな方法でカスタマイズできます。たとえば、ストレージ アカウントの追加、Hadoop 構成ファイルの変更 (core-site.xml、hive-site.xml など)、クラスター内の一般的な場所への共有ライブラリの追加 (Hive、Oozie など) といった方法があります。これらのカスタマイズには HDInsight PowerShell、.NET SDK、または、Azure 管理ポータルを使用できます。詳細については、「[カスタム オプションを使用した HDInsight での Hadoop クラスターのプロビジョニング][hdinsight-provision-cluster]」を参照してください。



> [AZURE.NOTE] Script Action を使用したクラスターのカスタマイズは、HDInsight クラスター バージョン 3.1 でのみサポートされます。HDInsight クラスター バージョンの詳細については、[HDInsight クラスター バージョン](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/)に関するページを参照してください。



## この記事の内容

- [クラスター作成時のスクリプトの使用方法](#lifecycle)
- [クラスターをカスタマイズするためのスクリプトの作成方法](#writescript)
- [Script Action を使ってクラスターをカスタマイズする方法。](#howto)
- [クラスターのカスタマイズ例](#example)
- [HDInsight クラスターで使用するオープン ソース ソフトウェアのサポート](#support)


## <a name="lifecycle"></a>クラスター作成時のスクリプトの使用方法

Script Action を使用して HDInsight クラスターをカスタマイズできるのは、クラスターの作成時のみです。HDInsight クラスターは、作成時に次の段階をたどります。

![HDInsight cluster customization and stages during cluster provisioning][img-hdi-cluster-states] 

スクリプトが呼び出されるのは、クラスターの作成が *HDInsightConfiguration* 段階を完了した後、 *ClusterOperational* 段階の前です。各クラスターは、指定された順序で呼び出される複数の Script Action を受け取ることができます。

> [AZURE.NOTE] HDInsight クラスターをカスタマイズするオプションは、標準の Azure HDInsight サブスクリプションの一部として、追加料金なしで利用可能です。

### スクリプトが動作するしくみ

スクリプトは、ヘッド ノードまたはワーカー ノードのどちらかまたは両方で実行できます。スクリプトの実行中、クラスターは *ClusterCustomization* 段階に入ります。この段階でスクリプトは、システム管理者アカウントで、ノードで完全な管理者権限を持ち、クラスター内のすべての指定したノードで並列的に実行されます。 

> [AZURE.NOTE]  *Cluster customization* 段階ではクラスター ノードで管理者権限を持っているため、スクリプトを使用して、Hadoop 関連のサービスを含め、サービスの停止や開始などの操作を行うことができます。そのため、スクリプトの一部として、スクリプトの完了前に Ambari サービスおよびその他の Hadoop 関連のサービスが動作していることを確認する必要があります。これらのサービスでは、クラスターの作成時にクラスターが正常に稼動しているか確認する必要があります。これらのサービスに影響するクラスター上の構成を変更する場合は、用意されているヘルパー関数を使用する必要があります。ヘルパー関数の詳細については、[HDInsight での Script Action の開発][hdinsight-write-script]に関するページを参照してください。

スクリプトの出力とエラー ログは、クラスター用に指定した既定のストレージ アカウントに格納されます。ログは、 *u<\cluster-name-fragment><\time-stamp>setuplog* という名前のテーブルに格納されます。これらは、クラスター内のすべてのノード (ヘッドノードおよびワーカー ノード) で実行されるスクリプトから取得される集計ログです。

## <a name="writescript"></a>クラスターをカスタマイズするためのスクリプトの作成方法

クラスターをカスタマイズするスクリプトの記述方法の詳細については、[HDInsight での Script Action の開発][hdinsight-write-script]に関するページを参照してください。 

## <a name="howto"></a>Script Action を使ってクラスターをカスタマイズする方法。

Azure 管理ポータル、PowerShell コマンドレット、または HDInsight .NET SDK から Script Action を使用してクラスターをカスタマイズできます。 

**管理ポータルからの場合**

1. [カスタム オプションを使用したクラスターのプロビジョニング](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#portal)に関するページの説明に従い、**[カスタム作成]** オプションを使用してプロビジョニングを開始します。 
2. ウィザードの [スクリプトのアクション] ページで、**[スクリプト アクションの追加]** をクリックし、以下に示す Script Action についての詳細を入力します。

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>プロパティ</th><th>値</th></tr>
		<tr><td>名前</td>
			<td>Script Action の名前を指定します。</td></tr>
		<tr><td>スクリプト URI</td>
			<td>クラスターのカスタマイズのために呼び出されるスクリプトへの URI を指定します。</td></tr>
		<tr><td>ノードの種類</td>
			<td>カスタマイズ スクリプトが実行されるノードを指定します。<b>[すべてのノード]</b>、<b>[ヘッド ノードのみ]</b>、<b>[ワーカー ノードのみ]</b> から選択できます。
		<tr><td>パラメーター</td>
			<td>スクリプトで必要な場合は、パラメーターを指定します。</td></tr>
	</table>

	複数の Script Action を追加して、複数のコンポーネントをクラスターにインストールできます。スクリプトの追加後、チェックマークをクリックしてクラスターのプロビジョニングを開始します。 
  
**PowerShell コマンドレットを使用する場合**

HDInsight PowerShell のコマンドを使用して、1 つまたは複数の Script Action を実行します。カスタム スクリプトの呼び出しには、**<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** コマンドレットを使用できます。これらのコマンドレットを使用するには Azure PowerShell をインストールし、構成しておく必要があります。コンピューターを構成して HDInsight Powershell コマンドレットを実行する方法については、[Azure PowerShell のインストールおよび構成][powershell-install-configure]に関するページを参照してください。

次の PowerShell コマンドを使用して、HDInsight クラスターのデプロイ時に、1 つの Script Action を実行します。

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName -Uri http://uri.to/scriptaction.ps1 -Parameters MyScriptActionParameter -ClusterRoleCollection HeadNode,DataNode

	New-AzureHDInsightCluster -Config $config

次の PowerShell コマンドを使用して、HDInsight クラスターのデプロイ時に、複数の Script Action を実行します。

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName1 -Uri http://uri.to/scriptaction1.ps1 -Parameters MyScriptAction1Parameters -ClusterRoleCollection HeadNode,DataNode | Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName2 -Uri http://uri.to/scriptaction2.ps1 -Parameters MyScriptAction2Parameters -ClusterRoleCollection HeadNode

	New-AzureHDInsightCluster -Config $config

**HDInsight .NET SDK を使用する場合**

HDInsight .NET SDK では、 <a href="http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx" target="_blank">カスタム スクリプトを呼び出すための</a> ScriptAction クラスが用意されています。HDInsight .NET SDK を使用するには:

1. Visual Studio アプリケーションを作成し、Nuget から SDK をインストールします。**[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。コンソールで次のコマンドを実行して、パッケージをインストールします。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

2. SDK を使用してクラスターを作成します。手順については、[.NET SDK を使用した HDInsight クラスターのプロビジョニング](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#sdk)に関するページを参照してください。

3. 以下に示すように、**ScriptAction** クラスを使用してカスタム スクリプトを呼び出します。

		
		var clusterInfo = new ClusterCreateParameters()
		{
			// PROVIDE THE CLUSTER INFORMATION LIKE
			// NAME, STORAGE ACCOUNT, CREDENTIALS,
			// CLUSTER SIZE, and VERSION		    
			...
			...
		};

		// ADD THE SCRIPT ACTION TO INSTALL SPARK
		clusterInfo.ConfigActions.Add(new ScriptAction(
	  		"MyScriptActionName", // Name of the config action
	  		new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install component on
	  		new Uri("http://uri.to/scriptaction.ps1"), // Location of the script to install the component
	  		"MyScriptActionParameter" //Parameters, if any, required by the script.
		));


## <a name="example"></a>クラスターのカスタマイズ例

HDInsight には、HDInsight クラスターに次の各コンポーネントをインストールするサンプル スクリプトが用意されています。

- **Spark のインストール**。[HDInsight クラスターへの Spark のインストール][hdinsight-install-spark]に関するページを参照してください。
- **R のインストール**。[HDInsight クラスターへの R のインストール][hdinsight-install-r]に関するページを参照してください。
- **Solr のインストール**。[HDInsight クラスターに Solr をインストールして使用します。](hdinsight-hadoop-solr-install.md)
- **Giraph のインストール**。[HDInsight クラスターに Giraph をインストールして使用します。](hdinsight-hadoop-giraph-install.md)

## <a name="support"></a>HDInsight クラスターで使用するオープン ソース ソフトウェアのサポート
Microsoft Azure HDInsight サービスは柔軟性に優れたプラットフォームであり、Hadoop を中心に形成されたオープン ソース テクノロジのエコシステムを利用し、クラウド内でビッグ データ アプリケーションを構築できます。Azure サポート FAQ Web サイトのサポート範囲のセクションでも説明しているように、Microsoft Azure では、 <a href="http://azure.microsoft.com/support/faq/" target="_blank">オープン ソース テクノロジについて一般的なレベルのサポートを提供しています</a>。これに加え、HDInsight サービスでは、次に説明するいくつかのコンポーネントについてさらに高いレベルのサポートを受けることができます。

HDInsight サービスで利用できるオープン ソース コンポーネントには、2 つの種類があります。

- **組み込みコンポーネント**。これらのコンポーネントは、HDInsight クラスターにプレインストールされており、クラスターの主要な機能を提供します。たとえば、Yarn リソース マネージャー、Hive クエリ言語、Mahout ライブラリなどがこのカテゴリに属します。すべてのクラスター コンポーネントの一覧は、 <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">ここから入手できます</a>。
- **カスタム コンポーネント**。クラスターのユーザーは、コミュニティで入手できるコンポーネントや自作のコンポーネントを、インストールするか、ワークロード内で使用することができます。

組み込みコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離および解決については、Microsoft サポートが支援します。

カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。たとえば、次のような数多くのコミュニティ サイトが利用できます。 <a href ="https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight" target="_blank">HDInsight についての MSDN フォーラム</a>、 <a href="http://stackoverflow.com" target="_blank">http://stackoverflow.com</a>。また、Apache プロジェクトにも、 <a href="http://apache.org" target="_blank">http://apache.org にプロジェクト サイトがあり、</a>たとえば、 <a href="http://hadoop.apache.org/" target="_blank">Hadoop</a>、 <a href="http://spark.apache.org/" target="_blank">Spark</a>。

HDInsight サービスでは、カスタム コンポーネントを使用する方法をいくつか用意しています。コンポーネントの用途やクラスターへのインストール方法にかかわらず、同じレベルのサポートが適用されます。以下は、HDInsight クラスターでのカスタム コンポーネントの用途として、最も一般的な方法の一覧です。

1. ジョブの送信。Hadoop や他の種類のジョブを、カスタム コンポーネントを実行または使用するクラスターに送信できます。
2. クラスターのカスタマイズ。クラスター作成時に、追加設定や、クラスター ノードにインストールするカスタム コンポーネントを指定できます。
3. サンプル。よく利用されるカスタム コンポーネントに対しては、それらを HDInsight クラスターで使用する方法について Microsoft やその他の提供者がサンプルを用意している場合があります。これらのサンプルはサポートなしで提供されます。


## 関連項目##
「[カスタム オプションを使用した HDInsight での Hadoop クラスターのプロビジョニング][hdinsight-provision-cluster]」では、その他のカスタム オプションを使用して HDInsight クラスターをプロビジョニングする方法について説明しています。

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-write-script]: ../hdinsight-hadoop-script-actions/
[hdinsight-provision-cluster]: ../hdinsight-provision-clusters/
[powershell-install-configure]: ../install-configure-powershell/


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Stages during cluster provisioning"

<!--HONumber=42-->
