<properties
   pageTitle="Azure Resource Manager テンプレートを使用した HDInsight での Linux ベースの Hadoop クラスターの作成 | Microsoft Azure"
   	description="Azure Resource Manager テンプレートを使用して Azure の Azure HDInsight のクラスターを作成する方法について説明します。"
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/02/2016"
   ms.author="jgao"/>

# Azure Resource Manager テンプレートを使用して、HDInsight での Linux ベースの Hadoop クラスターを作成する

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-selector-create-clusters.md)]

Azure リソース マネージャー (ARM) テンプレートを使用して HDInsight クラスターを作成する方法について説明します。詳細については、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](../resource-group-template-deploy.md)」を参照してください。その他のクラスター作成のツールと機能については、このページの上部にあるタブ セレクターをクリックするか、「[クラスターの作成方法](hdinsight-provision-clusters.md#cluster-creation-methods)」を参照してください。

##前提条件:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

この記事の手順を開始する前に、次の項目を用意する必要があります。

- [Azure サブスクリプション](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- Azure PowerShell/Azure CLI

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

## リソース マネージャーのテンプレート

Resource Manager テンプレートでは、アプリケーションの HDInsight クラスター、その依存リソース (既定のストレージ アカウントなど)、および他のリソース (Apache Sqoop を使用する Azure SQL Database など) の作成が 1 回の連携した操作で容易になります。テンプレートでは、アプリケーションのために必要なリソースを定義してさまざまな環境の値を入力するデプロイのパラメーターを指定します。テンプレートは、JSON、およびデプロイの値を構築するときの式で構成されます。

HDInsight クラスターと依存 Azure Storage アカウントを作成するための Resource Manager テンプレートについては、「[付録 A](#appx-a-arm-template)」を参照してください。クロスプラットフォームの [VSCode](https://code.visualstudio.com/#alt-downloads) と [Resource Manager の拡張機能](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)またはテキスト エディターを併用して、テンプレートをワークステーションのファイルに保存してください。テンプレートを呼び出すさまざまな方法について説明します。

Resource Manager テンプレートの詳細については、以下を参照してください

- [Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates.md)
- [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](../resource-group-template-deploy.md)

特定の要素の JSON スキーマを確認するには、次の手順に従います:

1. [Azure Portal](https://porta.azure.com) を開き、HDInsight クラスターを作成します。「[Azure Portal を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-portal.md)」を参照してください。
2. 必要な要素、および JSON スキーマを必要とする要素を構成します。
3. **[作成]** をクリックする前に、次のスクリーン ショットにのように **[自動化オプション]** をクリックします:

    ![HDInsight Hadoop で、クラスターのResource Manager テンプレート スキーマの自動化オプションが作成されます](./media/hdinsight-hadoop-create-linux-clusters-arm-templates/hdinsight-create-cluster-resource-manager-template-automation-option.png)

    ポータルでは、構成に基づいた Resource Manager テンプレートが作成されます。
## PowerShell でデプロイする

次の手順では、Linux ベースの HDInsight クラスターを作成します。

**Resource Manager テンプレートを使用してクラスターをデプロイするには**

1. [付録 A](#appx-a-arm-template) の JSON ファイルをワークステーションに保存します。以下の PowerShell スクリプトでは、ファイル名を *C:\\HDITutorials-ARM\\hdinsight-arm-template.json* にしています。
2. 必要に応じてパラメーターと変数を設定します。
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
        $parameters = @{clusterName="$hdinsightClusterName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

	この PowerShell スクリプトは、クラスター名のみを構成します。ストレージ アカウント名はテンプレートにハードコーディングされています。クラスターのユーザー パスワード (デフォルトのユーザー名は *admin*)、および SSH ユーザー パスワード (デフォルトの SSH ユーザー名は*sshuser*) の入力が求められます。
	
詳細については、「[PowerShell でデプロイする](../resource-group-template-deploy.md#deploy-with-powershell)」を参照してください。

## Azure CLI でのデプロイ

次のサンプルでは、Resource Manager テンプレートを呼び出すことによって、クラスターとその依存ストレージ アカウントとコンテナーが作成されます:

	azure login
	azure config mode arm
    azure group create -n hdi1229rg -l "East US"
    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json"
    
クラスター名、クラスターのユーザー パスワード (デフォルトのユーザー名は *admin*)、および SSH ユーザー パスワード (デフォルトの SSH ユーザー名は*sshuser*) の入力が求められます。次のようにインライン パラメーターで指定します:

    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "c:\Tutorials\HDInsightARM\create-linux-based-hadoop-cluster-in-hdinsight.json" --parameters '{"clusterName":{"value":"hdi1229"},"clusterLoginPassword":{"value":"Pass@word1"},"sshPassword":{"value":"Pass@word1"}}'

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

##付録 A: Resource Manager テンプレート

次に示す Azure リソース マネージャーのテンプレートは、Azure ストレージ アカウントに依存する Linux ベースの Hadoop クラスターを作成します。

> [AZURE.NOTE] このサンプルには、Hive metastore と Oozie metastore の構成情報が含まれます。セクションを削除するか、テンプレートを使用する前にセクションを構成します。

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
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
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "sshUserName": {
        "type": "string",
        "defaultValue": "sshuser",
        "metadata": {
            "description": "These credentials can be used to remotely access the cluster."
        }
        },
        "sshPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "location": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
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
            "Australia East",
            "Australia Southeast"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterType": {
        "type": "string",
        "defaultValue": "hadoop",
        "allowedValues": [
            "hadoop",
            "hbase",
            "storm",
            "spark"
        ],
        "metadata": {
            "description": "The type of the HDInsight cluster to create."
        }
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 2,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
    "variables": {
        "defaultApiVersion": "2015-05-01-preview",
        "clusterApiVersion": "2015-03-01-preview",
        "clusterStorageAccountName": "[concat(parameters('clusterName'),'store')]"
    },
    "resources": [
        {
        "name": "[variables('clusterStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": { },
        "properties": {
            "accountType": "Standard_LRS"
        }
        },
        {
        "name": "[parameters('clusterName')]",
        "type": "Microsoft.HDInsight/clusters",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('clusterApiVersion')]",
        "dependsOn": [ "[concat('Microsoft.Storage/storageAccounts/',variables('clusterStorageAccountName'))]" ],
        "tags": {

        },
        "properties": {
            "clusterVersion": "3.4",
            "osType": "Linux",
            "tier": "standard",
            "clusterDefinition": {
            "kind": "[parameters('clusterType')]",
            "configurations": {
                "gateway": {
                "restAuthCredential.isEnabled": true,
                "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                },
                "hive-site": {
                    "javax.jdo.option.ConnectionDriverName": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "javax.jdo.option.ConnectionURL": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "javax.jdo.option.ConnectionUserName": "johndole",
                    "javax.jdo.option.ConnectionPassword": "myPassword$"
                },
                "hive-env": {
                    "hive_database": "Existing MSSQL Server database with SQL authentication",
                    "hive_database_name": "myhive20160901",
                    "hive_database_type": "mssql",
                    "hive_existing_mssql_server_database": "myhive20160901",
                    "hive_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "hive_hostname": "myadla0901dbserver.database.windows.net"
                },
                "oozie-site": {
                    "oozie.service.JPAService.jdbc.driver": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "oozie.service.JPAService.jdbc.url": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "oozie.service.JPAService.jdbc.username": "johndole",
                    "oozie.service.JPAService.jdbc.password": "myPassword$",
                    "oozie.db.schema.name": "oozie"
                },
                "oozie-env": {
                    "oozie_database": "Existing MSSQL Server database with SQL authentication",
                    "oozie_database_name": "myhive20160901",
                    "oozie_database_type": "mssql",
                    "oozie_existing_mssql_server_database": "myhive20160901",
                    "oozie_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "oozie_hostname": "myadla0901dbserver.database.windows.net"
                }            
            }
            },
            "storageProfile": {
            "storageaccounts": [
                {
                "name": "[concat(variables('clusterStorageAccountName'),'.blob.core.windows.net')]",
                "isDefault": true,
                "container": "[parameters('clusterName')]",
                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                }
            ]
            },
            "computeProfile": {
            "roles": [
                {
                "name": "headnode",
                "targetInstanceCount": "2",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
                }
                },
                {
                "name": "workernode",
                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
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

<!---HONumber=AcomDC_0914_2016-->