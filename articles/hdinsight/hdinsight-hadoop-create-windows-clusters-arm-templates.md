<properties
   pageTitle="ARM テンプレートを使用した HDInsight での Windows ベースの Hadoop クラスターの作成 | Microsoft Azure"
   	description="Azure ARM テンプレートを使用して Azure HDInsight のクラスターを作成する方法について説明します。"
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

# ARM テンプレートを使用した HDInsight での Windows ベースの Hadoop クラスターの作成

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-selector-create-clusters.md)]

Azure リソース マネージャー (ARM) テンプレートを使用して HDInsight クラスターを作成する方法について説明します。詳細については、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](../resource-group-template-deploy.md)」を参照してください。その他のクラスター作成のツールと機能については、このページの上部にあるタブ セレクターをクリックするか、「[クラスターの作成方法](hdinsight-provision-clusters.md#cluster-creation-methods)」を参照してください。

##前提条件:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


この記事の手順を開始する前に、次の項目を用意する必要があります。

- [Azure サブスクリプション](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- Azure PowerShell または Azure CLI

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

## ARM テンプレート

ARM テンプレートでは、アプリケーションの HDInsight クラスター、その依存リソース (既定のストレージ アカウントなど)、および他のリソース (Apache Sqoop を使用する Azure SQL Database など) の作成が 1 回の連携した操作で容易になります。テンプレートでは、アプリケーションのために必要なリソースを定義してさまざまな環境の値を入力するデプロイのパラメーターを指定します。テンプレートは、JSON、およびデプロイの値を構築するときの式で構成されます。

HDInsight クラスターと依存 Azure Storage アカウントを作成するための ARM テンプレートについては、「[付録 A](#appx-a-arm-template)」を参照してください。テキスト エディターを使用し、ワークステーション上のファイルにテンプレートを保存します。さまざまなツールを使用してテンプレートを呼び出す方法について説明します。

ARM テンプレートについて詳しくは、以下を参照してください。

- [Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates.md)
- [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](../resource-group-template-deploy.md)


## PowerShell でデプロイする

次の手順では、HDInsight クラスターを作成します。

**ARM のテンプレートを使用してクラスターをデプロイするには**

1. [付録 A](#appx-a-arm-template) の JSON ファイルをワークステーションに保存します。
2. 必要な場合は、パラメーターを設定します。
3. 次の PowerShell スクリプトを使用して、テンプレートを実行します。

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>" 
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
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

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$hdinsightClusterName";clusterStorageAccountName="$defaultStorageAccountName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

	PowerShell スクリプトは、クラスター名とストレージ アカウント名のみを構成します。ARM テンプレートのその他の値は設定可能です。
	
詳細については、「[PowerShell でデプロイする](../resource-group-template-deploy.md#deploy-with-powershell)」を参照してください。

## Azure CLI でのデプロイ

次のサンプルでは、ARM テンプレートを呼び出すことによって、クラスターとその依存ストレージ アカウントとコンテナーが作成されます。

	azure login
	azure config mode arm
    azure group create -n hdi1229rg -l "East US 2"
    azure group deployment create "hdi1229rg" "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json" -p "{"clusterName":{"value":"hdi1229win"},"clusterStorageAccountName":{"value":"hdi1229store"},"location":{"value":"East US 2"},"clusterLoginPassword":{"value":"Pass@word1"}}"





## REST API でのデプロイ

「[REST API でデプロイする](../resource-group-template-deploy.md#deploy-with-the-rest-api)」を参照してください。

## Visual Studio でのデプロイ

Visual Studio では、リソース グループ プロジェクトを作成して、それをユーザー インターフェイスから Azure にデプロイできます。プロジェクトに含めるリソースの種類を選択すると、それらのリソースがリソース マネージャー テンプレートに自動的に追加されます。プロジェクトでは、テンプレートをデプロイするための PowerShell スクリプトも提供されます。

Visual Studio とリソース グループの使用の概要については、「[Visual Studio での Azure リソース グループの作成とデプロイ](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)」を参照してください。

##次のステップ
この記事では、HDInsight クラスターを作成する方法をいくつか説明しました。詳細については、次の記事を参照してください。


- .NET クライアント ライブラリを使用したリソースのデプロイの例については、「[Deploy resources using .NET libraries and a template](../virtual-machines/virtual-machines-windows-csharp-template.md)」 (.NET ライブラリとテンプレートを使用した Azure リソースのデプロイ) を参照してください。
- アプリケーションのデプロイの詳細な例については、「[Azure でマイクロサービスを予測どおりにデプロイする](../app-service-web/app-service-deploy-complex-application-predictably.md)」を参照してください。
- ソリューションを別の環境にデプロイする方法については、「[Microsoft Azure の開発環境とテスト環境](../solution-dev-test-environments.md)」を参照してください。
- Azure Resource Manager のテンプレートのセクションについては、「[Azure Resource Manager のテンプレートの作成](../resource-group-authoring-templates.md)」を参照してください。
- Azure Resource Manager のテンプレートで使用できる関数の一覧については、「[Azure Resource Manager のテンプレートの関数](../resource-group-template-functions.md)」を参照してください。



##付録 A: ARM テンプレート

次に示す Azure リソース マネージャーのテンプレートは、Azure ストレージ アカウントに依存する Windows ベースの Hadoop クラスターを作成します。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
        "type": "string",
        "defaultValue": "East US 2",
        "allowedValues": [
            "Central US",
            "North Europe",
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Brizil South",
            "Australia East",
            "Australia Southeast",
            "Central India"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password for the cluster login."
        }
        },
        "clusterStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "The name of the storage account to be created and be used as the cluster's storage."
        }
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
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 4,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
        "variables": {},
        "resources": [
            {
            "name": "[parameters('clusterStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2015-05-01-preview",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "accountType": "[parameters('clusterStorageType')]"
            }
            },
            {
            "name": "[parameters('clusterName')]",
            "type": "Microsoft.HDInsight/clusters",
            "location": "[parameters('location')]",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
            ],
            "tags": {},
            "properties": {
                "clusterVersion": "3.2",
                "osType": "Windows",
                "clusterDefinition": {
                "kind": "hadoop",
                "configurations": {
                    "gateway": {
                    "restAuthCredential.isEnabled": true,
                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                    }
                }
                },
                "storageProfile": {
                "storageaccounts": [
                    {
                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                    "isDefault": true,
                    "container": "[parameters('clusterName')]",
                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                    }
                ]
                },
                "computeProfile": {
                "roles": [
                    {
                    "name": "headnode",
                    "targetInstanceCount": "1",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    },
                    {
                    "name": "workernode",
                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    }
                ]
                }
            }
            }
        ],
        "outputs": {
            "cluster": {
            "type": "object",
            "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
            }
        }
    }

<!---HONumber=AcomDC_0601_2016-->