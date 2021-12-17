---
title: テンプレートを使用して SQL BACPAC ファイルをインポートする
description: Azure SQL Database 拡張機能を使用して Azure Resource Manager テンプレート (ARM テンプレート) で SQL BACPAC ファイルをインポートする方法について説明します。
author: mumian
ms.date: 09/30/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ae7f7fe3e626079c7e3bcb04dc76ffe5cacfe4a9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069788"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>チュートリアル:ARM テンプレートを使用して SQL BACPAC ファイルをインポートする

Azure SQL Database 拡張機能を使用して Azure Resource Manager テンプレート (ARM テンプレート) テンプレートで [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) ファイルをインポートする方法について説明します。 デプロイの成果物は、デプロイを完了するために必要なメイン テンプレート ファイルに加えたすべてのファイルです。 この BACPAC ファイルが成果物です。

このチュートリアルでは、テンプレートを作成して[論理 SQL サーバー](../../azure-sql/database/logical-servers.md)と単一データベースをデプロイし、BACPAC ファイルをインポートします。 ARM テンプレートを使用して Azure 仮想マシン拡張機能をデプロイする方法については、「[チュートリアル: ARM テンプレートを使用して仮想マシン拡張機能をデプロイする](./template-tutorial-deploy-vm-extensions.md)」を参照してください。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
>
> - BACPAC ファイルを準備する。
> - クイックスタート テンプレートを開く。
> - テンプレートを編集する。
> - テンプレートをデプロイします。
> - デプロイを検証する。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

- Visual Studio Code と Resource Manager ツール拡張機能。 「[クイック スタート:Visual Studio Code を使用して ARM テンプレートを作成する](./quickstart-create-templates-use-visual-studio-code.md)」を参照してください。
- セキュリティを向上させるために、生成されたパスワードをサーバー管理者アカウントに対して使用します。 [Azure Cloud Shell](../../cloud-shell/overview.md) を使用して、PowerShell または Bash で次のコマンドを実行できます。

    ```shell
    openssl rand -base64 32
    ```

    詳細については、`man openssl rand` を実行して man ページを開きます。

    Azure Key Vault は、暗号化キーおよびその他のシークレットを保護するために設計されています。 詳細については、[ARM テンプレートのデプロイで Azure Key Vault を統合する](./template-tutorial-use-key-vault.md)」を参照してください。 パスワードは 3 か月ごとに更新することをお勧めします。

## <a name="prepare-a-bacpac-file"></a>BACPAC ファイルを準備する

BACPAC ファイルは、[GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac) で共有されています。 独自のものを作成するには、[Azure SQL Database から BACPAC ファイルへのデータベースのエクスポート](../../azure-sql/database/database-export.md)に関する記事を参照してください。 ファイルを独自の場所に発行する場合は、チュートリアルの後半でテンプレートを更新する必要があります。

BACPAC ファイルは、ARM テンプレートを使用してインポートする前に、Azure Storage アカウントに格納されている必要があります。 以下の PowerShell スクリプトは、次の手順で BACPAC ファイルを準備します。

- BACPAC ファイルをダウンロードします。
- Azure ストレージ アカウントを作成します。
- ストレージ アカウントに BLOB コンテナーを作成します。
- BACPAC ファイルをコンテナーにアップロードします。
- ストレージ アカウント キー、BLOB の URL、リソース グループ名、および場所を表示します。

1. **[使ってみる]** を選択すると、Cloud Shell が開きます。 次の PowerShell スクリプトをコピーし、シェル ウィンドウに貼り付けます。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "The project name and location are $projectName and $location"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. ストレージ アカウント キー、BACPAC ファイルの URL、プロジェクト名、および場所を保存します。 それらの値は、このチュートリアルの後半でテンプレートをデプロイするときに使用します。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

このチュートリアルで使用されているテンプレートは、[GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json) に保存されています。

1. Visual Studio Code から、 **[ファイル]**  >  **[ファイルを開く]** を選択します。
1. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. **[開く]** を選択して、ファイルを開きます。

    テンプレートで定義されている 2 つのリソースがあります。

   - `Microsoft.Sql/servers`. [テンプレート リファレンス](/azure/templates/microsoft.sql/servers)をご覧ください。
   - `Microsoft.SQL.servers/databases`. [テンプレート リファレンス](/azure/templates/microsoft.sql/servers/databases)をご覧ください。

     カスタマイズする前にテンプレートの基本をある程度理解することは役に立ちます。

1. **[ファイル]**  >  **[Save As]\(名前を付けて保存\)** を選択し、このファイルのコピーを _azuredeploy.json_ という名前でローカル コンピューターに保存します。

## <a name="edit-the-template"></a>テンプレートの編集

1. `parameters` セクションの末尾に 2 つのパラメーターを追加して、ストレージ アカウント キーと BACPAC URL を設定します。

    ```json
        "storageAccountKey": {
          "type":"string",
          "metadata":{
            "description": "Specifies the key of the storage account where the BACPAC file is stored."
          }
        },
        "bacpacUrl": {
          "type":"string",
          "metadata":{
            "description": "Specifies the URL of the BACPAC file."
          }
        }
    ```

    `adminPassword` プロパティの閉じ中かっこ (`}`) の後に、コンマを追加します。 Visual Studio Code から JSON ファイルの書式を設定するには、Shift + Alt + F キーを押します。

