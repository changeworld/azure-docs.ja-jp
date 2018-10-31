---
title: Azure Resource Manager テンプレートでの条件の使用 | Microsoft Docs
description: 条件に基づいて Azure リソースをデプロイする方法について説明します。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/18/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 552b39c520396942fa81f963c0cfa1c8c7b47db4
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456968"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>チュートリアル: Azure Resource Manager テンプレートでの条件の使用

条件に基づいて Azure リソースをデプロイする方法について説明します。 

このチュートリアルで使用するシナリオは、[チュートリアル: Azure Resource Manager テンプレートを依存リソースで作成する](./resource-manager-tutorial-create-templates-with-dependent-resources.md)で使用されたシナリオに似ています。 そのチュートリアルでは、仮想マシン、仮想ネットワーク、およびその他の依存リソース (ストレージ アカウントなど) を作成します。 新しいストレージ アカウントを毎回作成する代わりに、新しいストレージ アカウントを作成するか、既存のストレージ アカウントを使用するかをユーザーに選択してもらいます。 この目的を達成するために、追加のパラメーターを定義します。 パラメーターの値に "new" が指定されると、新しいストレージ アカウントが作成されます。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * クイック スタート テンプレートを開く
> * テンプレートの変更
> * テンプレートのデプロイ
> * リソースのクリーンアップ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* [Resource Manager ツール拡張機能](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)を持つ [Visual Studio Code](https://code.visualstudio.com/)

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

Azure クイック スタート テンプレートは、Resource Manager テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このチュートリアルで使用するテンプレートは、「[Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)」(単純な Windows VM をデプロイする) と呼ばれます。

1. Visual Studio Code から、**[ファイル]**>**[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. **[開く]** を選択して、ファイルを開きます。
4. **[ファイル]**>**[Save As]\(名前を付けて保存\)** を選択し、このファイルのコピーを **azuredeploy.json** という名前でローカル コンピューターに保存します。

## <a name="modify-the-template"></a>テンプレートの変更

既存のテンプレートに 2 つの変更を加えます。

* ストレージ アカウント名パラメーターを追加します。 ユーザーは、新しいストレージ アカウント名を指定するか、または既存のストレージ アカウント名を指定することができます。
* **newOrExisting** という新しいパラメーターを追加します。 デプロイする際には、このパラメーターを使用して、新しいストレージ アカウントを作成する、または既存のストレージ アカウントを使用する場所が判別されます。

1. Visual Studio Code で **azuredeploy.json** を開きます。
2. テンプレート全体で **variables('storageAccountName')** を **parameters('storageAccountName')** に置き換えます。  **variables('storageAccountName')** は 3 か所で使用されています。
3. 次の変数定義を削除します。

    ```json
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",
    ```
4. 次の 2 つのパラメーターをテンプレートに追加します。

    ```json
    "storageAccountName": {
      "type": "string"
    },    
    "newOrExisting": {
      "type": "string", 
      "allowedValues": [
        "new", 
        "existing"
      ]
    },
    ```
    更新したパラメーター定義は次のようになります。

    ![Resource Manager での条件の使用](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. ストレージ アカウント定義の先頭に次の行を追加します。

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    この条件では、**newOrExisting** というパラメーターの値が確認されます。 パラメーターの値に **new** が指定されていると、ストレージ アカウントが作成されます。

    更新したストレージ アカウント定義は次のようになります。

    ![Resource Manager での条件の使用](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. **storageUri** を次の値に更新します。

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    この変更は、異なるリソース グループの既存のストレージ アカウントを使用するときに必要になります。

7. 変更を保存します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

[テンプレートをデプロイする](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)の指示に従って、テンプレートをデプロイします。

Azure PowerShell を使用してテンプレートをデプロイする場合、1 つの追加パラメーターを指定する必要があります。

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password"
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
$vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
New-AzureRmResourceGroupDeployment -Name mydeployment1018 -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin -adminPassword $vmPW `
    -dnsLabelPrefix $dnsLabelPrefix -storageAccountName $storageAccountName -newOrExisting $newOrExisting `
    -TemplateFile azuredeploy.json
```

> [!NOTE]
> **newOrExisting** が **new** のときに、指定したストレージ アカウント名のストレージ アカウントが既に存在している場合、デプロイは失敗します。

**newOrExisting** を "existing" に設定した別のデプロイを作成して、既存のストレージ アカウントを指定します。 事前にストレージ アカウントを作成する場合は、「[ストレージ アカウントの作成](../storage/common/storage-quickstart-create-account.md)」を参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、新しいストレージ アカウントを作成するか、既存のストレージ アカウントを使用するかをユーザーが選択できるテンプレートを作成します。 このチュートリアルで作成した仮想マシンでは、管理者のユーザー名とパスワードが必要になります。 デプロイ時にパスワードを渡す代わりに、Azure Key Vault を使用してパスワードを事前に格納しておき、デプロイ時にパスワードを取得することができます。 Azure Key Vault からシークレットを取得し、テンプレートのデプロイでそのシークレットを使用する方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [テンプレートのデプロイで Key Vault を統合する](./resource-manager-tutorial-use-key-vault.md)
