---
title: クイック スタート:Azure Firewall Manager プレビューを使用して仮想ハブをセキュリティで保護する - Resource Manager テンプレート
description: Azure Firewall Manager プレビューを使用して仮想ハブをセキュリティで保護する方法を学習します。
services: firewall-manager
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 05/19/2020
ms.author: victorh
ms.openlocfilehash: b9839e51fcea1e8fe4adc4760e16ae2d73b163ee
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83694136"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---resource-manager-template"></a>クイック スタート:Azure Firewall Manager を使用して仮想ハブをセキュリティで保護する - Resource Manager テンプレート

このクイック スタートでは、Resource Manager テンプレートを使用し、Azure Firewall Manager プレビューを使用して仮想ハブをセキュリティで保護します。

展開されたファイアウォールには、`www.microsoft.com` への接続を許可するアプリケーション ルールがあります。 ファイアウォールをテストするために、2 つの Windows Server 2019 仮想マシンが展開されています。 ワークロード サーバーへの接続には、1 台のジャンプ サーバーが使用されます。 ワークロード サーバーからは、`www.microsoft.com` にのみ接続できます。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Azure Firewall Manager プレビューの詳細については、「[Azure Firewall Manager プレビューとは](overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-secured-virtual-hub"></a>セキュリティ保護付き仮想ハブを作成する

このテンプレートによって、Azure Firewall Manager プレビューを使用してセキュリティ保護付き仮想ハブと、そのシナリオをサポートするために必要なリソースが作成されます。

### <a name="review-the-template"></a>テンプレートを確認する

このクイック スタートで使用されるテンプレートは [Azure クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/fwm-docs-qs/azuredeploy.json)からのものです。

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json" range="001-477" highlight="47-76":::

このテンプレートには、次の複数の Azure リソースが定義されています。

- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft.Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)

### <a name="deploy-the-template"></a>テンプレートのデプロイ

Azure への Resource Manager テンプレートのデプロイ:

1. **[Azure に配置する]** を選択して Azure にサインインし、テンプレートを開きます。 このテンプレートによって、Azure Firewall、仮想 WAN と仮想ハブ、ネットワーク インフラストラクチャ、2 つの仮想マシンが作成されます。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. ポータルの **[セキュリティ保護付き仮想ハブ]** ページで、次の値を入力または選択します。
   - サブスクリプション:既存のサブスクリプションから選択します 
   - リソース グループ: 既存のリソース グループから選択するか、 **[Create new]\(新規作成\)** を選択し、 **[OK]** を選択します。
   - 場所:場所を選択します。
   - 管理者ユーザー名: 管理者ユーザー アカウントのユーザー名を入力します 
   - 管理者パスワード: 管理者パスワードまたはキーを入力します

3. **[確認および作成]** を選択し、次に **[作成]** を選択します。 このデプロイの完了には、10 分以上かかる場合があります。

## <a name="validate-the-deployment"></a>デプロイの検証

今度は、ファイアウォール規則をテストして、予期したとおりに動作することを確認します。

1. Azure portal から、**Workload-Srv** 仮想マシンのネットワーク設定を確認し、プライベート IP アドレスをメモします。
2. リモート デスクトップを **Jump-Srv** 仮想マシンに接続し、サインインします。 そこから、**Workload-Srv** のプライベート IP アドレスへのリモート デスクトップ接続を開きます。

3. Internet Explorer を開き、 `www.microsoft.com` を参照します。
4. Internet Explorer のセキュリティ アラートで、 **[OK]**  >  **[閉じる]** の順に選択します。

   Microsoft のホーム ページが表示されるはずです。

5. `www.google.com` を参照します。

   ファイアウォールによってブロックされます。

これで、ファイアウォール ルールが動作していることを確認できました。

* 1 つの許可された FQDN は参照できますが、それ以外は参照できません。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ファイアウォールと共に作成したリソースが不要になった場合は、リソース グループを削除します。 これにより、ファイアウォールとすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [信頼されたセキュリティ パートナーについて学習する](trusted-security-partners.md)
