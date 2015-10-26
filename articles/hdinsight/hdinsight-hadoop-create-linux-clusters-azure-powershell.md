<properties
   	pageTitle="Azure CLI を使用した HDInsight 用の Linux ベースの Hadoop、HBase、または Storm クラスターの作成 | Microsoft Azure"
   	description="Azure CLI を使用して、HDInsight 用の Linux ベースの Hadoop、HBase、または Storm クラスターを作成する方法について説明します。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="10/14/2015"
   	ms.author="nitinme"/>

#Azure PowerShell を使用した HDInsight の Linux ベースのクラスターの作成

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell は、Azure のワークロードのデプロイと管理を制御し自動化するために使用できる強力なスクリプティング環境です。このドキュメントでは、Azure PowerShell を使用して Linux ベースの HDInsight クラスターをプロビジョニングする方法について説明し、スクリプト例を示します。

> [AZURE.NOTE]Azure PowerShell は、Windows クライアントだけで利用できます。Linux、Unix、または Mac OS X クライアントを使用している場合は、Azure CLI によるクラスターの作成について、「[Azure CLI を使用した Linux ベースの HDInsight クラスターの作成](hdinsight-hadoop-create-linux-cluster-azure-cli.md)」を参照してください。

##前提条件

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

- __Azure PowerShell__。ワークステーションを構成して HDInsight Windows Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成](../install-configure-powershell.md)」をご覧ください。HDInsight で Azure PowerShell を使用する方法の詳細については、「[PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)」をご覧ください。HDInsight Windows PowerShell コマンドレットの一覧については、「[HDInsight コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/dn858087.aspx)」をご覧ください。

##クラスターを作成する

Azure PowerShell を使用して HDInsight クラスターをプロビジョニングするには、以下の手順が必要です。

- Azure リソース グループの作成
- Azure のストレージ アカウントの作成
- Azure BLOB コンテナーの作成
- HDInsight クラスターの作成

Linux クラスターをプロビジョニングするために設定する必要がある最も重要な 2 つのパラメーターは、OS の種類と SSH ユーザーの詳細を指定するパラメーターです。

- **-OSType** パラメーターを **Linux** として指定していることを確認します。
- クラスターのリモート セッションで SSH を使用するには、SSH ユーザー パスワードまたは SSH 公開キーのいずれかを指定します。SSH ユーザー パスワードと SSH 公開キーを両方とも指定すると、キーが無視されます。リモート セッションで SSH キーを使用する場合は、入力を求められたら、空の SSH パスワードを指定する必要があります。HDInsight での SSH の使用方法の詳細については、次の記事をご覧ください。
    
    * [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

次のスクリプトでは、新しいクラスターを作成する方法を示します。

    # Use the new Azure Resource Manager mode
    Switch-AzureMode AzureResourceManager

    ###########################################
    # Create required items, if none exist
    ###########################################

    # Sign in
    Add-AzureAccount

    # Select the subscription to use
    $subscriptionName = "<SubscriptionName>"        # Provide your Subscription Name
    Select-AzureSubscription -SubscriptionName $subscriptionName

    # Register your subscription to use HDInsight
    Register-AzureProvider -ProviderNamespace "Microsoft.HDInsight" -Force

    # Create an Azure Resource Group
    $resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
    $location = "<Location>"                        # For example, "West US"
    New-AzureResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account
    $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
    New-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

    # Create an Azure Blob Storage container
    $containerName = "<ContainerName>"              # Provide a container name
    $storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    New-AzureStorageContainer -Name $containerName -Context $destContext

    ###########################################
    # Create an HDInsight Cluster
    ###########################################

    # Skip these variables if you just created them
    $resourceGroupName = "<ResourceGroupName>"      # Provide the Resource Group name
    $storageAccountName = "<StorageAcccountName>"   # Provide the Storage account name
    $containerName = "<ContainerName>"              # Provide the container name
    $storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }

    # Set these variables
    $clusterName = $containerName           		# As a best practice, have the same name for the cluster and container
    $clusterNodes = <ClusterSizeInNodes>    		# The number of nodes in the HDInsight cluster
    $credentials = Get-Credential
    $sshCredentials = Get-Credential

    # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
    $location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

    # Create a new HDInsight cluster
    New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -OSType Linux -Version "3.2" -SshCredential $sshCredentials

> [AZURE.NOTE]**$clusterCredentials** に指定する値は、クラスターの Hadoop ユーザー アカウントの作成に使用されます。このアカウントを使用してクラスターに接続します。**$sshCredentials** に指定する値は、クラスターの SSH ユーザーの作成に使用されます。このアカウントを使用してクラスターでリモート SSH セッションを開始し、ジョブを実行します。Azure ポータルから [簡易作成] オプションを使用してクラスターをプロビジョニングする場合、既定の Hadoop ユーザー名は "admin"、既定の SSH ユーザー名は "hdiuser" になります。

プロビジョニングの完了までに、最大で 15 分かかることがあります。

##次のステップ

HDInsight クラスターが正常に作成されました。次に、クラスターの使用方法について、以下のトピックをご覧ください。

###Hadoop クラスター

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

###HBase クラスター

* [HDInsight での Hbase の使用](hdinsight-hbase-tutorial-get-stared-linux.md)
* [HDInsight での HBase の Java アプリケーションの開発](hdinsight-hbase-build-java-maven-linux)

###Storm クラスター

* [HDInsight での Storm の Java トポロジの開発](hdinsight-storm-develop-java-topology.md)
* [HDInsight での Storm の Python コンポーネントの使用](hdinsight-storm-develop-python.md)
* [HDInsight での Storm を使用したトポロジのデプロイと監視](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=Oct15_HO3-->