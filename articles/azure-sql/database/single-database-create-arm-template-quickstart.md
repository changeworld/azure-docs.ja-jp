---
title: Azure Resource Manager:単一データベースを作成する
description: Azure Resource Manager テンプレートを使用して Azure SQL Database に単一データベースを作成します。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/28/2019
ms.openlocfilehash: 03d598e0c0756cd304248ab19ab28a25836c57ab
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84052410"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>クイック スタート:Azure Resource Manager テンプレートを使用して Azure SQL Database に単一データベースを作成する
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[単一データベース](single-database-overview.md)の作成は、Azure SQL Database でデータベースを作成する場合の最も迅速かつ簡単なオプションです。 このクイック スタートでは、Azure Resource Manager テンプレートを使用して単一データベースを作成する方法について説明します。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

なし

## <a name="create-a-single-database"></a>単一データベースを作成する

単一データベースには、2 種類の[購入モデル](purchasing-models.md)のいずれかを使用して定義されたコンピューティング、メモリ、IO、ストレージのリソースのセットがあります。 単一データベースを作成するときは、それを管理するための[サーバー](logical-servers.md)も定義し、指定したリージョンの [Azure リソース グループ](../../active-directory-b2c/overview.md)内にそれを配置します。

### <a name="review-the-template"></a>テンプレートを確認する

このクイック スタートで使用されるテンプレートは [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/101-sql-logical-server/)からのものです。

:::code language="json" source="~/quickstart-templates/101-sql-logical-server/azuredeploy.json" range="1-163" highlight="63-132":::

テンプレートでは、次のリソースが定義されています。

- [**Microsoft.Sql/servers**](/azure/templates/microsoft.sql/servers)
- [**Microsoft.Sql/servers/firewallRules**](/azure/templates/microsoft.sql/servers/firewallrules)
- [**Microsoft.Sql/servers/securityAlertPolicies**](/azure/templates/microsoft.sql/servers/securityalertpolicies)
- [**Microsoft.Sql/servers/vulnerabilityAssessments**](/azure/templates/microsoft.sql/servers/vulnerabilityassessments)
- [**Microsoft.Sql/servers/connectionPolicies**](/azure/templates/microsoft.sql/servers/connectionpolicies)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Storage/storageAccounts/providers/roleAssignments**](/azure/templates/microsoft.authorization/roleassignments)

その他の Azure SQL Database テンプレートのサンプルは、[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular)のページから入手できます。

### <a name="deploy-the-template"></a>テンプレートのデプロイ

次の PowerShell コード ブロックから **[使ってみる]** を選択して Azure Cloud Shell を開きます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-logical-server/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

az group create --location $location --name $resourceGroupName

az group deployment create -g $resourceGroupName --template-uri "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" `
    --parameters 'projectName=' + $projectName \
                 'administratorLogin=' + $adminUser \
                 'administratorLoginPassword=' + $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

* * *

## <a name="validate-the-deployment"></a>デプロイの検証

データベースに対してクエリを実行するには、「[データベースのクエリを実行する](single-database-create-quickstart.md#query-the-database)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

「[次の手順](#next-steps)」に進む場合は、このリソース グループ、サーバー、単一データベースをそのままにしてください。 次のステップでは、データベースに接続してクエリを実行するさまざまな方法を紹介しています。

リソース グループを削除するには:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

* * *

## <a name="next-steps"></a>次のステップ

- オンプレミスまたはリモート ツールから単一データベースに接続するために、サーバー レベルのファイアウォール規則を作成します。 詳細については、「[サーバーレベルのファイアウォール規則を作成する](firewall-create-server-level-portal-quickstart.md)」を参照してください。
- サーバーレベルのファイアウォール規則を作成した後に、いくつかの異なるツールと言語を使用して、データベースに[接続し、クエリを実行](connect-query-content-reference-guide.md)します。
  - [SQL Server Management Studio を使用して接続およびクエリを実行する](connect-query-ssms.md)
  - [Azure Data Studio を使用して接続およびクエリを実行する](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Azure CLI を使用して単一データベースを作成するには、「[Azure CLI のサンプル](az-cli-script-samples-content-guide.md)」をご覧ください。
- Azure PowerShell を使用して単一データベースを作成するには、「[Azure PowerShell サンプル](powershell-script-content-guide.md)」をご覧ください。
- Resource Manager テンプレートを作成する方法については、[初めてのテンプレートの作成](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)に関するページを参照してください。
