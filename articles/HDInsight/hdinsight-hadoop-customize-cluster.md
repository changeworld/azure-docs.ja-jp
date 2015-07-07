<properties 
	pageTitle="Script Action を使った HDInsight クラスターのカスタマイズ | Microsoft Azure" 
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
	ms.date="03/03/2015" 
	ms.author="nitinme"/>

# Script Action を使って HDInsight クラスターをカスタマイズする

Azure HDInsight クラスターをカスタマイズして、クラスター上に追加のソフトウェアをインストールしたり、クラスター上のアプリケーションの構成を変更できます。HDInsight は、カスタム スクリプトを呼び出す**Script Action** という構成オプションを提供します。Script Action はクラスター上で実行されるカスタマイズを定義します。これらのスクリプトを使って*展開される際の*クラスターをカスタマイズできます。

HDInsight クラスターは、その他さまざまな方法でカスタマイズできます。たとえば、Azure ストレージ アカウントの追加、Hadoop 構成ファイルの変更 (core-site.xml、hive-site.xml など)、クラスター内の一般的な場所への共有ライブラリの追加 (Hive、Oozie など) といった方法があります。これらのカスタマイズは、Azure PowerShell、Azure HDInsight .NET SDK、Azure ポータルから実行できます。詳細については、「[カスタム オプションを使用した HDInsight での Hadoop クラスターのプロビジョニング][hdinsight-provision-cluster]」をご覧ください。



> [AZURE.NOTE]Script Action を使用したクラスターのカスタマイズは、HDInsight クラスター バージョン 3.1 でのみサポートされます。HDInsight クラスター バージョンの詳細については、「[HDInsight クラスター バージョン](hdinsight-component-versioning.md)」をご覧ください。


## <a name="lifecycle"></a>クラスター作成時のスクリプトの使用方法

Script Action を使用して HDInsight クラスターをカスタマイズできるのは、クラスターの作成時のみです。HDInsight クラスターは、作成時に次の段階をたどります。

![クラスター プロビジョニング時の HDInsight クラスターのカスタマイズと段階][img-hdi-cluster-states]

スクリプトが呼び出されるのは、クラスターの作成が **HDInsightConfiguration** 段階を完了した後、**ClusterOperational** 段階の前です。各クラスターは、指定された順序で呼び出される複数の Script Action を受け取ることができます。

> [AZURE.NOTE]HDInsight クラスターをカスタマイズするオプションは、標準の Azure HDInsight サブスクリプションの一部として、追加料金なしで利用可能です。

### スクリプトが動作するしくみ

スクリプトは、ヘッド ノードとワーカー ノードのどちらかまたは両方で実行できます。スクリプトの実行中、クラスターは **ClusterCustomization** 段階に入ります。この段階でスクリプトは、システム管理者アカウントで、ノードで完全な管理者権限を持ち、クラスター内のすべての指定したノードで並列的に実行されます。

> [AZURE.NOTE]**ClusterCustomization** 段階ではクラスター ノードで管理者権限を持っているため、スクリプトを使用して、Hadoop 関連のサービスを含め、サービスの停止や開始などの操作を行うことができます。そのため、スクリプトの一部として、スクリプトの完了前に Ambari サービスや他の Hadoop 関連のサービスが動作していることを確認する必要があります。これらのサービスでは、クラスターの作成時にクラスターが正常に稼動しているか確認する必要があります。これらのサービスに影響するクラスター上の構成を変更する場合は、用意されているヘルパー関数を使用する必要があります。ヘルパー関数の詳細については、「[HDInsight での Script Action の開発][hdinsight-write-script]」をご覧ください。

スクリプトの出力とエラー ログは、クラスター用に指定した既定のストレージ アカウントに格納されます。ログは、**u<\cluster-name-fragment><\time-stamp>setuplog** という名前のテーブルに格納されます。これらは、クラスター内のすべてのノード (ヘッドノードとワーカー ノード) で実行されるスクリプトから取得される集計ログです。

## <a name="writescript"></a>クラスターをカスタマイズするためのスクリプトの作成方法

