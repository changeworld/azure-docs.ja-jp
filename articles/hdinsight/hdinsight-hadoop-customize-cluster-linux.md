<properties
	pageTitle="Script Action を使った HDInsight クラスターのカスタマイズ | Microsoft Azure"
	description="Script Action を使って HDInsight クラスターをカスタマイズする方法について説明します。"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2015"
	ms.author="larryfr"/>

# Script Action を使って HDInsight クラスターをカスタマイズする

HDInsight は、カスタム スクリプトを呼び出す **Script Action** という構成オプションを提供します。Script Action はプロビジョニング処理中にクラスター上で実行されるカスタマイズを定義します。これらのスクリプトを使用して、クラスター上に追加のソフトウェアをインストールしたり、クラスター上のアプリケーションの構成を変更したりできます。

> [AZURE.NOTE]この記事の情報は、Linux ベースの HDInsight クラスターに固有のものです。この記事の Windows ベースのクラスターに固有のバージョンについては、「[スクリプト アクションを使用して HDInsight クラスターをカスタマイズする (Windows)](hdinsight-hadoop-customize-cluster.md)」を参照してください。

## クラスターのプロビジョニング処理での Script Action

Script Action は、クラスターが作成中にのみ使用されます。次の図は、プロビジョニング処理中に Script Action が実行された場合を示しています。

![クラスター プロビジョニング時の HDInsight クラスターのカスタマイズと段階][img-hdi-cluster-states]

スクリプトは HDInsight の構成中に実行されます。この段階で、スクリプトは、クラスター内の指定されたすべてのノードで並列して実行され、それらのノードに対するルート権限で実行されます。

> [AZURE.NOTE]スクリプトの実行時にクラスター ノードに対するルート権限を持っているため、Hadoop 関連のサービスを含め、サービスの停止や開始などの操作を行うことができます。サービスを停止する場合は、スクリプトの実行が完了する前に Ambari サービスや他の Hadoop 関連のサービスが稼働していることを確認する必要があります。これらのサービスでは、クラスターの作成時にクラスターが正常に稼動しているか確認する必要があります。

各クラスターは、指定された順序で呼び出される複数の Script Action を受け取ることができます。スクリプトはヘッド ノード、ワーカー ノード、またはその両方で実行できます。

> [AZURE.IMPORTANT]スクリプト アクションは 15 分以内に完了する必要があります。そうしないと、タイムアウトします。

## スクリプト アクションのサンプル スクリプト

Script Action のスクリプトは、Azure プレビュー ポータル、Azure PowerShell、または HDInsight .NET SDK から使用できます。この記事では、ポータルから Script Action を使用する方法を示します。PowerShell と .NET SDK で Script Action を使用する方法については、次の表に示されている例を参照してください。

HDInsight は、HDInsight クラスターで、次のコンポーネントをインストールするためのいくつかのスクリプトを提供します。

名前 | スクリプト
----- | -----
**Hue のインストール** | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh[HDInsight クラスターでの Hue のインストールと使用](hdinsight-hadoop-hue-linux.md)に関するページを参照してください。
**Spark のインストール** | https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv01/spark-installer-v01.sh[HDInsight クラスターでの Spark のインストールと使用](hdinsight-hadoop-spark-install-linux.md)に関するページを参照してください。
**R のインストール** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh[HDInsight クラスターでの R のインストールと使用](hdinsight-hadoop-r-scripts-linux.md)に関するページを参照してください。
**Solr のインストール** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh[HDInsight クラスターでの Solr のインストールと使用](hdinsight-hadoop-solr-install-linux.md)に関するページを参照してください。
**Giraph のインストール** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh[HDInsight クラスターでの Giraph のインストールと使用](hdinsight-hadoop-giraph-install-linux.md)に関するページを参照してください。

## Azure プレビュー ポータルからスクリプト アクションを使用する

