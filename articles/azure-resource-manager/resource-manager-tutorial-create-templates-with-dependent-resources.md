---
title: 依存リソースを含む Azure Resource Manager テンプレートを作成する | Microsoft Docs
description: 複数のリソースを含む Azure Resource Manager テンプレートを作成し、Azure Portal を使用してデプロイする方法を説明します。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/09/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 50f1c81f08787181de2fe3a9f6fb97a96a2bd882
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114314"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>チュートリアル: 依存リソースを含む Azure Resource Manager テンプレートを作成する

Azure Resource Manager テンプレートを作成して、複数のリソースをデプロイする方法を説明します。  テンプレートを作成したら、Azure Portal から Cloud Shell を使用してテンプレートをデプロイします。

このチュートリアルでは、ストレージ アカウント、仮想マシン、仮想ネットワーク、およびその他の依存リソースを作成します。 別のリソースが存在するまではデプロイできないリソースもあります。 たとえば、仮想マシンを作成するには、そのストレージ アカウントとネットワーク インターフェイスが存在している必要があります。 このリレーションシップは、一方のリソースと他方のリソースに依存関係を持たせることにより定義します。 Resource Manager により、リソース間の依存関係が評価され、リソースは依存する順にデプロイされます。 相互依存していないリソースは、平行してデプロイされます。 詳細については、「[Define the order for deploying resources in Azure Resource Manager Templates](./resource-group-define-dependencies.md)」 (Azure Resource Manager テンプレートでリソースをデプロイする順序を定義する) を参照してください。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * Key Vault を準備する
> * クイック スタート テンプレートを開く
> * テンプレートを調べる
> * パラメーター ファイルを編集する
> * テンプレートのデプロイ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* Resource Manager ツール拡張機能を持つ [Visual Studio Code](https://code.visualstudio.com/)。  「[拡張機能のインストールに関する記事](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)」を参照してください。

## <a name="prepare-key-vault"></a>Key Vault を準備する

パスワード スプレー攻撃を防ぐには、仮想マシンの管理者アカウントに自動生成されたパスワードを使用し、Key Vault を使用してそのパスワードを格納することをお勧めします。 次の手順では、パスワードを格納するための Key Vault とシークレットを作成します。 また、Key Vault に格納されているシークレットにアクセスするようテンプレートのデプロイに必要なアクセス許可を構成します。 Key Vault が別の Azure サブスクリプションにある場合は、追加のアクセス ポリシーが必要です。 詳細については、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](./resource-manager-keyvault-parameter.md)」を参照してください。