1. テンプレートに 2 つのリソースを追加します。

    - SQL Database 拡張機能で BACPAC ファイルをインポートできるようにするには、Azure サービスからのトラフィックを許可する必要があります。 SQL サーバーがデプロイされると、ファイアウォール ルールによって、 **[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する]** の設定が有効になります。

      次のファイアウォール規則をサーバー定義の下に追加します。

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2021-02-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        次の例は、更新後のテンプレートを示しています。

        :::image type="content" source="media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png" alt-text="ファイアウォール定義を含むテンプレート。":::

    - 次の JSON を使用して、SQL データベース拡張機能リソースをデータベース定義に追加します。

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        次の例は、更新後のテンプレートを示しています。

        :::image type="content" source="media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png" alt-text="SQL Database 拡張機能を含むテンプレート。":::

        リソース定義を理解するには、API バージョンの [SQL Database 拡張機能のリファレンス](/azure/templates/microsoft.sql/servers/databases/extensions)に関するページを参照してください。 以下にいくつかの重要な要素を示します。

        - `dependsOn`:拡張機能リソースは、データベースが作成された後に作成される必要があります。
        - `storageKeyType`:使用するストレージ キーの種類を指定します。 値は `StorageAccessKey` と `SharedAccessKey` のいずれかにできます。 このチュートリアルでは `StorageAccessKey` を使用します。
        - `storageKey`:BACPAC ファイルが格納されているストレージ アカウントのキーを指定します。 ストレージ キーの種類が `SharedAccessKey` の場合、その前に "?" を付ける必要があります。
        - `storageUri`:ストレージ アカウントに格納されている BACPAC ファイルの URL を指定します。
        - `administratorLogin`: SQL 管理者のアカウント名。
        - `administratorLoginPassword`: SQL 管理者のパスワード。 生成されたパスワードを使用するには、「[前提条件](#prerequisites)」を参照してください。

次の例に、完成したテンプレートを示します。

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>テンプレートのデプロイ

BACPAC ファイルを準備したときに使用したプロジェクトの名前と場所を使用します。 これにより、すべてのリソースが同じリソース グループに配置され、リソースを削除するときに役立ちます。

1. [Cloud Shell](https://shell.azure.com) にサインインします。
1. 左上隅から **[PowerShell]** を選択します。

    :::image type="content" source="media/template-tutorial-deploy-sql-extensions-bacpac/cloud-shell-select.png" alt-text="PowerShell で Azure Cloud Shell を開き、ファイルをアップロードします。":::

1. **[ファイルのアップロード/ダウンロード]** を選択し、_azuredeploy. json_ ファイルをアップロードします。
1. テンプレートをデプロイするには、次のスクリプトをコピーし、シェル ウィンドウに貼り付けます。

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the SQL admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $storageAccountKey = Read-Host -Prompt "Enter the storage account key"
    $bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUser $adminUsername `
        -adminPassword $adminPassword `
        -TemplateFile "$HOME/azuredeploy.json" `
        -storageAccountKey $storageAccountKey `
        -bacpacUrl $bacpacUrl

    Write-Host "Press [ENTER] to continue ..."
    ```

## <a name="verify-the-deployment"></a>デプロイを検証する

クライアント コンピューターからサーバーにアクセスするには、ファイアウォール規則を追加する必要があります。 クライアントの IP アドレスと、サーバーへの接続に使用される IP アドレスは、ネットワーク アドレス変換 (NAT) のために異なる場合があります。 詳細については、「[IP ファイアウォール規則の作成および管理](../../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)」を参照してください。

たとえば、**クエリ エディター** にサインインすると、IP アドレスが許可されていないことを示すメッセージが表示されます。 アドレスは、NAT のために、クライアントの IP アドレスと異なります。 メッセージのリンクを選択して、IP アドレスにファイアウォール規則を追加します。 完了したら、サーバーの **ファイアウォールと仮想ネットワーク** の設定から IP アドレスを削除できます。

Azure portal で、リソース グループからデータベースを選択します。 **[クエリ エディター (プレビュー)]** を選択し、管理者の資格情報を入力します。 データベースにインポートされた 2 つのテーブルが表示されます。

![クエリ エディター (プレビュー)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

デプロイした Azure リソースが不要になったら、リソース グループを削除します。 リソース グループ、ストレージ アカウント、SQL サーバー、SQL データベースが削除されます。

1. Azure portal で、検索ボックスに「**リソース グループ**」と入力します。
1. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
1. リソース グループ名を選択します。
1. **[リソース グループの削除]** を選択します。
1. 削除を確認するには、リソース グループ名を入力して **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、サーバーとデータベースをデプロイして、BACPAC ファイルをインポートしました。 テンプレート デプロイのトラブルシューティング方法については、次のトピックを参照してください。

> [!div class="nextstepaction"]
> [ARM テンプレート デプロイのトラブルシューティング](../troubleshooting/quickstart-troubleshoot-arm-deployment.md)