1. [カスタム オプションを使用したクラスターのプロビジョニング](hdinsight-provision-clusters.md#portal)に関するページの説明に従って、クラスターのプロビジョニングを開始します。

2. __[オプションの構成]__ の **[スクリプト アクション]** ブレードで、**[スクリプト アクションの追加]** をクリックし、次に示すように、スクリプト アクションの詳細を指定します。

	![Script Action を使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png "Script Action を使ってクラスターをカスタマイズする")

	| プロパティ | 値 |
	| -------- | ----- |
	| 名前 | スクリプト アクションの名前を指定します。 |
	| スクリプト URI | クラスターのカスタマイズのために呼び出されるスクリプトへの URI を指定します。 |
	| ヘッド/ワーカー | カスタマイズ スクリプトが実行されるノード (**[ヘッド]**、**[ワーカー]**、または **[ZooKeeper]**) を指定します。 |
	| パラメーター | スクリプトで必要な場合は、パラメーターを指定します。 |

	クラスターに複数のコンポーネントをインストールするには、Enter キーを押して複数のスクリプト アクションを追加します。

3. **[選択]** をクリックしてスクリプト アクションの構成を保存し、クラスターのプロビジョニングを続行します。

## Azure リソース マネージャーのテンプレートからスクリプト アクションを使用する

このセクションでは、Azure リソース マネージャー (ARM) テンプレートを使用して HDInsight クラスターをプロビジョニングします。また、スクリプト アクションを使用してクラスターにカスタム コンポーネント (この例では R) をインストールします。このセクションでは、スクリプト アクションを使用してクラスターをプロビジョニングする ARM テンプレートの例を示します。

### 開始する前に

* コンピューターを構成して HDInsight Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成](../powershell-install-configure.md)」をご覧ください。
* ARM テンプレートを作成する方法の手順については、「[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。
* リソース マネージャーで Azure PowerShell を使用したことがない場合は、「[Azure リソース マネージャーでの Azure PowerShell の使用](powershell-azure-resource-manager)」を参照してください。

### スクリプト アクションを使用してクラスターをプロビジョニングする

1. コンピューター上の場所に次のテンプレートをコピーします。このテンプレートにより、クラスター内のヘッド ノードとワーカー ノードに R がインストールされます。JSON テンプレートが有効かどうかも確認できます。テンプレートの内容を [JSONLint](http://jsonlint.com/) というオンラインの JSON 検証ツールに貼り付けます。

			{
		    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
		    "contentVersion": "1.0.0.0",
		    "parameters": {
		        "clusterLocation": {
		            "type": "string",
		            "defaultValue": "West US",
		            "allowedValues": [ "West US" ]
		        },
		        "clusterName": {
		            "type": "string"
		        },
		        "clusterUserName": {
		            "type": "string",
		            "defaultValue": "admin"
		        },
		        "clusterUserPassword": {
		            "type": "securestring"
		        },
		        "sshUserName": {
		            "type": "string",
		            "defaultValue": "hdiuser"
		        },
		        "sshPassword": {
		            "type": "securestring"
		        },
		        "clusterStorageAccountName": {
		            "type": "string"
		        },
		        "clusterStorageAccountResourceGroup": {
		            "type": "string"
		        },
		        "clusterStorageType": {
		            "type": "string",
		            "defaultValue": "Standard_LRS",
		            "allowedValues": [
		                "Standard_LRS",
		                "Standard_GRS",
		                "Standard_ZRS"
		            ]
		        },
		        "clusterStorageAccountContainer": {
		            "type": "string"
		        },
		        "clusterHeadNodeCount": {
		            "type": "int",
		            "defaultValue": 1
		        },
		        "clusterWorkerNodeCount": {
		            "type": "int",
		            "defaultValue": 2
		        }
		    },
		    "variables": {
		    },
		    "resources": [
		        {
		            "name": "[parameters('clusterStorageAccountName')]",
		            "type": "Microsoft.Storage/storageAccounts",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-05-01-preview",
		            "dependsOn": [ ],
		            "tags": { },
		            "properties": {
		                "accountType": "[parameters('clusterStorageType')]"
		            }
		        },
		        {
		            "name": "[parameters('clusterName')]",
		            "type": "Microsoft.HDInsight/clusters",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-03-01-preview",
		            "dependsOn": [
		                "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
		            ],
		            "tags": { },
		            "properties": {
		                "clusterVersion": "3.2",
		                "osType": "Linux",
		                "clusterDefinition": {
		                    "kind": "hadoop",
		                    "configurations": {
		                        "gateway": {
		                            "restAuthCredential.isEnabled": true,
		                            "restAuthCredential.username": "[parameters('clusterUserName')]",
		                            "restAuthCredential.password": "[parameters('clusterUserPassword')]"
		                        }
		                    }
		                },
		                "storageProfile": {
		                    "storageaccounts": [
		                        {
		                            "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
		                            "isDefault": true,
		                            "container": "[parameters('clusterStorageAccountContainer')]",
		                            "key": "[listKeys(resourceId(parameters('clusterStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).key1]"
		                        }
		                    ]
		                },
		                "computeProfile": {
		                    "roles": [
		                        {
		                            "name": "headnode",
		                            "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        },
		                        {
		                            "name": "workernode",
		                            "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        }
		                    ]
		                }
		            }
		        }
		    ],
		    "outputs": {
		        "cluster":{
		            "type" : "object",
		            "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
		        }
		    }
		}

2. Azure PowerShell を起動し、Azure アカウントにログインします。資格情報を提供すると、コマンドがアカウントの情報を返します。

		Add-AzureAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. 複数のサブスクリプションがある場合、デプロイに使用するサブスクリプション ID を提供します。

		Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

4. Azure リソース マネージャー モジュールに切り替える

		Switch-AzureMode AzureResourceManager

5. 既存のリソース グループがない場合は、新しいリソース グループを作成します。ソリューションに必要なリソース グループと場所の名前を指定します。新しいリソース グループの概要が返されます。

		New-AzureResourceGroup -Name myresourcegroup -Location "West US"

		ResourceGroupName : myresourcegroup
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		            		Actions  NotActions
		            		=======  ==========
		            		*
		ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. リソース グループに新しいデプロイを作成するには、**New-AzureResourceGroupDeployment** コマンドを実行して必要なパラメーターを指定します。パラメーターにはデプロイの名前、リソース グループの名前、作成したテンプレートへのパスまたは URL が含まれます。テンプレートでパラメーターが必要な場合は、それらのパラメーターも渡す必要があります。この場合は、クラスターに R をインストールするスクリプト アクションでパラメーターは必要ありません。


		New-AzureResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


	テンプレートで定義されているパラメーターの値を指定するよう要求されます。

7. リソース グループをデプロイすると、デプロイの概要が表示されます。

		  DeploymentName    : mydeployment
		  ResourceGroupName : myresourcegroup
		  ProvisioningState : Succeeded
		  Timestamp         : 8/17/2015 7:00:27 PM
		  Mode              : Incremental
		  ...

8. デプロイに失敗した場合は、次のコマンドレットを使用してエラーに関する情報を取得できます。

		Get-AzureResourceGroupLog -ResourceGroup myresourcegroup -Status Failed

	デプロイ エラーに関する詳細については、次のコマンドレットを使用します。

		Get-AzureResourceGroupLog -ResourceGroup myresourcegroup -Status Failed -DetailedOutput

## Azure PowerShell からスクリプト アクションを使用する

このセクションでは、**<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptActionScript Action</a>** コマンドレットで、スクリプト アクションを使用し、クラスターのカスタマイズを行うスクリプトを呼び出します。次に進む前に、Azure PowerShell をインストールして構成したことを確認します。コンピューターを構成して HDInsight Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成](../powershell-install-configure.md)」をご覧ください。

次の手順に従います。

1. Azure PowerShell コンソールを開き、次の変数を宣言します。

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # HDInsight version, for example "3.1"

2. クラスター内のノードや使用する既定のストレージなどの構成値を指定します。

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName

3. **Add-AzureHDInsightScriptAction** コマンドレットを使用してスクリプトを呼び出します。次の例では、クラスターに R をインストールするスクリプトを使用します。

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install R"  -ClusterRoleCollection HeadNode,WorkerNode,ZookeeperNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh


	**Add-AzureHDInsightScriptAction** コマンドレットは、次のパラメーターを受け取ります。

	| パラメーター | 定義 |
	| --------- | ---------- |
	| Config | スクリプト アクション情報が追加される構成オブジェクト。 |
	| 名前 | スクリプト アクションの名前。 |
	| ClusterRoleCollection | カスタマイズ スクリプトが実行されるノードを指定します。有効な値は **HeadNode** (ヘッド ノードにインストールする場合)、**WorkerNode** (すべてのデータ ノードにインストールする場合)、**ZookeeperNode** (zookeeper ノードにインストールする場合) です。いずれかまたはすべての値を使用できます。 |
	| パラメーター | スクリプトで必要なパラメーター。 |
	| Uri | 実行されるスクリプトへの URI を指定します。 |

4. 最後にクラスターをプロビジョニングします。

		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

入力を求められたら、クラスターの資格情報を入力します。クラスターが作成されるまでに数分かかる場合があります。

## HDInsight .NET SDK からスクリプト アクションを使用する

HDInsight .NET SDK は、.NET アプリケーションから HDInsight を簡単に操作できるクライアント ライブラリを提供します。次の手順では、スクリプトを使用して HDInsight .NET SDK からクラスターをカスタマイズする方法を示します。

> [AZURE.IMPORTANT]最初に自己署名証明書を作成し、それをワークステーションにインストールして、Azure サブスクリプションにアップロードする必要があります。手順については、「[Create a self-signed certificate (自己署名証明書の作成)](http://go.microsoft.com/fwlink/?LinkId=511138)」を参照してください。


### Visual Studio プロジェクトを作成する

1. Visual Studio 2013 または 2015 を開きます。

2. **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。

3. **[新しいプロジェクト]** で、次の値を入力するか、選択します。

	| プロパティ | 値 |
	| -------- | ----- |
	| カテゴリ | テンプレート/Visual C#/Windows |
	| テンプレート | コンソール アプリケーション |
	| 名前 | ScriptActionCluster |

4. **[OK]** をクリックしてプロジェクトを作成します。

5. **[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

6. コンソールで次のコマンドを実行して、パッケージをインストールします。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	このコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトから追加します。

7. **ソリューション エクスプローラー**で **[Program.cs]** をダブルクリックして開きます。

8. 次の **using** ステートメントをファイルの先頭に追加します。

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;

9. **Main()** 関数に次のコードを貼り付けた後、変数の値を指定します。

        var clusterName = args[0];

        // PROVIDE VALUES FOR THE VARIABLES
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // PROVIDE THE CERTIFICATE THUMBPRINT TO RETRIEVE THE CERTIFICATE FROM THE CERTIFICATE STORE
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // CREATE AN HDINSIGHT CLIENT OBJECT
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;

        // PROVIDE THE CLUSTER INFORMATION
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.1"
        };

10. **Main()** 関数に次のコードを追加します。このコードによりスクリプト アクションが呼び出されます。この例では、クラスターに R をインストールするスクリプトが呼び出されます。

		// ADD THE SCRIPT ACTION TO INSTALL R

        clusterInfo.ConfigActions.Add(new ScriptAction(
            "Install R",
            new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode },
            new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), null
            ));

11. 最後に、クラスターを作成します。

		client.CreateCluster(clusterInfo);

11. アプリケーションに変更を保存し、ソリューションをビルドします。

### アプリケーションの実行

Azure PowerShell コンソールを開き、プロジェクトを保存した場所に移動し、プロジェクト内の \\bin\\debug ディレクトリに移動して、次のコマンドを実行します。

	.\ScriptActionCluster <cluster-name>

クラスター名を指定し、Enter キーを押して、クラスターをプロビジョニングします。

## HDInsight クラスターで使用するオープン ソース ソフトウェアのサポート

Microsoft Azure HDInsight サービスは柔軟性に優れたプラットフォームであり、Hadoop を中心に形成されたオープン ソース テクノロジのエコシステムを利用し、クラウド内でビッグ データ アプリケーションを構築できます。[Azure サポート FAQ Web サイト](http://azure.microsoft.com/support/faq/)の**サポート範囲**のセクションでも説明しているように、Microsoft Azure では、オープン ソース テクノロジについて一般的なレベルのサポートを提供しています。HDInsight サービスでは、次に説明するいくつかのコンポーネントについてさらに高いレベルのサポートを受けることができます。

HDInsight サービスで利用できるオープン ソース コンポーネントには、2 つの種類があります。

- **組み込みコンポーネント** - これらのコンポーネントは、HDInsight クラスターにプレインストールされており、クラスターの主要な機能を提供します。たとえば、YARN リソース マネージャー、Hive クエリ言語 (HiveQL)、Mahout ライブラリなどがこのカテゴリに属します。クラスター コンポーネントの完全な一覧は、「[HDInsight で提供される Hadoop クラスター バージョンの新機能](hdinsight-component-versioning.md)」から入手できます。

- **カスタム コンポーネント** - クラスターのユーザーは、コミュニティで入手できるコンポーネントや自作のコンポーネントを、インストールするか、ワークロード内で使用することができます。

> [AZURE.WARNING]HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。たとえば、[MSDN の HDInsight フォーラム](https://social.msdn.microsoft.com/Forums/azure/ja-JP/home?forum=hdinsight)や [http://stackoverflow.com](http://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。また、Apache プロジェクトには、[http://apache.org](http://apache.org) に [Hadoop](http://hadoop.apache.org/) や [Spark](http://spark.apache.org/) などのプロジェクト サイトがあります。

HDInsight サービスでは、カスタム コンポーネントを使用する方法をいくつか用意しています。コンポーネントの用途やクラスターへのインストール方法にかかわらず、同じレベルのサポートが適用されます。以下は、HDInsight クラスターでのカスタム コンポーネントの用途として、最も一般的な方法の一覧です。

1. ジョブの送信 - Hadoop や他の種類のジョブを、カスタム コンポーネントを実行または使用するクラスターに送信できます。

2. クラスターのカスタマイズ - クラスター作成時に、追加設定や、クラスター ノードにインストールするカスタム コンポーネントを指定できます。

3. サンプル - よく利用されるカスタム コンポーネントに対しては、それらを HDInsight クラスターで使用する方法について Microsoft やその他の提供者がサンプルを用意している場合があります。これらのサンプルはサポートなしで提供されます。

## トラブルシューティング

Ambari の Web UI を使用すると、クラスターのプロビジョニング中に、スクリプトによってログに記録された情報を表示できます。

1. ブラウザーで https://CLUSTERNAME.azurehdinsight.net にアクセスします。CLUSTERNAME を、使用する HDInsight クラスターの名前に置き換えます。

	プロンプトが表示されたら、クラスターの管理者アカウント名 (admin) とパスワードを入力します。Web フォームで管理者の資格情報の再入力が必要な場合があります。

2. ページ上部のバーから __[OPS]__ エントリを選択します。これにより、Ambari を使用してクラスターで実行される、現在と過去の操作の一覧が表示されます。

	![OPS が選択されている Ambari Web UI バー](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. __Operations__ 列で __run\_customscriptaction__ を含むエントリを探します。これらは、スクリプト アクションの実行時に作成されます。

	![操作のスクリーンショット](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

	このエントリを選択してリンクをたどると、クラスターでスクリプトを実行したときに生成される STDOUT と STDERR の出力が表示されます。

## 次のステップ

クラスターをカスタマイズするスクリプトの作成と使用に関する情報と例については、次を参照してください。

- [HDInsight 用の Script Action スクリプトの開発](hdinsight-hadoop-script-actions-linux.md)
- [HDInsight クラスターで Spark をインストールして使用する](hdinsight-hadoop-spark-install-linux.md)
- [HDInsight クラスターに R をインストールして使用する](hdinsight-hadoop-r-scripts-linux.md)
- [HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install-linux.md)
- [HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "クラスター プロビジョニング時の段階"

<!---HONumber=Oct15_HO1-->