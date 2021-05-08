---
title: 'クイックスタート: Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure ルート サーバーを作成する'
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure ルート サーバーを作成する方法について説明します。
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 3476e5fa2c274f0fc2c180711480375b0ebefaf2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388042"
---
# <a name="quickstart-create-an-azure-route-server-using-an-arm-template"></a>クイックスタート: ARM テンプレートを使用して Azure ルート サーバーを作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、新規または既存の仮想ネットワークに Azure ルート サーバーをデプロイする方法について説明します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-route-server%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-route-server)からのものです。

このクイックスタートでは、Azure ルート サーバーを新規または既存の仮想ネットワークにデプロイします。 ルート サーバーをホストするために、`RouteServerSubnet` という名前の専用サブネットが作成されます。 また、ルート サーバーは、BGP ピアリングを確立するために、ピア ASN とピア IP を使用して構成されます。

:::code language="json" source="~/quickstart-templates/101-route-server/azuredeploy.json" range="001-145" highlight="105-142":::

テンプレートでは、複数の Azure リソースが定義されています。

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualNetworks/subnets) (2 つのサブネット、1 つの名前は `routeserversubnet`)
* [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualhubs) (ルート サーバーのデプロイ)
* [**Microsoft.Network/virtualHubs/ipConfigurations**](/azure/templates/microsoft.network/virtualhubs/ipConfigurations)
* [**Microsoft.Network/virtualHubs/bgpConnections**](/azure/templates/microsoft.network/virtualhubs/bgpconnections) (ピア ASN およびピア IP 構成)


ExpressRoute に関連するテンプレートをさらに探すには、「[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)」を参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. 次のコード ブロックの **[使ってみる]** を選択して Azure Cloud Shell を開き、指示に従って Azure にサインインします。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-route-server/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    コンソールからプロンプトが表示されるまで待ちます。

1. 前のコード ブロックから **[コピー]** を選択して、PowerShell スクリプトをコピーします。

1. シェル コンソール ウィンドウを右クリックし、 **[貼り付け]** を選択します。

1. 値を入力します。

    リソース グループの名前は、**rg** が付加されたプロジェクト名です。

    テンプレートのデプロイには約 20 分かかります。 完了すると、次のように出力されます。

    :::image type="content" source="./media/quickstart-configure-template/powershell-output.png" alt-text="ルート サーバーの Resource Manager テンプレートの PowerShell デプロイ出力。":::

テンプレートをデプロイするには Azure PowerShell を使用します。 Azure PowerShell だけでなく、Azure portal、Azure CLI、および REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-portal.md)」を参照してください。

## <a name="validate-the-deployment"></a>デプロイの検証

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 左側のウィンドウから **[リソース グループ]** を選択します。

1. 前のセクションで作成したリソース グループを選択します 既定のリソース グループ名は、プロジェクト名に **rg** が追加されたものです。

1. リソース グループには、仮想ネットワークのみを含める必要があります。

     :::image type="content" source="./media/quickstart-configure-template/resource-group.png" alt-text="仮想ネットワークを含むルート サーバー デプロイ リソース グループ。":::

1. https://aka.ms/routeserver にアクセスします。

1. **routeserver** という名前のルート サーバーを選択して、デプロイが成功したことを確認します。

    :::image type="content" source="./media/quickstart-configure-template/deployment.png" alt-text="ルート サーバーの概要ページのスクリーンショット。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ルート サーバーと共に作成したリソースが不要になった場合は、リソース グループを削除します。 これにより、ルート サーバーとすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、次のものを作成しました。

* ルート サーバー
* Virtual Network
* Subnet

Azure Route Server を作成した後、Azure Route Server が ExpressRoute および VPN Gateway とどのように連携するかについては、以下を参照してください。 

> [!div class="nextstepaction"]
> [Azure ExpressRoute と Azure VPN のサポート](expressroute-vpn-support.md)
