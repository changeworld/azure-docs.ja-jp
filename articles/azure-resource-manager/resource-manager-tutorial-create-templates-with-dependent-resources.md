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
ms.date: 07/20/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: bd559cb9f0140706a4b9735c642367e03616a14d
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188167"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>チュートリアル: 依存リソースを含む Azure Resource Manager テンプレートを作成する

Azure Resource Manager テンプレートを作成して、複数のリソースをデプロイする方法を説明します。  テンプレートを作成したら、Azure Portal から Cloud Shell を使用してテンプレートをデプロイします。

別のリソースが存在するまではデプロイできないリソースもあります。 たとえば、仮想マシンを作成するには、そのストレージ アカウントとネットワーク インターフェイスが存在している必要があります。 このリレーションシップは、一方のリソースと他方のリソースに依存関係を持たせることにより定義します。 Resource Manager により、リソース間の依存関係が評価され、リソースは依存する順にデプロイされます。 相互依存していないリソースは、平行してデプロイされます。 詳細については、「[Define the order for deploying resources in Azure Resource Manager Templates](./resource-group-define-dependencies.md)」 (Azure Resource Manager テンプレートでリソースをデプロイする順序を定義する) を参照してください。

> [!div class="checklist"]
> * クイック スタート テンプレートを開く
> * テンプレートを調べる
> * テンプレートのデプロイ
> * リソースのクリーンアップ

このチュートリアルの手順では、仮想マシン、仮想ネットワーク、およびその他の依存リソースを作成します。 

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* [Visual Studio Code](https://code.visualstudio.com/)。
* Resource Manager ツール拡張機能。  「[拡張機能のインストールに関する記事](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)」を参照してください。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

Azure クイック スタート テンプレートは、Resource Manager テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このチュートリアルで使用するテンプレートは、「[Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)」(単純な Windows VM をデプロイする) と呼ばれます。

1. Visual Studio Code から、**[ファイル]**>**[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. **[開く]** を選択して、ファイルを開きます。
4. **[ファイル]**>**[Save As]\(名前を付けて保存\)** を選択し、このファイルのコピーを **azuredeploy.json** という名前でローカル コンピューターに保存します。

## <a name="explore-the-template"></a>テンプレートを調べる

1. Visual Studio Code で、**resources** 内の最初のレベルの要素と 2 番目のレベルの要素のみが表示されるまで要素を折り畳みます。

    ![Visual Studio Code Azure Resource Manager テンプレート](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    テンプレートによって定義されたリソースは、5 つあります。
2. 4 番目の要素を展開します。

    ![Visual Studio Code Azure Resource Manager テンプレートの dependson](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    dependsOn 要素を使用すると、1 つのリソースが 1 つ以上のリソースに依存していることを定義できます。 この例では、このリソースは、networkInterface です。  次に示す他の 2 つのリソースに依存しています。

    * publicIPAddress
    * virtualNetwork

3. 5 番目の要素を展開します。 このリソースは、仮想マシンです。 次に示す他の 2 つのリソースに依存しています。

    * storageAccount
    * networkInterface

次の図は、このテンプレートのリソースと依存関係の情報を示しています。

![Visual Studio Code Azure Resource Manager テンプレートの依存関係図](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

依存関係を指定することにより、Resource Manager は、ソリューションを効率的にデプロイします。 ストレージ アカウント、パブリック IP アドレス、および仮想ネットワークは、依存関係がないため、同時にデプロイされます。 パブリック IP アドレスと仮想ネットワークがデプロイされると、ネットワーク インターフェイスが作成されます。 その他のすべてのリソースがデプロイされると、Resource Manager は、仮想マシンをデプロイします。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

テンプレートをデプロイする方法は多数あります。  このチュートリアルでは、Azure Portal から Cloud Shell を使用します。

1. [Azure ポータル](https://portal.azure.com)
2. 次の図のように、右上隅の **[Cloud Shell]** を選択します。

    ![Azure portal の Cloud Shell](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
3. Cloud Shell の左上隅から **[PowerShell]** を選択します。  このチュートリアルでは、PowerShell を使用します。
4. **[再起動]** を選択します。
5. Cloud Shell から **[ファイルのアップロード]** を選択します。

    ![Azure portal の Cloud Shell のファイルのアップロード](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
6. チュートリアルで前に保存したファイルを選択します。 既定の名前は **azuredeploy.json** です。  同じ名前のファイルを既に持っている場合は、古いファイルが通知なしに上書きされます。
7. Cloud Shell から次のコマンドを実行し、ファイルが正常にアップロードされていることを確認します。 

    ```shell
    ls
    ```

    ![Azure portal の Cloud Shell のファイルの一覧表示](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    スクリーン ショットに示されているファイル名は、azuredeploy.json です。

8. Cloud Shell から次のコマンドを実行し、JSON ファイルの内容を確認します。

    ```shell
    cat azuredeploy.json
    ```
9. Cloud Shell から、次のような PowerShell コマンドを実行します。

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $location = "<Enter the Azure location>"
    $vmAdmin = "<Enter the admin username>"
    $vmPassword = "<Enter the password>"
    $dnsLabelPrefix = "<Enter the prefix>"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    $vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
    New-AzureRmResourceGroupDeployment -Name mydeployment0710 -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json -adminUsername $vmAdmin -adminPassword $vmPW `
        -dnsLabelPrefix $dnsLabelPrefix
    ```
    サンプル デプロイのスクリーンショットを次に示します。

    ![Azure portal の Cloud Shell のテンプレートのデプロイ](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-deploy-template.png)

    スクリーンショットでは、次の値が使用されています。

    * **$resourceGroupName**: myresourcegroup0710 
    * **$location**: eastus2
    * **&lt;DeployName>**: mydeployment0710
    * **&lt;TemplateFile>**: azuredeploy.json
    * **テンプレート パラメーター**:

        * **adminUsername**: JohnDole
        * **adminPassword**: Pass@word123
        * **dnsLabelPrefix**: myvm0710

10. 次の PowerShell コマンドを実行して、新しく作成された仮想マシンの一覧を表示します。

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName <ResourceGroupName>
    ```

    仮想マシンの名前は、テンプレート内に **SimpleWinVM** としてハードコーディングされています。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、テンプレートを作成してデプロイし、仮想マシン、仮想ネットワーク、および依存リソースを作成しました。 テンプレートの詳細については、「[Understand the structure and syntax of Azure Resource Manager Templates](./resource-group-authoring-templates.md)」 (Azure Resource Manager テンプレートの構造と構文について) を参照してください。