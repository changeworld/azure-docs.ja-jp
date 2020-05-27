---
title: クイック スタート:Azure Firewall と IP グループを作成する - Resource Manager テンプレート
description: Resource Manager テンプレートを使用して、Azure Firewall と IP グループを作成する方法について説明します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: 403aaafebcae680f337aeff551b81a80a9549252
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680561"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---resource-manager-template"></a>クイック スタート:Azure Firewall と IP グループを作成する - Resource Manager テンプレート

このクイックスタートでは、Resource Manager テンプレートを使用して、ネットワーク規則とアプリケーション規則で使用されるサンプル IP グループを使って Azure Firewall をデプロイします。 IP グループは、1 つのオブジェクトに IP アドレス、範囲、およびサブネットを定義してグループ化することができる最上位リソースです。 これは Azure Firewall 規則で IP アドレスを管理する場合に便利です。 IP アドレスは手動で入力することも、ファイルからインポートすることもできます。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-azure-firewall-and-ip-groups"></a>Azure Firewall と IP グループを作成する

このテンプレートは、Azure Firewall と IP グループ、および Azure Firewall をサポートするために必要なリソースを作成します。

### <a name="review-the-template"></a>テンプレートを確認する

このクイック スタートで使用されるテンプレートは [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox)からのものです。

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json" range="001-512" highlight="118-141":::

このテンプレートには、次の複数の Azure リソースが定義されています。

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

### <a name="deploy-the-template"></a>テンプレートのデプロイ

Azure への Resource Manager テンプレートのデプロイ:

1. **[Azure に配置する]** を選択して Azure にサインインし、テンプレートを開きます。 このテンプレートは、Azure Firewall、ネットワーク インフラストラクチャ、2 つの仮想マシンを作成します。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

2. ポータルの **[Create an Azure Firewall with IpGroups]\(Azure Firewall と IpGroup の作成\)** ページで、次の値を入力または選択します。
   - サブスクリプション:既存のサブスクリプションから選択します 
   - リソース グループ: 既存のリソース グループから選択するか、 **[Create new]\(新規作成\)** を選択し、 **[OK]** を選択します。
   - 場所:場所を選択します。
   - 仮想ネットワーク名: 新しい仮想ネットワーク (VNet) の名前を入力します 
   - IP グループ名 1: IP グループ 1 の名前を入力します 
   - IP グループ名 2: IP グループ 2 の名前を入力します 
   - 管理者ユーザー名: 管理者ユーザー アカウントのユーザー名を入力します 
   - 認証: sshPublicKey またはパスワードを選択します 
   - 管理者パスワード: 管理者パスワードまたはキーを入力します

3. **[上記の使用条件に同意する]** を選択し、 **[購入]** を選択します。 デプロイの完了には、10 分以上かかる場合があります。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure portal で、デプロイされたリソース、特に IP グループを使用するファイアウォール規則を確認します。

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="IP グループ。":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="ネットワーク規則。":::

テンプレートにあるファイアウォールの JSON 構文とプロパティの詳細については、[Microsoft.Network azureFirewalls テンプレートのリファレンス](https://docs.microsoft.com/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls)に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ファイアウォールと共に作成したリソースが不要になった場合は、リソース グループを削除します。 これにより、ファイアウォールとすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:Azure portal を使用してハイブリッド ネットワークに Azure Firewall をデプロイして構成する](tutorial-hybrid-portal.md)