---
title: Azure Resource Manager テンプレートを使用して SQL BACPAC ファイルをインポートする | Microsoft Docs
description: SQL Database 拡張機能を使用して、Azure Resource Manager テンプレートで SQL BACPAC ファイルをインポートする方法について説明します
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/29/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fa7d48c9a8079dc9171879ab5b72e3f04f870ebc
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036178"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>チュートリアル: Azure Resource Manager テンプレートを使用して SQL BACPAC ファイルをインポートする

Azure SQL Database 拡張機能を使用して BACPAC ファイルをインポートする方法について説明します。 このチュートリアルでは、テンプレートを作成して Azure SQL サーバー、SQL データベース、BACPAC ファイルをデプロイします。 Azure Resource Manager テンプレートを使用して Azure 仮想マシン拡張機能をデプロイする方法については、「[チュートリアル: Azure Resource Manager テンプレートを使用して仮想マシン拡張機能をデプロイする](./resource-manager-tutorial-deploy-vm-extensions.md)」を参照してください。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * BACPAC ファイルを準備する
> * クイック スタート テンプレートを開く
> * テンプレートの編集
> * テンプレートのデプロイ
> * デプロイを検証する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* Resource Manager ツール拡張機能を持つ [Visual Studio Code](https://code.visualstudio.com/)。 [拡張機能のインストールに関する記事](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)を参照してください。
* セキュリティを向上させるために、生成されたパスワードを SQL Server 管理者アカウントに対して使用します。 パスワードを生成するためのサンプルを次に示します。

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault は、暗号化キーおよびその他のシークレットを保護するために設計されています。 詳細については、「[チュートリアル: Resource Manager Template deployment で Azure Key Vault を統合する](./resource-manager-tutorial-use-key-vault.md)」を参照してください。 パスワードは 3 か月ごとに更新することをお勧めします。

## <a name="prepare-a-bacpac-file"></a>BACPAC ファイルを準備する

BACPAC ファイルは、[パブリック アクセスが有効な Azure ストレージ アカウント](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac)で共有されます。 独自のものを作成するには、「[Azure SQL Database を BACPAC ファイルにエクスポートする](../sql-database/sql-database-export.md)」を参照してください。 ファイルを独自の場所に発行する場合は、チュートリアルの後半でテンプレートを更新する必要があります。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

Azure クイック スタート テンプレートは、Resource Manager テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このチュートリアルで使用されるテンプレートは、[脅威の検出を備えた Azure SQL サーバーのデプロイ](https://azure.microsoft.com/resources/templates/201-sql-threat-detection-server-policy-optional-db/)と呼ばれます。

1. Visual Studio Code から、**[ファイル]**>**[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-sql-threat-detection-server-policy-optional-db/azuredeploy.json
    ```
3. **[開く]** を選択して、ファイルを開きます。

    テンプレートで定義されている 3 つのリソースがあります。

    * `Microsoft.Sql/servers` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.sql/servers)をご覧ください。
    * `Microsoft.SQL/servers/securityAlertPolicies` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies)をご覧ください。
    * `Microsoft.SQL.servers/databases`  [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases)をご覧ください。
    カスタマイズする前にテンプレートの基本をある程度理解することは役に立ちます。
4. **[ファイル]**>**[Save As]\(名前を付けて保存\)** を選択し、このファイルのコピーを **azuredeploy.json** という名前でローカル コンピューターに保存します。

## <a name="edit-the-template"></a>テンプレートの編集

テンプレートには 2 つのリソースをさらに追加する必要があります。

* SQL データベース拡張機能を使用して BACPAC ファイルをインポートできるようにするには、Azure サービスへのアクセスを許可する必要があります。 次の JSON を SQL サーバー定義に追加します。

    ```json
    {
        "type": "firewallrules",
        "name": "AllowAllAzureIps",
        "location": "[parameters('location')]",
        "apiVersion": "2014-04-01",
        "dependsOn": [
            "[variables('databaseServerName')]"
        ],
        "properties": {
            "startIpAddress": "0.0.0.0",
            "endIpAddress": "0.0.0.0"
        }
    }
    ```

    テンプレートは次のようになります。

    ![Azure Resource Manager による SQL 拡張機能 BACPAC のデプロイ](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

* 次の JSON を使用して、SQL データベース拡張機能リソースをデータベース定義に追加します。

    ```json
    "resources": [
        {
            "name": "Import",
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
            ],
            "properties": {
                "storageKeyType": "SharedAccessKey",
                "storageKey": "?",
                "storageUri": "https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import",
            }
        }
    ]
    ```

    テンプレートは次のようになります。

    ![Azure Resource Manager による SQL 拡張機能 BACPAC のデプロイ](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

    リソース定義を理解するには、[SQL Database 拡張機能のリファレンス](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions)に関するページを参照してください。 以下にいくつかの重要な要素を示します。

    * **dependsOn**: 拡張機能リソースは、SQL データベースが作成された後に作成される必要があります。
    * **storageKeyType**: 使用するストレージ キーの種類。 値は `StorageAccessKey` と `SharedAccessKey` のいずれかにできます。 提供される BACPAC ファイルはパブリック アクセスが有効な Azure ストレージ アカウントで共有されるため、ここでは "SharedAccessKey" が使用されます。
    * **storageKey**: 使用するストレージ キー。 ストレージ キーの種類が SharedAccessKey の場合は、前に "?" が付いている必要があります。
    * **storageUri**: 使用するストレージ URI。 提供される BACPAC ファイルを使用しない場合は、値を更新する必要があります。
    * **administratorLoginPassword**: SQL 管理者のパスワード。 生成されたパスワードを使用することが推奨されます。 「[前提条件](#prerequisites)」を参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

デプロイ手順については、「[テンプレートのデプロイ](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template)」セクションを参照してください。 代わりに次の PowerShell デプロイ スクリプトを使用します。

```azurepowershell
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmResourceGroupDeployment -Name $deploymentName `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile azuredeploy.json
```

生成されたパスワードを使用することが推奨されます。 「[前提条件](#prerequisites)」を参照してください。

## <a name="verify-the-deployment"></a>デプロイを検証する

ポータルで、新しくデプロイされたリソース グループから SQL データベースを選択します。 **[クエリ エディター (プレビュー)]** を選択してから、管理者の資格情報を入力します。 データベースにインポートされた 2 つのテーブルが表示されます。

![Azure Resource Manager による SQL 拡張機能 BACPAC のデプロイ](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、SQL サーバーと SQL データベースをデプロイして、BACPAC ファイルをインポートしました。 複数のリージョンにわたって Azure リソースをデプロイする方法のほか、安全なデプロイの実践については、以下を参照してください

> [!div class="nextstepaction"]
> [Azure Deployment Manager の使用](./resource-manager-tutorial-deploy-vm-extensions.md)
