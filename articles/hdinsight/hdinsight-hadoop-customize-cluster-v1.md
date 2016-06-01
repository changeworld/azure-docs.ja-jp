<properties 
	pageTitle="スクリプト アクションを使った HDInsight クラスターのカスタマイズ | Microsoft Azure" 
	description="スクリプト アクションを使って HDInsight クラスターをカスタマイズする方法について説明します。" 
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
	ms.date="05/17/2016" 
	ms.author="nitinme"/>

# スクリプト アクションを使って HDInsight クラスターをカスタマイズする

> [AZURE.IMPORTANT] このドキュメントの手順では、Azure クラシック ポータルを使用します。新しいサービスを作成するときは、クラシック ポータルを使用しないことをお勧めします。Azure ポータルの利点の詳細については、「[Microsoft Azure ポータル](https://azure.microsoft.com/features/azure-portal/)」を参照してください。
>
> このドキュメントでは、Azure PowerShell と HDInsight .NET SDK の使用方法についても説明します。ここで示すスニペットは、Azure サービス管理 (ASM) を使用して HDInsight で機能するコマンドに基づいていますが、これらのコマンドは__推奨されていません__。これらのコマンドは、2017 年 1 月 1 日までに削除される予定です。
>
>Azure Resource Manager (ARM) を使用する PowerShell と HDInsight .NET SDK スニペットと共に、Azure ポータルを使用するこのドキュメントのバージョンについては、「[スクリプト アクションを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md)」を参照してください。

HDInsight には、カスタム スクリプトを呼び出す **スクリプト アクション**という構成オプションがあります。スクリプト アクションは、プロビジョニング プロセス中にクラスター上で実行されるカスタマイズを定義します。これらのスクリプトを使用して、クラスター上に追加のソフトウェアをインストールしたり、クラスター上のアプリケーションの構成を変更したりできます。


> [AZURE.NOTE] スクリプト アクションは、HDInsight クラスター バージョン 3.1 以降では、Windows オペレーティング システムでのみサポートされます。HDInsight クラスター バージョンの詳細については、「[HDInsight クラスター バージョン](hdinsight-component-versioning.md)」を参照してください。
> 
> スクリプト アクションは標準の Azure HDInsight のサブスクリプションの一部として追加料金なしで利用可能です。

HDInsight クラスターは、その他さまざまな方法でカスタマイズできます。たとえば、Azure ストレージ アカウントの追加、Hadoop 構成ファイルの変更 (core-site.xml、hive-site.xml など)、クラスター内の一般的な場所への共有ライブラリの追加 (Hive、Oozie など) といった方法があります。これらのカスタマイズは、Azure PowerShell、Azure HDInsight .NET SDK、Azure クラシック ポータルから実行できます。詳細については、「[カスタム オプションを使用した HDInsight での Hadoop クラスターのプロビジョニング][hdinsight-provision-cluster]」を参照してください。

## クラスターのプロビジョニング処理でのスクリプト アクション

スクリプト アクションは、クラスターが作成中にのみ使用されます。次の図は、プロビジョニング処理中にスクリプト アクションが実行された場合を示しています。

![クラスター プロビジョニング時の HDInsight クラスターのカスタマイズと段階][img-hdi-cluster-states]

スクリプトの実行中、クラスターは **ClusterCustomization** 段階に入ります。この段階でスクリプトは、システム管理者アカウントで、ノードで完全な管理者権限を持ち、クラスター内のすべての指定したノードで並列的に実行されます。

> [AZURE.NOTE] **ClusterCustomization** 段階ではクラスター ノードで管理者権限を持っているため、スクリプトを使用して、Hadoop 関連のサービスを含め、サービスの停止や開始などの操作を行うことができます。そのため、スクリプトの一部として、スクリプトの完了前に Ambari サービスや他の Hadoop 関連のサービスが動作していることを確認する必要があります。これらのサービスでは、クラスターの作成時にクラスターが正常に稼動しているか確認する必要があります。これらのサービスに影響するクラスター上の構成を変更する場合は、用意されているヘルパー関数を使用する必要があります。ヘルパー関数の詳細については、「[HDInsight 用のスクリプト アクションのスクリプトを開発する][hdinsight-write-script]」を参照してください。

スクリプトの出力とエラー ログは、クラスター用に指定した既定のストレージ アカウントに格納されます。ログは、**u<\\cluster-name-fragment><\\time-stamp>setuplog** という名前のテーブルに格納されます。これらは、クラスター内のすべてのノード (ヘッドノードとワーカー ノード) で実行されるスクリプトから取得される集計ログです。


各クラスターは、指定された順序で呼び出される複数のスクリプト アクションを受け取ることができます。スクリプトはヘッド ノード、ワーカー ノード、またはその両方で実行できます。

## スクリプト アクションのスクリプトを呼び出す

スクリプト アクションのスクリプトは、Azure クラシック ポータル、Azure PowerShell、または HDInsight .NET SDK から使用できます。

HDInsight は、HDInsight クラスターで、次のコンポーネントをインストールするためのいくつかのスクリプトを提供します。

名前 | スクリプト
----- | -----
**Spark のインストール** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1「[HDInsight クラスターで Spark をインストールして使用する][hdinsight-install-spark]」を参照してください。
**R のインストール** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1[HDInsight クラスターでの R のインストールと使用][hdinsight-install-r]に関するページを参照してください。
**Solr のインストール** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1「[HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install.md)」を参照してください。
- **Giraph のインストール** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1「[HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install.md)」を参照してください。



**Azure クラシック ポータルから以下の操作を実行します。**

1. 「[カスタム オプションを使用したクラスターのプロビジョニング](hdinsight-provision-clusters.md#portal)」に関するページの説明に従い、**[カスタム作成]** オプションを使用してプロビジョニングを開始します。 
2. ウィザードの **[スクリプトのアクション]** ページで、**[スクリプト アクションの追加]** をクリックし、次に示すように、スクリプト アクションの詳細を指定します。

	![スクリプト アクションを使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-customize-cluster-v1/HDI.CustomProvision.Page6.png "スクリプト アクションを使ってクラスターをカスタマイズする")
	
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
	</table>

	複数のスクリプト操作を追加して、クラスターに複数のコンポーネントをインストールすることができます。

3. チェック マークをクリックして、クラスターのプロビジョニングを開始します。
  
**Azure PowerShell コマンドレットから**

HDInsight の Azure PowerShell コマンドを使用して、1 つまたは複数のスクリプト アクションを実行します。**<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** コマンドレットを使用して、カスタム スクリプトを呼び出します。これらのコマンドレットを使用するには Azure PowerShell をインストールし、構成しておく必要があります。コンピューターを構成して HDInsight Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成](../powershell-install-configure.md)」を参照してください。

次の Azure PowerShell コマンドを使用して、HDInsight クラスターのデプロイ時に、1 つのスクリプト アクションを実行します。

	$config = New-AzureHDInsightClusterConfig –ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName –Uri http://uri.to/scriptaction.ps1 –Parameters MyScriptActionParameter -ClusterRoleCollection HeadNode,DataNode

	New-AzureHDInsightCluster -Config $config

次の Azure PowerShell コマンドを使用して、HDInsight クラスターのデプロイ時に、複数のスクリプト アクションを実行します。

	$config = New-AzureHDInsightClusterConfig –ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName1 –Uri http://uri.to/scriptaction1.ps1 –Parameters MyScriptAction1Parameters -ClusterRoleCollection HeadNode,DataNode | Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName2 –Uri http://uri.to/scriptaction2.ps1 -Parameters MyScriptAction2Parameters -ClusterRoleCollection HeadNode

	New-AzureHDInsightCluster -Config $config

**HDInsight .NET SDK から**

HDInsight .NET SDK は、カスタム スクリプトを呼び出す <a href="http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx" target="_blank">ScriptAction</a> クラスを提供します。HDInsight .NET SDK を使用するには:

1. Visual Studio アプリケーションを作成し、NuGet から SDK をインストールします。**[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。コンソールで次のコマンドを実行して、パッケージをインストールします。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

2. SDK を使用してクラスターを作成します。手順については、「[.NET SDK を使用した HDInsight クラスターのプロビジョニング](hdinsight-provision-clusters.md#sdk)」に関するページを参照してください。

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



## HDInsight クラスターで使用するオープン ソース ソフトウェアのサポート
Microsoft Azure HDInsight サービスは柔軟性に優れたプラットフォームであり、Hadoop を中心に形成されたオープン ソース テクノロジのエコシステムを利用し、クラウド内でビッグ データ アプリケーションを構築できます。<a href="http://azure.microsoft.com/support/faq/" target="_blank">Azure サポート FAQ Web サイト</a>の**サポート範囲**のセクションでも説明しているように、Microsoft Azure では、オープン ソース テクノロジについて一般的なレベルのサポートを提供しています。HDInsight サービスでは、次に説明するいくつかのコンポーネントについてさらに高いレベルのサポートを受けることができます。

HDInsight サービスで利用できるオープン ソース コンポーネントには、2 つの種類があります。

- **組み込みコンポーネント** - これらのコンポーネントは、HDInsight クラスターにプレインストールされており、クラスターの主要な機能を提供します。たとえば、YARN リソース マネージャー、Hive クエリ言語 (HiveQL)、Mahout ライブラリなどがこのカテゴリに属します。クラスター コンポーネントの完全な一覧は、「[HDInsight で提供される Hadoop クラスター バージョンの新機能](hdinsight-component-versioning.md)」から入手できます。
- **カスタム コンポーネント** - クラスターのユーザーは、コミュニティで入手できるコンポーネントや自作のコンポーネントを、インストールするか、ワークロード内で使用することができます。

組み込みコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。

カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。たとえば、<a href ="https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight" target="_blank">HDInsight についての MSDN フォーラム</a>や <a href="http://stackoverflow.com" target="_blank">Stack Overflow</a> などの数多くのコミュニティ サイトが利用できます。また、Apache プロジェクトにも、<a href="http://apache.org" target="_blank">Apache.org</a> に <a href="http://hadoop.apache.org/" target="_blank">Hadoop</a> や <a href="http://spark.apache.org/" target="_blank">Spark</a> などのプロジェクト サイトがあります。

HDInsight サービスでは、カスタム コンポーネントを使用する方法をいくつか用意しています。コンポーネントの用途やクラスターへのインストール方法にかかわらず、同じレベルのサポートが適用されます。以下は、HDInsight クラスターでのカスタム コンポーネントの用途として、最も一般的な方法の一覧です。

1. ジョブの送信 - Hadoop や他の種類のジョブを、カスタム コンポーネントを実行または使用するクラスターに送信できます。
2. クラスターのカスタマイズ - クラスター作成時に、追加設定や、クラスター ノードにインストールするカスタム コンポーネントを指定できます。
3. サンプル - よく利用されるカスタム コンポーネントに対しては、それらを HDInsight クラスターで使用する方法について Microsoft やその他の提供者がサンプルを用意している場合があります。これらのサンプルはサポートなしで提供されます。

## スクリプト アクションのスクリプトを開発する

「[HDInsight 用のスクリプト アクションのスクリプトを開発する][hdinsight-write-script]」を参照してください。


## 関連項目

- 「[カスタム オプションを使用した HDInsight での Hadoop クラスターのプロビジョニング][hdinsight-provision-cluster]」では、その他のカスタム オプションを使用して HDInsight クラスターをプロビジョニングする方法について説明しています。
- [HDInsight 用のスクリプト アクションのスクリプトを開発する][hdinsight-write-script]
- [HDInsight クラスターで Spark をインストールして使用する][hdinsight-install-spark]
- [HDInsight クラスターに R をインストールして使用する][hdinsight-install-r]
- [HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install.md)
- [HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install.md)

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-v1/HDI-Cluster-state.png "クラスター プロビジョニング時の段階"
 

<!---HONumber=AcomDC_0518_2016-->