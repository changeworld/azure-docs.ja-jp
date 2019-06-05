---
title: Resource Manager テンプレートのデプロイで Azure Key Vault を統合する | Microsoft Docs
description: Azure Key Vault を使用して Resource Manager テンプレートのデプロイ時にセキュリティで保護されたパラメーターの値を渡す方法について説明します
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 0d78e6eaca708073c3a216507b320fe8783a25b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239237"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>チュートリアル: Resource Manager Template deployment で Azure Key Vault を統合する

Azure Key Vault からシークレットを取得し、Resource Manager デプロイ時にシークレットをパラメーターとして渡す方法を説明します。 参照するのは Key Vault ID だけであるため、値が公開されることはありません。 詳しくは、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](./resource-manager-keyvault-parameter.md)」を参照してください

「[リソースのデプロイ順序の設定](./resource-manager-tutorial-create-templates-with-dependent-resources.md)」チュートリアルでは、仮想マシンを作成します。 仮想マシン管理者のユーザー名とパスワードを入力する必要があります。 パスワードを指定する代わりに、Azure Key Vault にパスワードを事前に格納しておき、デプロイ時にキー コンテナーからパスワードを取得するようテンプレートをカスタマイズすることができます。

![Resource Manager テンプレートの Key Vault 統合の図](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * キー コンテナーを準備する
> * クイック スタート テンプレートを開く
> * パラメーター ファイルを編集する
> * テンプレートのデプロイ
> * デプロイの検証
> * リソースのクリーンアップ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* [Visual Studio Code](https://code.visualstudio.com/) と [Resource Manager ツール拡張機能](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)。
* セキュリティを向上させるには、生成されたパスワードを仮想マシンの管理者アカウントに対して使用します。 パスワードを生成するためのサンプルを次に示します。

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    生成されたパスワードが、仮想マシンのパスワード要件を満たしていることを確認します。 各Azure サービスには、特定のパスワード要件があります。 VMのパスワード要件については、 [VM を作成するときのパスワードの要件とは?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)を参照してください。

## <a name="prepare-a-key-vault"></a>キー コンテナーを準備する

このセクションでは、テンプレートをデプロイするときにシークレットを取得できるよう、キー コンテナーを作成し、キー コンテナーにシークレットを追加します。 キー コンテナーを作成するにはさまざまな方法があります。 このチュートリアルでは、Azure PowerShell を使用して [Resource Manager テンプレート](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json)をデプロイします。 このテンプレートを使用すると：

* `enabledForTemplateDeployment` プロパティを有効にしてキー コンテナーを作成する。 テンプレートのデプロイ プロセスがこのキー コンテナーで定義されているシークレットにアクセスできるようにするには、事前にこのプロパティを true にする必要があります。
* キー コンテナーにシークレットを追加します。  シークレットは、仮想マシンの管理者パスワードを格納します。

> [!NOTE]
> 仮想マシンのテンプレートをデプロイするユーザーがキー コンテナーの所有者または共同作成者でない場合、キー コンテナーの所有者または共同作成者によって Microsoft.KeyVault/vaults/deploy/action アクセス許可へのアクセス権を与えられる必要があります。 詳しくは、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](./resource-manager-keyvault-parameter.md)」を参照してください

次の PowerShell スクリプトを実行するには、 **[使ってみる]** を選択して、Cloud Shell を開きます。 スクリプトを貼り付けるには、シェル ウィンドウを右クリックし、 **[貼り付け]** を選択します。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue
```

いくつかの重要な情報を次に示します。

* リソース グループの名前は、**rg** が付加されたプロジェクト名です。 [このチュートリアルで作成したリソースのクリーンアップ](#clean-up-resources)を容易にするには、[次のテンプレートをデプロイする](#deploy-the-template)ときに、同じプロジェクト名およびリソース グループ名を使用します。
* シークレットの既定の名前は **vmAdminPassword** です。 これは、テンプレートでハードコードされます。
* テンプレートによってシークレットを取得できるようにするには、キー コンテナーに対して、 **[テンプレートの展開に対して Azure Resource Manager へのアクセスを有効にする]** アクセス ポリシーを有効にする必要があります。 このポリシーは、テンプレートで有効にします。 このアクセス ポリシーの詳細については、[キー コンテナーとシークレットのデプロイ](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets)に関するページを参照してください。

テンプレートには、**keyVaultId** という 1 つの出力値があります。 この値を書き留めます。 仮想マシンをデプロイする時に、この ID が必要です。 リソース ID の形式は:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

ID をコピーして貼り付けると、ID が複数の行に分かれてしまう場合があります。 行をマージし、余分なスペースを削除する必要があります。

デプロイを検証するには、同じシェル ウィンドウで次の PowerShell コマンドを実行して、シークレットをクリア テキストで取得します。 このコマンドは、前述の PowerShell スクリプトで定義された $keyVaultName 変数を使用しているため、同じシェル セッションでのみ機能します。

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

これでキー コンテナーとシークレットを準備できたので、次のセクションでは、デプロイ時にシークレットを取得するよう既存のテンプレートをカスタマイズする方法について説明します。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

Azure クイック スタート テンプレートは、Resource Manager テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このチュートリアルで使用するテンプレートは、「[Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)」(単純な Windows VM をデプロイする) と呼ばれます。

1. Visual Studio Code から、 **[ファイル]** > **[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. **[開く]** を選択して、ファイルを開きます。 これは、[チュートリアル: Azure Resource Manager テンプレートを依存リソースで作成する](./resource-manager-tutorial-create-templates-with-dependent-resources.md)で使用されたものと同じシナリオです。
4. テンプレートによって定義されたリソースは、5 つあります。

   * `Microsoft.Storage/storageAccounts` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)をご覧ください。
   * `Microsoft.Network/publicIPAddresses` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)をご覧ください。
   * `Microsoft.Network/virtualNetworks` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)をご覧ください。
   * `Microsoft.Network/networkInterfaces` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)をご覧ください。
   * `Microsoft.Compute/virtualMachines` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)をご覧ください。

     カスタマイズする前にテンプレートの基本をある程度理解することは役に立ちます。
5. **[ファイル]** > **[Save As]\(名前を付けて保存\)** を選択し、このファイルのコピーを **azuredeploy.json** という名前でローカル コンピューターに保存します。
6. 次の URL を開くには手順1～4を繰り返して、ファイルを**azuredeploy.parameters.json**として保存します。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>パラメーター ファイルを編集する

テンプレートファイルに変更を加える必要はありません。

1. **azuredeploy.parameters.json**が開いていない場合、Visual Studio Code で開きます。
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

    > [!IMPORTANT]
    > **id** の値を、最後の手順で作成したキー コンテナーのリソース ID に置き換えます。

    ![Key Vault と Resource Manager テンプレートの仮想マシンのデプロイ パラメーター ファイルを統合する](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. 次のように値を指定します。

    * **adminUsername**: 仮想マシンの管理者アカウントに名前を付けます。
    * **dnsLabelPrefix**: dnsLabelPrefix に名前を付けます。

    前のスクリーンショットの例を参照してください。

4. 変更を保存します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

[テンプレートをデプロイする](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)の指示に従って、テンプレートをデプロイします。 テンプレートをデプロイするには、**azuredeploy.json** と **azuredeploy.parameters.json** の両方を Cloud Shell にアップロードし、次の PowerShell スクリプトを使用する必要があります:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

テンプレートをデプロイするときに、キー コンテナーと同じリソース グループを使用します。 そうすると、リソースをクリーンアップしやすくなります。 削除する必要があるのは2 つではなく 1 つのリソース グループのみです。

## <a name="validate-the-deployment"></a>デプロイの検証

仮想マシンを正常にデプロイした後は、キー コンテナーに格納されているパスワードを使用してログインをテストします。

1. [Azure Portal](https://portal.azure.com)を開きます。
2. **リソース grouips**/**YourResourceGroupName >** /**simpleWinVM**を選択します
3. 上部から**接続**を選択します。
4. **[RDP ファイルのダウンロード]** を選択します。その後、指示に従って、キー コンテナーに格納されているパスワードを使用して仮想マシンにサインインします。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Key Vault からシークレットを取得し、そのシークレットをテンプレートのデプロイで使用しました。  リンク済みテンプレートを作成する方法についての説明は、次を参照してください:

> [!div class="nextstepaction"]
> [リンク済みテンプレートを作成する](./resource-manager-tutorial-create-linked-templates.md)
