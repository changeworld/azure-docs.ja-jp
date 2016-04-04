<properties
	pageTitle="Script Action を使った HDInsight クラスターのカスタマイズ | Microsoft Azure"
	description="スクリプト アクションを使用して Linux ベースの HDInsight クラスターにカスタム コンポーネントを追加する方法について説明します。スクリプト アクションは、クラスター ノード上の Bash スクリプトであり、クラスター構成のカスタマイズや、サービスとユーティリティ (Hue、Solr、R など) の追加に使用できます。"
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
	ms.date="03/17/2016"
	ms.author="larryfr"/>

# Script Action を使用して Linux ベースの HDInsight クラスターをカスタマイズする

HDInsight には、クラスターをカスタマイズするカスタム スクリプトを呼び出す**スクリプト アクション**という構成オプションがあります。こうしたスクリプトは、クラスターの作成時、または既に実行されているクラスターで使用できるほか、その他のコンポーネントのインストールや構成設定の変更にも使用されます。

> [AZURE.NOTE] 既に実行されているクラスター上でスクリプト アクションを使用する機能は、Linux ベースの HDInsight クラスターでのみ使用できます。Windows ベースのクラスターでスクリプト アクションを使用する方法の詳細については、「[Script Action を使用して Windows ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md)」を参照してください。

## スクリプト アクションについて

スクリプト アクションは、URL とパラメーターの指定先の Bash スクリプトにすぎず、HDInsight クラスター ノードで実行されます。スクリプト アクションの特性と機能を次に示します。

* ヘッド ノードや worker ノードなど、__特定のノード タイプでのみ実行__できます。

* __保存__することも、__アドホック__に使うこともできます。

    __保存済み__スクリプトは、worker ノードに適用されるスクリプトで、クラスターのスケールアップ時に作成された新しいノードで自動的に実行されます。

    保存済みスクリプトによって、ヘッド ノードなどの別のタイプのノードに変更が適用されることもありますが、機能の観点から見た場合、スクリプトを保存する唯一の理由は、クラスターのスケールアウト時に作成された新しい worker ノードに適用されるようにすることです。

    > [AZURE.IMPORTANT] 保存済みスクリプト アクションには一意の名前が必要です。

    __アドホック__ スクリプトは保存されませんが、後でアドホック スクリプトを保存済みスクリプトに昇格したり、保存済みスクリプトをアドホック スクリプトに降格したりできます。

    > [AZURE.IMPORTANT] クラスターの作成時に使用されるスクリプト アクションは自動的に保存されます。
    >
    > 失敗したスクリプトは、保存するように指定した場合でも保存されません。

* 実行中にスクリプトによって使用される__パラメーター__を受け取ることができます。

* クラスター ノードで__ルート レベルの権限__を使用して実行されます。

* __Azure ポータル__、__Azure PowerShell__、または __HDInsight .NET SDK__ で使用できます。

> [AZURE.IMPORTANT] スクリプト アクションで行われた変更を自動的に元に戻すことはできません。スクリプトの影響を元に戻す必要がある場合は、どのような変更が行われたかを特定し、手動で戻す必要があります (または、変更を元に戻すスクリプト アクションを用意します)。

クラスターに適用されているスクリプトを把握しやすいように、また、昇格または降格対象のスクリプトの ID を確認しやすいように、クラスターには、実行されたすべてのスクリプトの履歴が保持されます。

### クラスターの作成処理での Script Action

クラスターの作成時に使用されるスクリプト アクションは、既存のクラスターで実行されるスクリプト アクションとは少し異なります。

* このスクリプトは、__自動的に保存__されます。

* スクリプトの__エラー__によって、クラスターの作成プロセスが失敗することがあります。

次の図は、作成処理中に Script Action が実行された場合を示しています。

![クラスター作成時の HDInsight クラスターのカスタマイズと段階][img-hdi-cluster-states]

スクリプトは HDInsight の構成中に実行されます。この段階で、スクリプトは、クラスター内の指定されたすべてのノードで並列して実行され、それらのノードに対するルート権限で実行されます。

