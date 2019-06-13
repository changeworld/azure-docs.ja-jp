---
title: クイック スタート:Standard ロード バランサーを作成する - Azure Resource Manager テンプレート
titlesuffix: Azure Load Balancer
description: このクイックスタートでは、Azure Resource Manager テンプレートを使用して Standard ロード バランサーを作成する方法について説明します。
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer by using Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 0e47560ca43b23f4779da701f3e6f11f53a6b1ce
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480406"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>クイック スタート:VM の負荷を分散する Standard ロード バランサーを Azure Resource Manager テンプレートを使って作成する

負荷分散では、着信要求を複数の仮想マシンに分散させることで、より高いレベルの可用性とスケールを実現します。 Azure Resource Manager テンプレートをデプロイして、仮想マシン (VM) の負荷を分散するロード バランサーを作成できます。 このクイック スタートでは、Standard Load Balancer を使用して VM に負荷分散する方法を示します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-standard-load-balancer"></a>Standard Load Balancer の作成

このセクションでは、仮想マシンの負荷分散に役立つ Standard Load Balancer を作成します。 Standard Load Balancer では、Standard パブリック IP アドレスだけがサポートされています。 Standard Load Balancer を作成するときに、Standard Load Balancer のフロントエンド (既定では *LoadBalancerFrontend* という名前) として構成される新しい Standard パブリック IP アドレスも作成する必要があります。 Standard ロード バランサーの作成に使用できる方法は多数あります。 このクイックスタートでは、Azure PowerShell を使用して [Resource Manager テンプレート](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json)をデプロイします。 Resource Manager テンプレートとは、ソリューションに対してデプロイが必要なリソースを定義した JSON ファイルのことをいいます。 Azure ソリューションのデプロイと管理に関する概念を理解するには、[Azure Resource Manager のドキュメント](/azure/azure-resource-manager/)を参照してください。 Azure Load Balancer に関連したテンプレートをさらに探すには、「[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)」を参照してください。

テンプレートをデプロイするには、 **[試してみる]** を選択し、Azure Cloud Shell を開いて、シェル ウィンドウに次の PowerShell スクリプトを貼り付けます。 コードを貼り付けるには、シェル ウィンドウを右クリックして、 **[貼り付け]** を選択します。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
$adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword
```

リソース グループ名は、**rg** が追加されたプロジェクト名であることに注意してください。 次のセクションでリソース グループ名が必要になります。  リソースの作成には数分かかります。

## <a name="test-the-load-balancer"></a>Load Balancer をテストする

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側のウィンドウから **[リソース グループ]** を選択します。
1. 前のセクションで作成したリソース グループを選択します。  既定のリソース グループ名は、プロジェクト名に **rg** が追加されたものです。
1. ロード バランサーを選択します。  ロード バランサーは 1 つしかありません。 既定の名前は、プロジェクト名に **-lb** が追加されたものです。
1. パブリック IP アドレス (IP アドレス部分のみ) をコピーして、ブラウザーのアドレス バーに貼り付けます。 IIS Web サーバーの既定のページがブラウザーに表示されます。

   ![IIS Web サーバー](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

ロード バランサーが 3 つの VM すべての間でトラフィックを分散していることを確認するには、クライアント マシンから Web ブラウザーを強制的に最新の情報に更新します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、Load Balancer、および関連するすべてのリソースは、不要になったら削除します。 これを行うには、ロード バランサーを含むリソース グループを Azure portal で選択し、 **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Standard Load Balancer を作成し、それに VM をアタッチして、ロード バランサー トラフィック規則と正常性プローブを構成してから、ロード バランサーをテストしました。 Azure Load Balancer についてさらに学習するには、Azure Load Balancer のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Load Balancer のチュートリアル](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