1. [Azure Cloud Shell](https://shell.azure.com) にサインインします。
2. 左上隅からお気に入りの環境 (**PowerShell** または **Bash**) に切り替えます。
3. 次の Azure PowerShell または Azure CLI コマンドを実行します。  

    ```azurecli-interactive
    keyVaultName='<your-unique-vault-name>'
    resourceGroupName='<your-resource-group-name>'
    location='Central US'
    userPrincipalName='<your-email-address-associated-with-your-subscription>'
    
    # Create a resource group
    az group create --name $resourceGroupName --location $location
    
    # Create a Key Vault
    keyVault=$(az keyvault create \
      --name $keyVaultName \
      --resource-group $resourceGroupName \
      --location $location \
      --enabled-for-template-deployment true)
    keyVaultId=$(echo $keyVault | jq -r '.id')
    az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

    # Create a secret
    password=$(openssl rand -base64 32)
    az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: $keyVaultId."
    ```

    ```azurepowershell-interactive
    $keyVaultName = "<your-unique-vault-name>"
    $resourceGroupName="<your-resource-group-name>"
    $location='Central US'
    $userPrincipalName="<your-email-address-associated-with-your-subscription>"
    
    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
        
    # Create a Key Vault
    $keyVault = New-AzureRmKeyVault `
      -VaultName $keyVaultName `
      -resourceGroupName $resourceGroupName `
      -Location $location `
      -EnabledForTemplateDeployment
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
      
    # Create a secret
    $password = openssl rand -base64 32
    
    $secretValue = ConvertTo-SecureString $password -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretValue
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: " $keyVault.ResourceID
    ```
4. 出力値を書き留めます。 これらは後で必要になります。

> [!NOTE]
> 各 Azure サービスには、特定のパスワード要件があります。 たとえば、Azure 仮想マシンの要件については、「VM を作成する際のパスワードの要件は何ですか。」で確認できます。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

Azure クイック スタート テンプレートは、Resource Manager テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このチュートリアルで使用するテンプレートは、「[Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)」(単純な Windows VM をデプロイする) と呼ばれます。

1. Visual Studio Code から、**[ファイル]**>**[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. **[開く]** を選択して、ファイルを開きます。
4. **[ファイル]**>**[Save As]\(名前を付けて保存\)** を選択し、このファイルのコピーを **azuredeploy.json** という名前でローカル コンピューターに保存します。
5. 手順 1 から 4 までを繰り返して **https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json** を開き、そのファイルを **azuredeploy.parameters.json** として保存します。

## <a name="explore-the-template"></a>テンプレートを調べる

このセクションでテンプレートを調べるときは、次の質問に回答するようにしてください。

- このテンプレートに定義されている Azure リソースの数はいくつか。
- リソースの 1 つは、Azure Storage アカウントです。  定義は前回のチュートリアルで使用されたものと同じか。
- このテンプレートに定義されているリソースのテンプレート 参照を見つけることができるか。
- リソースの依存関係を見つけることができるか。

1. Visual Studio Code で、**resources** 内の最初のレベルの要素と 2 番目のレベルの要素のみが表示されるまで要素を折り畳みます。

    ![Visual Studio Code Azure Resource Manager テンプレート](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    テンプレートによって定義されたリソースは、5 つあります。
2. 最初のリソースを展開します。 それはストレージ アカウントです。 定義は、前回のチュートリアルの先頭で使用されたものと同じです。

    ![Visual Studio Code の Azure Resource Manager テンプレートのストレージ アカウント定義](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. 2 番目のリソースを展開します。 リソースの種類は **Microsoft.Network/publicIPAddresses** です。 テンプレート リファレンスを見つけるには、[テンプレート リファレンス](https://docs.microsoft.com/azure/templates/)に移動し、**[タイトルでフィルター]** フィールドに **1 つのパブリック ID アドレス**または**複数のパブリック IP アドレス**を入力します。 リソース定義とテンプレート リファレンスを比較します。

    ![Visual Studio Code の Azure Resource Manager テンプレートのパブリック IP アドレス定義](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. 最後の手順を繰り返して、このテンプレートに定義されている他のリソースのテンプレート参照を見つけます。  リソース定義とリファレンスを比較します。
5. 4 番目の要素を展開します。

    ![Visual Studio Code Azure Resource Manager テンプレートの dependson](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    dependsOn 要素を使用すると、1 つのリソースが 1 つ以上のリソースに依存していることを定義できます。 この例では、このリソースは、networkInterface です。  次に示す他の 2 つのリソースに依存しています。

    * publicIPAddress
    * virtualNetwork

6. 5 番目の要素を展開します。 このリソースは、仮想マシンです。 次に示す他の 2 つのリソースに依存しています。

    * storageAccount
    * networkInterface

次の図は、このテンプレートのリソースと依存関係の情報を示しています。

![Visual Studio Code Azure Resource Manager テンプレートの依存関係図](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

依存関係を指定することにより、Resource Manager は、ソリューションを効率的にデプロイします。 ストレージ アカウント、パブリック IP アドレス、および仮想ネットワークは、依存関係がないため、同時にデプロイされます。 パブリック IP アドレスと仮想ネットワークがデプロイされると、ネットワーク インターフェイスが作成されます。 その他のすべてのリソースがデプロイされると、Resource Manager は、仮想マシンをデプロイします。

## <a name="edit-the-parameters-file"></a>パラメーター ファイルを編集する

テンプレート ファイルに変更を加える必要はありません。 ただし、Key Vault から管理者パスワードを取得するには、パラメーター ファイルを変更する必要があります。

1. **azuredeploy.parameters.json** が開いていない場合は、Visual Studio Code で開きます。
2. **adminPassword** パラメーターを次のように更新します。

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```
    **id** を、最後の手順で作成した Key Vault の リソース ID に置き換えます。 出力の 1 つを次に示します。 

    ![Key Vault と Resource Manager テンプレートの仮想マシンのデプロイ パラメーター ファイルを統合する](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. 次のように値を指定します。

    - **adminUsername**: 仮想マシンの管理者アカウントに名前を付けます。
    - **dnsLabelPrefix**: dnsLablePrefix に名前を付けます。
4. 変更を保存します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

テンプレートをデプロイする方法は多数あります。  このチュートリアルでは、Azure Portal から Cloud Shell を使用します。

1. [Cloud Shell](https://shell.azure.com) にサインインします。 また、[Azure portal](https://portal.azure.com) にサインインしてから、次の図のように、右上隅の **[Cloud Shell]** を選択できます。

    ![Azure portal の Cloud Shell](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
2. Cloud Shell の左上隅から **[PowerShell]** を選択し、**[確認]** を選択します。  このチュートリアルでは、PowerShell を使用します。
3. Cloud Shell から **[ファイルのアップロード]** を選択します。

    ![Azure portal の Cloud Shell のファイルのアップロード](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. チュートリアルで前に保存したファイルを選択します。 既定の名前は **azuredeploy.json** と **azuredeploy.paraemters.json** です。  同じ名前のファイルを既に持っている場合は、古いファイルが通知なしに上書きされます。
5. Cloud Shell から次のコマンドを実行し、ファイルが正常にアップロードされていることを確認します。 

    ```bash
    ls
    ```

    ![Azure portal の Cloud Shell のファイルの一覧表示](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    スクリーン ショットに示されているファイル名は、azuredeploy.json です。

6. Cloud Shell から次のコマンドを実行し、JSON ファイルの内容を確認します。

    ```bash
    cat azuredeploy.json
    cat azuredeploy.parameters.json
    ```
7. Cloud Shell から、次の PowerShell コマンドを実行します。 サンプル スクリプトでは、Key Vault 用に作成された同じリソース グループを使用します。 同じリソース グループを使用することで、リソースをより簡単にクリーンアップできます。

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $deploymentName = "<Enter a deployment name>"

    New-AzureRmResourceGroupDeployment -Name $deploymentName `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json `
        -TemplateparameterFile azuredeploy.parameters.json
    ```
8. 次の PowerShell コマンドを実行して、新しく作成された仮想マシンの一覧を表示します。

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    仮想マシンの名前は、テンプレート内に **SimpleWinVM** としてハードコーディングされています。

9. 仮想マシンにサインインして、管理者の資格情報をテストします。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、テンプレートを作成してデプロイし、仮想マシン、仮想ネットワーク、および依存リソースを作成しました。 条件に基づいて Azure リソースをデプロイする方法については、以下を参照してください。


> [!div class="nextstepaction"]
> [使用条件](./resource-manager-tutorial-use-conditions.md)