> [AZURE.NOTE] スクリプトはルート レベルの権限でクラスター ノードで実行されるため、Hadoop 関連のサービスを含め、サービスの停止や開始などの操作を行うことができます。サービスを停止する場合は、スクリプトの実行が完了する前に Ambari サービスや他の Hadoop 関連のサービスが稼働していることを確認する必要があります。これらのサービスでは、クラスターの作成時にクラスターが正常に稼動しているかどうかを確認する必要があります。

クラスターの作成時に、指定された順序で呼び出される複数のスクリプト アクションを指定できます。

> [AZURE.IMPORTANT] スクリプト アクションは 60 分以内に完了する必要があります。そうしないと、タイムアウトします。クラスターのプロビジョニング中に、スクリプトは他のセットアップ プロセスや構成プロセスと同時に実行されます。CPU 時間やネットワーク帯域幅などのリソースの競合が原因で、開発環境の場合よりスクリプトの完了に時間がかかる場合があります。
>
> スクリプトの実行時間を最小限に抑えるために、ソースからアプリケーションをダウンロードしてコンパイルするなどのタスクを実行しないようにしてください。代わりに、アプリケーションを事前にコンパイルし、バイナリを Azure BLOB ストレージに格納して、クラスターにすばやくダウンロードできるようにします。

###実行中のクラスターでのスクリプト アクション

クラスターの作成時に使用されるスクリプトとは異なり、既に実行中のクラスターで実行されたスクリプトのエラーによって、クラスターの状態が自動的に失敗に変更されることはありません。スクリプトが完了すると、クラスターは "実行中" 状態に戻ります。

> [AZURE.IMPORTANT] これは、実行中のクラスターが、正しくない処理を実行するスクリプトの影響を受けないという意味ではありません。たとえば、スクリプトによって、クラスターに必要なファイルが削除されたり、構成が変更されたりした結果、サービスのエラーなどが発生することがあります。
>
> スクリプト アクションはルート権限で実行されるため、スクリプトをクラスターに適用する前に、そのスクリプトによる処理を必ず理解しておく必要があります。

スクリプトをクラスターに適用すると、スクリプトが適切であれば、クラスターの状態は __[実行中]__ から __[受理されました]__、__[HDInsight の構成]__ の順に変わり、最後に __[実行中]__ に戻ります。スクリプトの状態はスクリプト アクションの履歴に記録されます。そのログを使用して、スクリプトが成功したか失敗したかを確認できます。たとえば、`Get-AzureRmHDInsightScriptActionHistory` PowerShell コマンドレットを使用して、スクリプトの状態を表示できます。このコマンドレットは、次のような情報を返します。

    ScriptExecutionId : 635918532516474303
    StartTime         : 2/23/2016 7:40:55 PM
    EndTime           : 2/23/2016 7:41:05 PM
    Status            : Succeeded

## スクリプト アクションのサンプル スクリプト

Script Action のスクリプトは、Azure ポータル、Azure PowerShell、または HDInsight .NET SDK から使用できます。HDInsight は、HDInsight クラスターで次のコンポーネントをインストールするためのスクリプトを提供します。

名前 | スクリプト
----- | -----
**Hue のインストール** | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh「[HDInsight クラスターに Hue をインストールして使用する](hdinsight-hadoop-hue-linux.md)」を参照してください。
**R のインストール** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh[HDInsight クラスターでの R のインストールと使用](hdinsight-hadoop-r-scripts-linux.md)に関するページを参照してください。
**Solr のインストール** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh「[HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install-linux.md)」を参照してください。
**Giraph のインストール** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh「[HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install-linux.md)」を参照してください。
| **Hive ライブラリの事前読み込み** | https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh「[HDInsight クラスター作成時の Hive ライブラリの追加](hdinsight-hadoop-add-hive-libraries.md)」を参照してください。 |

## クラスターの作成時にスクリプト アクションを使用する

このセクションでは、HDInsight クラスターの作成時にスクリプト アクションをさまざまな方法で使用する例を紹介します。ここでは、Azure ポータル、ARM テンプレート、PowerShell コマンドレット、.NET SDK からスクリプト アクションを使用しています。

### クラスターの作成時に Azure ポータルからスクリプト アクションを使用する

