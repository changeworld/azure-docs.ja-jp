<properties
   pageTitle="Azure CLI を使用した HDInsight での Windows ベースの Hadoop クラスターの作成"
   	description="Azure CLI を使用して Azure HDInsight のクラスターを作成する方法について説明します。"
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/27/2016"
   ms.author="jgao"/>

# Azure CLI を使用した HDInsight での Windows ベースの Hadoop クラスターの作成

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-selector-create-clusters.md)]

Azure CLI を使用して HDInsight クラスターを作成する方法について説明します。その他のクラスター作成のツールと機能については、このページの上部にあるタブ セレクターをクリックするか、「[クラスターの作成方法](hdinsight-provision-clusters.md#cluster-creation-methods)」を参照してください。

##前提条件:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


この記事の手順を開始する前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
- **Azure CLI**。

	[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##Connect to Azure

次のコマンドを使用して、Azure に接続します。

	azure login

職場か学校のアカウントを使用した認証の詳細については、「[Azure CLI から Azure サブスクリプションへの接続する](../xplat-cli-connect.md)」をご覧ください。

次のコマンドを使用して、ARM モードに切り替えます。

	azure config mode arm

ヘルプを取得するには、**-h** スイッチを使用します。次に例を示します。

	azure hdinsight cluster create -h

##クラスターの作成

HDInsight クラスターを作成するには、Azure リソース マネージャー (ARM)、および Azure BLOB ストレージ アカウントが必要です。HDInsight クラスターを作成するには、以下を指定する必要があります。

- **Azure リソース グループ**: Data Lake Analytics アカウントは、Azure リソース グループ内に作成する必要があります。Azure リソース マネージャーを使用すると、アプリケーション内の複数リソースを 1 つのグループと見なして作業できます。アプリケーションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、または削除できます。

	サブスクリプションのリソース グループをリストするには:

		azure group list

	新しいリソース グループを作成するには:

		azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **HDInsight クラスター名**

- **場所**: HDInsight クラスターをサポートする Azure データ センターのいずれかです。サポートされている場所のリストについては、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。

- **既定のストレージ アカウント**: HDInsight は、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには Azure ストレージ アカウントが必要です。

	新しい Azure ストレージ アカウントを作成するには:

		azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS

	> [AZURE.NOTE] ストレージ アカウントは、HDInsight と共にデータ センターに配置する必要があります。ZRS ではテーブルがサポートされないため、ストレージ アカウントの種類を ZRS にすることはできません。

	Azure ポータルを使った Azure ストレージ アカウントの作成については、「ストレージ アカウントの作成、管理、削除」 [azure-create-storageaccount] を参照してください。

	既にストレージ アカウントを持っていて、アカウント名とアカウント キーがわからない場合は、次のコマンドを使ってその情報を取得できます。

		-- Lists Storage accounts
		azure storage account list
		-- Shows a Storage account
		azure storage account show "<Storage Account Name>"
		-- Lists the keys for a Storage account
		azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

	Azure ポータルを使用して情報を取得する方法の詳細については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account#manage-your-storage-account)」の「ストレージ アカウントの管理」セクションを参照してください。

- **(省略可能) 既定の BLOB コンテナー**: **azure hdinsight cluster create** コマンドは、コンテナーが存在しない場合、コンテナーを作成します。コンテナーを事前に作成する場合は、次のコマンドを使用できます。

	azure storage container create --account-name "<Storage Account Name>" --account-key <Storage Account Key> [ContainerName]

ストレージ アカウントを用意したら、クラスターを作成する準備は整いました。


    azure hdinsight cluster create -g <Resource Group Name> -c <HDInsight Cluster Name> -l <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --workerNodeCount 2 --headNodeSize Large --workerNodeSize Large --defaultStorageAccountName <Azure Storage Account Name>.blob.core.windows.net --defaultStorageAccountKey "<Default Storage Account Key>" --defaultStorageContainer <Default Blob Storage Container> --userName admin --password "<HTTP User Password>" --rdpUserName <RDP Username> --rdpPassword "<RDP User Password" --rdpAccessExpiry "03/01/2016"


##構成ファイルを使用するクラスターの作成
通常は、HDInsight クラスターを作成し、そのクラスター上でジョブを実行してから、クラスターを削除してコストを削減します。コマンド ライン インターフェイスでは構成をファイルに保存することもできるため、クラスターを作成するたびにその構成を再利用できます。

	azure hdinsight config create [options ] <Config File Path> <overwirte>
	azure hdinsight config add-config-values [options] <Config File Path>
	azure hdinsight config add-script-action [options] <Config File Path>

例: クラスターを作成するときに実行するスクリプト アクションを含む構成ファイルの作成

	azure hdinsight config create "C:\myFiles\configFile.config"
	azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"
	azure hdinsight cluster create --configurationPath "C:\myFiles\configFile.config"

##スクリプト アクションを使用したクラスターの作成

クラスターを作成するときに実行するスクリプト アクションを含む構成ファイルの作成

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"

スクリプト アクションを使用したクラスターの作成

	azure hdinsight cluster create -g myarmgroup01 -l westus -y Windows --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01


スクリプト アクションの一般情報については、「[Script Action を使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md)」をご覧ください。


## ARM テンプレートを使用したクラスターの作成

CLI を使用して、ARM テンプレートを呼び出すことによってクラスターを作成することができます。「[Azure CLI でのデプロイ](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-azure-cli)」を参照してください。

## 関連項目

- [Azure HDInsight の概要](hdinsight-hadoop-linux-tutorial-get-started.md) - HDInsight クラスターの使用方法について説明しています。
- [HDInsight での Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md) - プログラムを使用して HDInsight にジョブを送信する方法について説明しています。
- [Azure CLI を使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-command-line.md)
- [Azure サービス管理での Mac、Linux、および Windows 用 Azure CLI の使用](../virtual-machines-command-line-tools.md)

<!---HONumber=AcomDC_0601_2016-->