<properties
	pageTitle="ブートストラップを使って HDInsight クラスターをカスタマイズする| Microsoft Azure"
	description="ブートストラップを使って HDInsight クラスターをカスタマイズする方法について説明します。"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="jgao"/>

# ブートストラップを使って HDInsight クラスターをカスタマイズする

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-create-windows-cluster-selector.md)]

次を含む構成ファイルを構成することが必要な場合があります。

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

再イメージ化により、クラスターは変更を保持できません。再イメージ化については、「[Role Instance Restarts Due to OS Upgrades (OS のアップグレードに伴うロール インスタンスの再起動)](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)」を参照してください。クラスターの有効期間中に変更を保持するには、作成プロセスで HDInsight クラスターのカスタマイズを使用します。これはクラスターの構成を変更するための推奨方法であり、変更は Azure 再イメージ化再起動イベントを越えて存続します。これらの構成の変更はサービスが起動される前に適用されるため、サービスを再起動する必要はありません。

ブートストラップを使用する 3 つの方法があります。

- Azure PowerShell の使用
- .NET SDK の使用
- ARM テンプレートの使用

作成時に HDInsight クラスターへの追加コンポーネントのインストールの詳細については、次を参照してください。

- [Script Action を使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)
- [Script Action を使用して HDInsight をカスタマイズする (Windows)](hdinsight-hadoop-customize-cluster.md)

## Azure PowerShell の使用

次の PowerShell コードでは、Hive 構成をカスタマイズします。

	# hive-site.xml configuration
	$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
	
	$config = New-AzureRmHDInsightClusterConfig `
		| Set-AzureRmHDInsightDefaultStorage `
			-StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
			-StorageAccountKey $defaultStorageAccountKey `
		| Add-AzureRmHDInsightConfigValues `
			-HiveSite $hiveConfigValues 
	
	New-AzureRmHDInsightCluster `
		-ResourceGroupName $existingResourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-ClusterSizeInNodes $clusterSizeInNodes `
		-ClusterType Hadoop `
		-OSType Windows `
		-Version "3.2" `
		-HttpCredential $httpCredential `
		-Config $config 

完全な作業 PowerShell スクリプトについては、「[付録 A](#hdinsight-hadoop-customize-cluster-bootstrap.md/appx-a:-powershell-sample) 」を参照してください。

**接続を確認するには:**

1. [Azure ポータル](https://portal.azure.com)にサインオンします。
2. 左側のウィンドウで、**[参照]** をクリックし、 **[HDInsight クラスター]** をクリックします。
3. PowerShell スクリプトを使用して作成したクラスターをクリックします。
4. ブレードの上部から **[ダッシュボード]** をクリックし、Ambari UI を開きます。
5. 左側のメニューで **[Hive]** をクリックします。
6. **[HiveServer2]** を **[概要]** からクリックします。
7. **[Configs]** タブをクリックします。
8. 左側のメニューで **[Hive]** をクリックします。
9. **[詳細]** タブをクリックします。
10. 下にスクロールして、**[Advanced hive-site]** を展開します。
11. セクションの **hive.metastore.client.socket.timeout** を検索します。

他の構成ファイルのカスタマイズのサンプルを次に示します。

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

	# mapred-site.xml configuration
	$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

	# oozie-site.xml configuration
	$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

詳細については、[HDInsight クラスターの作成のカスタマイズ](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx)に関する Azim Uddin のブログをご覧ください。

## .NET SDK の使用

「[.NET SDK を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-dotnet-sdk#use-bootstrap)」を参照してください。

## Azure ARM テンプレートの使用

ARM テンプレートでは、ブートストラップを使用できます。

    "configurations": {
        …
        "hive-site": {
            "hive.metastore.client.connect.retry.delay": "5",
            "hive.execution.engine": "mr",
            "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
        }
    }


![hdinsight hadoop を使用し、クラスターのブートストラップの arm テンプレートをカスタマイズする](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)



## 関連項目

- 「[HDInsight での Hadoop クラスターの作成][hdinsight-provision-cluster]」では、その他のカスタム オプションを使用して HDInsight クラスターを作成する方法について説明しています。
- [HDInsight 用の Script Action スクリプトの開発][hdinsight-write-script]
- [HDInsight クラスターで Spark をインストールして使用する][hdinsight-install-spark]
- [HDInsight クラスターに R をインストールして使用する][hdinsight-install-r]
- [HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install.md)
- [HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install.md)

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "クラスター作成時の段階"

## 付録 A: PowerShell サンプル

この PowerShell スクリプトでは、HDInsight クラスターを作成し、Hive の設定をカスタマイズします。

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<ENTER AN ALIAS>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

    $sshUserName = "sshuser" #HDInsight ssh user name
    $sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
    #endregion

    ####################################
    # Service names and varialbes
    ####################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an HDInsight cluster
    ####################################
    # Create dependent components
    ####################################
    Write-Host "Creating a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS

    $defaultStorageAccountKey = Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName |  %{ $_.Key1 }
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageContext #use the cluster name as the container name

    ####################################
    # Create a configuration object
    ####################################
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
        
    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 

    ####################################
    # Create an HDInsight cluster
    ####################################
    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    $sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
    $sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes 1 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -SshCredential $sshCredential `
        -Config $config

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

    #endregion

<!---HONumber=AcomDC_0218_2016-->