1. 「[HDInsight で Hadoop クラスターを作成する](hdinsight-provision-clusters.md#portal)」の説明に基づき、クラスターの作成を開始します。

2. __[オプションの構成]__ の **[スクリプト アクション]** ブレードで、**[スクリプト アクションの追加]** をクリックし、次に示すように、スクリプト アクションの詳細を指定します。

	![Script Action を使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png)

	| プロパティ | 値 |
	| -------- | ----- |
	| 名前 | スクリプト アクションの名前を指定します。 |
	| スクリプト URI | クラスターのカスタマイズのために呼び出されるスクリプトへの URI を指定します。 |
	| ヘッド/ワーカー | カスタマイズ スクリプトが実行されるノード (**[ヘッド]**、**[ワーカー]**、または **[ZooKeeper]**) を指定します。 |
	| パラメーター | スクリプトで必要な場合は、パラメーターを指定します。 |

	クラスターに複数のコンポーネントをインストールするには、Enter キーを押して複数のスクリプト アクションを追加します。

3. **[選択]** をクリックして構成を保存し、クラスターの作成を続行します。

### Azure リソース マネージャーのテンプレートからスクリプト アクションを使用する

このセクションでは、Azure リソース マネージャー (ARM) テンプレートを使用して HDInsight クラスターを作成します。また、スクリプト アクションを使用してクラスターにカスタム コンポーネント (この例では R) をインストールします。このセクションでは、スクリプト アクションを使用してクラスターを作成する ARM テンプレートの例を示します。

#### 開始する前に

* コンピューターを構成して HDInsight Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成](../powershell-install-configure.md)」をご覧ください。
* ARM テンプレートを作成する方法の手順については、「[Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates.md)」を参照してください。
* リソース マネージャーで Azure PowerShell を使用したことがない場合は、「[Azure リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

#### スクリプト アクションを使用してクラスターを作成する

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
		            "defaultValue": "username"
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

		Add-AzureRmAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. 複数のサブスクリプションがある場合、デプロイメントに使用するサブスクリプション ID を提供します。

		Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] `Get-AzureRmSubscription` を使用して、アカウントに関連付けられているすべてのサブスクリプションのリストを取得することができます。これに、各サブスクリプション ID が含まれています。

5. 既存のリソース グループがない場合は、新しいリソース グループを作成します。ソリューションに必要なリソース グループと場所の名前を指定します。新しいリソース グループの概要が返されます。

		New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"

		ResourceGroupName : myresourcegroup
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		            		Actions  NotActions
		            		=======  ==========
		            		*
		ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. リソース グループに新しいデプロイを作成するには、**New-AzureRmResourceGroupDeployment** コマンドを実行して必要なパラメーターを指定します。パラメーターにはデプロイメントの名前、リソース グループの名前、作成したテンプレートへのパスまたは URL が含まれます。テンプレートでパラメーターが必要な場合は、それらのパラメーターも渡す必要があります。この場合は、クラスターに R をインストールするスクリプト アクションでパラメーターは必要ありません。


		New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


	テンプレートで定義されているパラメーターの値を指定するよう要求されます。

7. リソース グループをデプロイすると、デプロイの概要が表示されます。

		  DeploymentName    : mydeployment
		  ResourceGroupName : myresourcegroup
		  ProvisioningState : Succeeded
		  Timestamp         : 8/17/2015 7:00:27 PM
		  Mode              : Incremental
		  ...

8. デプロイメントに失敗した場合は、次のコマンドレットを使用してエラーに関する情報を取得できます。

		Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

### クラスターの作成時に Azure PowerShell からスクリプト アクションを使用する

このセクションでは、[Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) コマンドレットで、スクリプト アクションを使用し、クラスターのカスタマイズを行うスクリプトを呼び出します。次に進む前に、Azure PowerShell をインストールして構成したことを確認します。ワークステーションを構成して HDInsight PowerShell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成](../powershell-install-configure.md)」を参照してください。

次の手順に従います。

1. Azure PowerShell コンソールを開き、次のコマンドレットを使用して Azure サブスクリプションにログインし、PowerShell 変数をいくつか宣言します。

        # LOGIN TO ZURE
        Login-AzureRmAccount

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionId = "<SubscriptionId>"		# ID of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
        $resourceGroupName = "<ResourceGroupName>"      # The resource group that the HDInsight cluster will be created in

