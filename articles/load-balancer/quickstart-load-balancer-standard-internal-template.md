---
title: 'クイックスタート: テンプレートを使用して内部ロード バランサーを作成する'
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して内部の Azure ロード バランサーを作成する方法について説明します。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: allensu
ms.date: 09/14/2020
ms.openlocfilehash: aa68dad2f8f018a9f3f70f2f02fd5e989ccbad4e
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047762"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>クイック スタート」を参照してください。ARM テンプレートを使用して VM の負荷を分散する内部ロード バランサーを作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して内部の Azure ロード バランサーを作成する方法を説明します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/201-2-vms-internal-load-balancer)からのものです。

:::code language="json" source="~/quickstart-templates/201-2-vms-internal-load-balancer/azuredeploy.json":::

テンプレートでは、複数の Azure リソースが定義されています。

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts):ブート診断用の仮想マシンのストレージ アカウント。
- [**Microsoft.Compute/availabilitySets**](/azure/templates/microsoft.compute/availabilitySets):仮想マシンの可用性セット。
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks): ロード バランサーと仮想マシン用の仮想ネットワーク。
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkInterfaces): 仮想マシンのネットワーク インターフェイス。
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): 内部ロード バランサー。
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualMachines): 仮想マシン

Azure Load Balancer に関連するテンプレートをさらに探すには、「[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)」を参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupLB"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 左側のウィンドウから **[リソース グループ]** を選択します。

3. 前のセクションで作成したリソース グループを選択します 既定のリソース グループ名は **myResourceGroupLB** です

4. 次のリソースがリソース グループ内に作成されていることを確認します。

:::image type="content" source="media/quickstart-load-balancer-standard-internal-template/verify-creation.png" alt-text="Azure portal を使用してリソースの作成を確認する。" border="true":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループとそこに含まれているすべてのリソースは、不要になったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して削除できます。

```azurecli-interactive 
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>次のステップ

テンプレートの作成手順について説明したチュートリアルについては、次のページを参照してください。

> [!div class="nextstepaction"]
> 初めての ARM テンプレートを作成してデプロイする[