クラスターをカスタマイズするスクリプトの記述方法の詳細については、「[HDInsight での Script Action の開発][hdinsight-write-script]」をご覧ください。

## <a name="howto"></a>Script Action を使ってクラスターをカスタマイズする方法

Azure ポータル、Azure PowerShell コマンドレット、HDInsight .NET SDK から Script Action を使用してクラスターをカスタマイズできます。

**Azure ポータルの使用**

1. 「[カスタム オプションを使用したクラスターのプロビジョニング](hdinsight-provision-clusters.md#portal)」に関するページの説明に従い、**[カスタム作成]** オプションを使用してプロビジョニングを開始します。 
2. ウィザードの **[スクリプトのアクション]** ページで、**[スクリプト アクションの追加]** をクリックし、次に示すように、スクリプト アクションの詳細を指定します。

	![Script Action を使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Script Action を使ってクラスターをカスタマイズする")
	
	<table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>名前</td>
		<td>スクリプト アクションの名前を指定します。</td></tr>
	<tr><td>スクリプト URI</td>
		<td>クラスターのカスタマイズのために呼び出されるスクリプトへの URI を指定します。</td></tr>
	<tr><td>ノードの種類</td>
		<td>カスタマイズ スクリプトが実行されるノードを指定します。<b>[すべてのノード]</b>、<b>[ヘッド ノードのみ]</b>、<b>[ワーカー ノードのみ]</b> から選択できます。
	<tr><td>パラメーター</td>
		<td>スクリプトで必要な場合は、パラメーターを指定します。</td></tr>
</table>複数のスクリプト操作を追加して、クラスターに複数のコンポーネントをインストールすることができます。スクリプトの追加後、チェックマークをクリックしてクラスターのプロビジョニングを開始します。
  
**Azure PowerShell コマンドレットの使用**

HDInsight の Azure PowerShell コマンドを使用して、1 つまたは複数の Script Action を実行します。**<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** コマンドレットを使用して、カスタム スクリプトを呼び出します。これらのコマンドレットを使用するには Azure PowerShell をインストールし、構成しておく必要があります。コンピューターを構成して HDInsight Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成][powershell-install-configure]」をご覧ください。

次の Azure PowerShell コマンドを使用して、HDInsight クラスターのデプロイ時に、1 つの Script Action を実行します。

	$config = New-AzureHDInsightClusterConfig –ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName –Uri http://uri.to/scriptaction.ps1 –Parameters MyScriptActionParameter -ClusterRoleCollection HeadNode,DataNode

	New-AzureHDInsightCluster -Config $config

次の Azure PowerShell コマンドを使用して、HDInsight クラスターのデプロイ時に、複数の Script Action を実行します。

	$config = New-AzureHDInsightClusterConfig –ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName1 –Uri http://uri.to/scriptaction1.ps1 –Parameters MyScriptAction1Parameters -ClusterRoleCollection HeadNode,DataNode | Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName2 –Uri http://uri.to/scriptaction2.ps1 -Parameters MyScriptAction2Parameters -ClusterRoleCollection HeadNode

	New-AzureHDInsightCluster -Config $config

**HDInsight .NET SDK の使用**

HDInsight .NET SDK は、カスタム スクリプトを呼び出す <a href="http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx" target="_blank">ScriptAction</a> クラスを提供します。HDInsight .NET SDK を使用するには:

1. Visual Studio アプリケーションを作成し、NuGet から SDK をインストールします。**[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。コンソールで次のコマンドを実行して、パッケージをインストールします。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

2. SDK を使用してクラスターを作成します。手順については、「[.NET SDK を使用した HDInsight クラスターのプロビジョニング](hdinsight-provision-clusters.md#sdk)」に関するページをご覧ください。

3. 以下に示すように、**ScriptAction** クラスを使用してカスタム スクリプトを呼び出します。

		
		var clusterInfo = new ClusterCreateParameters()
		{
			// Provide the cluster information, like
			// name, Storage account, credentials,
			// cluster size, and version		    
			...
			...
		};

		// Add the script action to install Spark
		clusterInfo.ConfigActions.Add(new ScriptAction(
	  		"MyScriptActionName", // Name of the config action
	  		new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install the component on
	  		new Uri("http://uri.to/scriptaction.ps1"), // Location of the script to install the component
	  		"MyScriptActionParameter" //Parameters, if any, required by the script
		));


## <a name="example"></a>クラスターのカスタマイズ例

HDInsight には、HDInsight クラスターに次の各コンポーネントをインストールするサンプル スクリプトが用意されています。

- **Install Spark** - 「[HDInsight クラスターで Spark をインストールして使用する][hdinsight-install-spark]」をご覧ください。
- **Install R** - 「[HDInsight Hadoop クラスターに R をインストールして使用する][hdinsight-install-r]」をご覧ください。
- **Install Solr** - 「[HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install.md)」をご覧ください。
- **Install Giraph** - 「[HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install.md)」をご覧ください。

## <a name="support"></a>HDInsight クラスターで使用するオープン ソース ソフトウェアのサポート
Microsoft Azure HDInsight サービスは柔軟性に優れたプラットフォームであり、Hadoop を中心に形成されたオープン ソース テクノロジのエコシステムを利用し、クラウド内でビッグ データ アプリケーションを構築できます。<a href="http://azure.microsoft.com/support/faq/" target="_blank">Azure サポート FAQ Web サイト</a>の**サポート範囲**のセクションでも説明しているように、Microsoft Azure では、オープン ソース テクノロジについて一般的なレベルのサポートを提供しています。HDInsight サービスでは、次に説明するいくつかのコンポーネントについてさらに高いレベルのサポートを受けることができます。

HDInsight サービスで利用できるオープン ソース コンポーネントには、2 つの種類があります。

- **組み込みコンポーネント** - これらのコンポーネントは、HDInsight クラスターにプレインストールされており、クラスターの主要な機能を提供します。たとえば、YARN リソース マネージャー、Hive クエリ言語 (HiveQL)、Mahout ライブラリなどがこのカテゴリに属します。クラスタ コンポーネントの完全な一覧は、「<a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">HDInsight で提供される Hadoop クラスター バージョンの新機能</a>」から入手できます。
- **カスタム コンポーネント** - クラスターのユーザーは、コミュニティで入手できるコンポーネントや自作のコンポーネントを、インストールするか、ワークロード内で使用することができます。

組み込みコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。

カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。たとえば、<a href ="https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight" target="_blank">HDInsight についての MSDN フォーラム</a>や <a href="http://stackoverflow.com" target="_blank">Stack Overflow</a> などの数多くのコミュニティ サイトが利用できます。また、Apache プロジェクトにも、<a href="http://apache.org" target="_blank">Apache.org</a> に <a href="http://hadoop.apache.org/" target="_blank">Hadoop</a> や <a href="http://spark.apache.org/" target="_blank">Spark</a> などのプロジェクト サイトがあります。

HDInsight サービスでは、カスタム コンポーネントを使用する方法をいくつか用意しています。コンポーネントの用途やクラスターへのインストール方法にかかわらず、同じレベルのサポートが適用されます。以下は、HDInsight クラスターでのカスタム コンポーネントの用途として、最も一般的な方法の一覧です。

1. ジョブの送信 - Hadoop や他の種類のジョブを、カスタム コンポーネントを実行または使用するクラスターに送信できます。
2. クラスターのカスタマイズ - クラスター作成時に、追加設定や、クラスター ノードにインストールするカスタム コンポーネントを指定できます。
3. サンプル - よく利用されるカスタム コンポーネントに対しては、それらを HDInsight クラスターで使用する方法について Microsoft やその他の提供者がサンプルを用意している場合があります。これらのサンプルはサポートなしで提供されます。


## 関連項目##
「[カスタム オプションを使用した HDInsight での Hadoop クラスターのプロビジョニング][hdinsight-provision-cluster]」では、その他のカスタム オプションを使用して HDInsight クラスターをプロビジョニングする方法について説明しています。

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "クラスター プロビジョニング時の段階"
 

<!---HONumber=62-->