2. クラスター内のノードや使用する既定のストレージなどの構成値を指定します。

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureRmSubscription -SubscriptionId $subscriptionId
		$config = New-AzureRmHDInsightClusterConfig
		$config.DefaultStorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccountKey=$storageAccountKey

3. **Add-AzureRmHDInsightScriptAction** コマンドレットを使用してスクリプトを呼び出します。次の例では、クラスターに R をインストールするスクリプトを使用します。

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION FOR HEADNODE AND WORKERNODE
		$config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType WorkerNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh

	**Add-AzureRmHDInsightScriptAction** コマンドレットは、次のパラメーターを受け取ります。

	| パラメーター | 定義 |
	| --------- | ---------- |
	| Config | スクリプト アクション情報が追加される構成オブジェクト。 |
	| 名前 | スクリプト アクションの名前。 |
	| NodeType | カスタマイズ スクリプトが実行されるノードを指定します。有効な値は **HeadNode** (ヘッド ノードにインストールする場合)、**WorkerNode** (すべてのデータ ノードにインストールする場合)、**ZookeeperNode** (zookeeper ノードにインストールする場合) です。 |
	| パラメーター | スクリプトで必要なパラメーター。 |
	| Uri | 実行されるスクリプトへの URI を指定します。 |

4. クラスターの admin/HTTPS ユーザーを設定します。

        $httpCreds = get-credential

    プロンプトが表示されたら、名前に「admin」と入力し、パスワードを入力します。

5. SSH 資格情報を設定します。

        $sshCreds = get-credential

    プロンプトが表示されたら、SSH ユーザー名とパスワードを入力します。パスワードではなく証明書を使用して SSH アカウントをセキュリティで保護する場合は、パスワードを空にして、使用する証明書の公開キーの内容を `$sshPublicKey` に設定します。次に例を示します。

        $sshPublicKey = Get-Content .\path\to\public.key -Raw

4. 最後に、クラスターを作成します。

        New-AzureRmHDInsightCluster -config $config -clustername $clusterName -DefaultStorageContainer $containerName -Location $location -ResourceGroupName $resourceGroupName -ClusterSizeInNodes $clusterNodes -HttpCredential $httpCreds -SshCredential $sshCreds -OSType Linux

    SSH アカウントをセキュリティで保護するために公開キーを使用する場合、パラメーターとして `-SshPublicKey $sshPublicKey` も指定する必要があります。

クラスターが作成されるまでに数分かかる場合があります。

### クラスターの作成時に HDInsight .NET SDK からスクリプト アクションを使用する

HDInsight .NET SDK は、.NET アプリケーションから HDInsight を簡単に操作できるクライアント ライブラリを提供します。コード サンプルについては、「[.NET SDK を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action)」を参照してください。

## 実行中のクラスターにスクリプト アクションを適用する

このセクションでは、実行中の HDInsight クラスターにさまざまな方法でスクリプト アクションを適用する例を紹介します。ここでは、Azure ポータル、PowerShell コマンドレット、.NET SDK を通じてスクリプト アクションを適用しています。

### 実行中のクラスターに Azure ポータルからスクリプト アクションを適用する

1. [Azure ポータル](https://portal.azure.com)で HDInsight クラスターを選択します。

2. HDInsight クラスター ブレードで、__[設定]__ を選択します。

    ![Settings icon](./media/hdinsight-hadoop-customize-cluster-linux/settingsicon.png)

3. [設定] ブレードで、__[スクリプト アクション]__ を選択します。

    ![Script Actions link](./media/hdinsight-hadoop-customize-cluster-linux/settings.png)

4. [スクリプト アクション] ブレードの上部で、__[新規で送信]__ を選択します。

    ![Submit new icon](./media/hdinsight-hadoop-customize-cluster-linux/newscriptaction.png)

5. [スクリプト アクションの追加] ブレードで、次の情報を入力します。

    * __[名前]__: このスクリプト アクションに使用する表示名。この例では、`R` です。
    * __[スクリプト URI]__: スクリプトの URI。この例では、`https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh` です。
    * __[ヘッド]__、__[ワーカー]__、および __[Zookeeper]__: このスクリプトを適用するノードをオンにします。この例では [ヘッド] と [ワーカー] がオンになっています。
    * __[パラメーター]__: スクリプトがパラメーターを受け取る場合は、そのパラメーターをここに入力します。
    * __[保存済み]__: クラスターをスケールアップするときに、新しい worker ノードに適用されるようにスクリプトを保存する場合は、このエントリをオンにします。

6. 最後に、__[作成]__ ボタンを使用して、スクリプトをクラスターに適用します。

### 実行中のクラスターに Azure PowerShell からスクリプト アクションを適用する

次に進む前に、Azure PowerShell をインストールして構成したことを確認します。ワークステーションを構成して HDInsight PowerShell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成](../powershell-install-configure.md)」を参照してください。

