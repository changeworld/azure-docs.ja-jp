---
title: クイック スタート:Resource Manager テンプレートを使用した Web トラフィックのルーティング
titleSuffix: Azure Application Gateway
description: Resource Manager テンプレートを使用して、Web トラフィックをバックエンド プール内の仮想マシンにルーティングする Azure Application Gateway を作成する方法を説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/23/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: cce3ef20a93c6d7a24bfa312501d2f8cc8ed9273
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604895"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---resource-manager-template"></a>クイック スタート:Azure Application Gateway による Web トラフィックのルーティング - Resource Manager テンプレート

このクイック スタートでは、Resource Manager テンプレートを使用して Azure Application Gateway を作成します。 次いで、アプリケーション ゲートウェイをテストし、それが正常に動作することを確認します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

このクイック スタートは、[Azure portal](quick-create-portal.md)、[Azure PowerShell](quick-create-powershell.md)、または [Azure CLI](quick-create-cli.md) を使用して完了することもできます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

わかりやすくするために、このテンプレートでは、パブリック フロントエンド IP、アプリケーション ゲートウェイで単一サイトをホストするための基本リスナー、基本要求ルーティング規則、およびバックエンド プール内の 2 つの仮想マシンを使用する簡単な設定を作成します。

### <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-qs/azuredeploy.json)からのものです

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json" range="001-343" highlight="197-297":::

このテンプレートには、次の複数の Azure リソースが定義されています。

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses): アプリケーション ゲートウェイ用に 1 つと仮想マシン用に 2 つ。
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : 仮想マシン用に 2 つ。
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : 仮想マシン用に 2 つ。
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : IIS と Web ページの構成用。


### <a name="deploy-the-template"></a>テンプレートのデプロイ

Azure への Resource Manager テンプレートのデプロイ:

1. **[Azure に配置する]** を選択して Azure にサインインし、テンプレートを開きます。 このテンプレートにより、アプリケーション ゲートウェイ、ネットワーク インフラストラクチャ、IIS を実行しているバックエンドプールに 2 つの仮想マシンが作成されます。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. ご自分のリソース グループを選択または作成し、仮想マシンの管理者ユーザーの名前とパスワードを入力します。
3. **[上記の使用条件に同意する]** を選択し、 **[購入]** を選択します。 このデプロイの完了には、20 分以上かかる場合があります。

## <a name="validate-the-deployment"></a>デプロイの検証

アプリケーション ゲートウェイの作成に IIS は必要はありませんが、これは Azure がアプリケーション ゲートウェイを正常に作成したかどうかを確認するためにインストールされます。 IIS を使用してアプリケーション ゲートウェイをテストします。

1. アプリケーション ゲートウェイのパブリック IP アドレスをその **[概要]** ページで見つけます。![アプリケーション ゲートウェイのパブリック IP アドレスを記録する](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) または、 **[すべてのリソース]** を選択し、検索ボックスに「*myAGPublicIPAddress*」と入力してから、検索結果でそれを選択することができます。 Azure によって、 **[概要]** ページにパブリック IP アドレスが表示されます。
2. パブリック IP アドレスをコピーし、お使いのブラウザーのアドレス バーに貼り付けて、その IP アドレスを開きます。
3. 応答を確認します。 有効な応答によって、アプリケーション ゲートウェイが正常に作成されたことが確認され、それによりバックエンドに正常に接続できます。

   ![アプリケーション ゲートウェイのテスト](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   ブラウザーを何度か更新すると、myVM1 と myVM2 の両方への接続が表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリケーション ゲートウェイと共に作成したリソースが不要になったら、リソース グループを削除します。 これによりアプリケーション ゲートウェイとすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure CLI を使用してアプリケーション ゲートウェイで Web トラフィックを管理する](./tutorial-manage-web-traffic-cli.md)