1. Azure PowerShell コンソールを開き、次のコマンドレットを使用して Azure サブスクリプションにログインし、PowerShell 変数をいくつか宣言します。

        # LOGIN TO ZURE
        Login-AzureRmAccount

		# PROVIDE VALUES FOR THESE VARIABLES
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
        $saName = "<ScriptActionName>"                  # Name of the script action
        $saURI = "<URI to the script>"                  # The URI where the script is located
        $nodeTypes = "headnode", "workernode"


2. 次のコマンドを使用して、スクリプトをクラスターに適用します。

        Submit-AzureRmHDInsightScriptAction -ClusterName $clusterName -Name $saName -Uri $saURI -NodeTypes $nodeTypes -PersistOnSuccess

    ジョブが完了すると、次のような情報が表示されます。

        OperationState  : Succeeded
        ErrorMessage    :
        Name            : R
        Uri             : https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
        Parameters      :
        NodeTypes       : {HeadNode, WorkerNode}

### 実行中のクラスターに HDInsight .NET SDK からスクリプト アクションを適用する

.NET SDK を使用してスクリプトをクラスターに適用する例については、[https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action) を参照してください。

## 履歴を表示して、スクリプト アクションを昇格、降格する

### Azure ポータルの使用

1. [Azure ポータル](https://portal.azure.com)で HDInsight クラスターを選択します。

2. HDInsight クラスター ブレードで、__[設定]__ を選択します。

    ![Settings icon](./media/hdinsight-hadoop-customize-cluster-linux/settingsicon.png)

3. [設定] ブレードで、__[スクリプト アクション]__ を選択します。

    ![Script Actions link](./media/hdinsight-hadoop-customize-cluster-linux/settings.png)

4. 保存済みスクリプトの一覧と、クラスターに適用されたスクリプトの履歴が、[スクリプト アクション] ブレードに表示されます。次のスクリーンショットでは、Solr スクリプトがこのクラスターで実行されていますが、保存されたスクリプト アクションはありません。

    ![Script Actions blade](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionhistory.png)

5. 履歴からスクリプトを選択すると、このスクリプトの [プロパティ] ブレードが表示されます。ブレードの上部で、スクリプトを再実行または昇格できます。

    ![Script actions properties blade](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionproperties.png)

6. [スクリプト アクション] ブレードのエントリの右側にある __[...]__ を使用して、再実行、保存、(保存済みアクションに対する) 削除などのアクションを実行することもできます。

    ![Script actions ... usage](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### Azure PowerShell の使用

| 使用 | 目的 |
| ----- | ----- |
| Get-AzureRmHDInsightPersistedScriptAction | 保存済みスクリプト アクションの情報を取得します |
| Get-AzureRmHDInsightScriptActionHistory | クラスターに適用されたスクリプト アクションの履歴、または特定のスクリプトの詳細を取得します |
| Set-AzureRmHDInsightPersistedScriptAction | アドホック スクリプト アクションを保存済みスクリプト アクションに昇格します |
| Remove-AzureRmHDInsightPersistedScriptAction | 保存済みスクリプト アクションをアドホック アクションに降格します |

> [AZURE.IMPORTANT] `Remove-AzureRmHDInsightPersistedScriptAction` を使用しても、スクリプトによって実行されたアクションは元に戻されません。クラスターに追加された新しい worker ノードに対してスクリプトが実行されないように、保存済みフラグが削除されるだけです。

次のスクリプトの例では、昇格のコマンドレットを使用してから、スクリプトを降格しています。

    # Get a history of scripts
    Get-AzureRmHDInsightScriptActionHistory -ClusterName mycluster

    # From the list, we want to get information on a specific script
    Get-AzureRmHDInsightScriptActionHistory -ClusterName mycluster -ScriptExecutionId 635920937765978529

    # Promote this to a persisted script
    # Note: the script must have a unique name to be promoted
    # if the name is not unique, you will receive an error
    Set-AzureRmHDInsightPersistedScriptAction -ClusterName mycluster -ScriptExecutionId 635920937765978529

    # Demote the script back to ad hoc
    # Note that demotion uses the unique script name instead of
    # execution ID.
    Remove-AzureRmHDInsightPersistedScriptAction -ClusterName mycluster -Name "Install Giraph"

### HDInsight .NET SDK を使用する場合

.NET SDK を使用してクラスターからスクリプトの履歴を取得し、スクリプトを昇格または降格する例については、[TBD]() を参照してください。

## トラブルシューティング

Ambari の Web UI を使用すると、スクリプト アクションによってログに記録された情報を表示できます。スクリプトを使用してクラスターを作成しているときに、そのスクリプトで発生したエラーが原因でクラスターの作成に失敗した場合は、クラスターに関連付けられた既定のストレージ アカウントのログを利用することもできます。このセクションでは、これら両方のオプションを使用してログを取得する方法について説明します。

### Ambari Web UI を使用する

1. ブラウザーで、https://CLUSTERNAME.azurehdinsight.net にアクセスします。CLUSTERNAME を、使用する HDInsight クラスターの名前に置き換えます。

	プロンプトが表示されたら、クラスターの管理者アカウント名 (admin) とパスワードを入力します。Web フォームで管理者の資格情報の再入力が必要な場合があります。

2. ページ上部のバーから __[OPS]__ エントリを選択します。これにより、Ambari を使用してクラスターで実行される、現在と過去の操作の一覧が表示されます。

	![Ambari Web UI バーで OPS を選択](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. __[Operations]__ 列で __run\_customscriptaction__ エントリを探します。これらは、スクリプト アクションの実行時に作成されます。

	![操作のスクリーンショット](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

	このエントリを選択してリンクをたどると、クラスターでスクリプトを実行したときに生成される STDOUT と STDERR の出力が表示されます。

### 既定のストレージ アカウントからログにアクセスする

スクリプトで発生したエラーが原因でクラスターの作成に失敗した場合、クラスターに関連付けられた既定のストレージ アカウントからスクリプト アクション ログに直接アクセスすることもできます。

* ストレージ ログは、`\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` にあります。

	![操作のスクリーンショット](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

	この下で、ヘッド ノード、ワーカー ノード、および zookeeper ノードごとにログが整理されています。次に例をいくつか示します。
	* **ヘッド ノード** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`
	* **worker ノード** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`
	* **Zookeeper ノード** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* 対応するホストのすべての stdout と stderr が、ストレージ アカウントにアップロードされます。各スクリプト アクションに対して、**output-*.txt** と **errors-*.txt** が 1 つずつあります。output-*.txt ファイルには、ホストで実行されたスクリプトの URI に関する情報が含まれます。たとえば、次のように入力します。

		'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* 同じ名前のスクリプト アクション クラスターを繰り返し作成できます。そのような場合は、DATE フォルダー名に基づいて適切なログを識別できます。たとえば、異なる日付で作成されるクラスターのフォルダー構造 (mycluster) は、次のようになります。
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04`
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* 同じ日に同じ名前のスクリプト アクション クラスターを作成する場合は、一意のプレフィックスを使用して該当するログ ファイルを識別できます。

* 1 日の最後にクラスターを作成すると、ログ ファイルが 2 日間にまたがる場合があります。そのような場合は、同じクラスターに日付が異なる 2 つのフォルダーが作成されます。

* 既定のコンテナーへのログ ファイルのアップロードは、特に大きなクラスターの場合、最大 5 分かかることがあります。そのため、ログにアクセスする必要がある場合は、スクリプト アクションが失敗したときにクラスターをすぐに削除しないでください。


## HDInsight クラスターで使用するオープン ソース ソフトウェアのサポート

Microsoft Azure HDInsight サービスは柔軟性に優れたプラットフォームであり、Hadoop を中心に形成されたオープン ソース テクノロジのエコシステムを利用し、クラウド内でビッグ データ アプリケーションを構築できます。[Azure サポート FAQ Web サイト](https://azure.microsoft.com/support/faq/)の**サポート範囲**のセクションでも説明しているように、Microsoft Azure では、オープン ソース テクノロジについて一般的なレベルのサポートを提供しています。HDInsight サービスでは、次に説明するいくつかのコンポーネントについてさらに高いレベルのサポートを受けることができます。

HDInsight サービスで利用できるオープン ソース コンポーネントには、2 つの種類があります。

- **組み込みコンポーネント** - これらのコンポーネントは、HDInsight クラスターにプレインストールされており、クラスターの主要な機能を提供します。たとえば、YARN リソース マネージャー、Hive クエリ言語 (HiveQL)、Mahout ライブラリなどがこのカテゴリに属します。クラスター コンポーネントの完全な一覧は、「[HDInsight で提供される Hadoop クラスター バージョンの新機能](hdinsight-component-versioning.md)」から入手できます。

- **カスタム コンポーネント** - クラスターのユーザーは、コミュニティで入手できるコンポーネントや自作のコンポーネントを、インストールするか、ワークロード内で使用することができます。

> [AZURE.WARNING] HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。たとえば、[HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/ja-JP/home?forum=hdinsight)や [http://stackoverflow.com](http://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。また、Apache プロジェクトには、[http://apache.org](http://apache.org) に [Hadoop](http://hadoop.apache.org/) などのプロジェクト サイトがあります。

HDInsight サービスでは、カスタム コンポーネントを使用する方法をいくつか用意しています。コンポーネントの用途やクラスターへのインストール方法にかかわらず、同じレベルのサポートが適用されます。以下は、HDInsight クラスターでのカスタム コンポーネントの用途として、最も一般的な方法の一覧です。

1. ジョブの送信 - Hadoop や他の種類のジョブを、カスタム コンポーネントを実行または使用するクラスターに送信できます。

2. クラスターのカスタマイズ - クラスター作成時に、追加設定や、クラスター ノードにインストールするカスタム コンポーネントを指定できます。

3. サンプル - よく利用されるカスタム コンポーネントに対しては、それらを HDInsight クラスターで使用する方法について Microsoft やその他の提供者がサンプルを用意している場合があります。これらのサンプルはサポートなしで提供されます。

##トラブルシューティング

###クラスターの作成時に使用されたスクリプトが履歴に表示されません

クラスターが 2016 年 3 月 15 日より前に作成された場合、クラスターの作成時に使用されたスクリプトのスクリプト アクション履歴にエントリが表示されない可能性があります。ただし、2016 年 3 月 15 日より後にそのクラスターのサイズを変更すると、スクリプトはサイズ変更操作の一部としてクラスター内の新しいノードに適用されるため、クラスター作成時のスクリプトの使用は履歴に表示されます。

ただし、例外が 2 つあります。

* クラスターが 2015 年 9 月 1 日より前に作成された場合。スクリプト アクションが導入される前であるため、この日付より前に作成されたクラスターについては、作成時にスクリプト アクションを使用できませんでした。

* クラスターの作成時に複数のスクリプト アクションを使用した場合、複数のスクリプトに対して同じ名前を使用した場合、または複数のスクリプトに対して同じ名前と URI、異なるパラメーターを使用した場合。この場合は、次のエラーが発生します。

    既存のスクリプトでスクリプト名が競合するため、このクラスターで新しいスクリプト アクションを実行できません。クラスターの作成時に指定されるスクリプト名はすべて一意である必要があります。既存のスクリプトは、サイズ変更時に引き続き実行されます。

## 次のステップ

クラスターをカスタマイズするスクリプトの作成と使用に関する情報と例については、次を参照してください。

- [HDInsight 用の Script Action スクリプトの開発](hdinsight-hadoop-script-actions-linux.md)
- [HDInsight クラスターに R をインストールして使用する](hdinsight-hadoop-r-scripts-linux.md)
- [HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install-linux.md)
- [HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "クラスター作成時の段階"

<!---HONumber=AcomDC_0323_2016-->