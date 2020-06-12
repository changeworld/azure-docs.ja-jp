---
title: Azure Private Link サービス ARM テンプレート
description: Private Link サービス ARM テンプレート
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: article
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: 93a66057ddb0034f7ac9ac62578292ca38f2d2fe
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/31/2020
ms.locfileid: "84236411"
---
# <a name="create-a-private-link-service---resource-manager-template"></a>プライベート リンクを作成する - Resource Manager テンプレート

このクイックスタートでは、Resource Manager テンプレートを使用してプライベート リンク サービスを作成します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

このクイック スタートは、[Azure portal](create-private-link-service-portal.md)、[Azure PowerShell](create-private-link-service-powershell.md)、または [Azure CLI](create-private-link-service-cli.md) を使用して完了することもできます。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-private-link-service"></a>プライベート リンク サービスを作成する

このテンプレートは、プライベート リンク サービスを作成します。

### <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-privatelink-service/azuredeploy.json)からのものです

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json" range="001-432" highlight="263-289":::

このテンプレートには、次の複数の Azure リソースが定義されています。

- [**Microsoft.Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices) : サービスをプライベートで公開するプライベート リンク サービス
- [**Microsoft.Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints) : サービスにプライベートにアクセスするためのプライベート エンドポイント
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers) :サービスをホストする仮想マシンを公開するロード バランサー
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) :2 台の仮想マシン (1 台はサービスをホストし、もう 1 台はプライベート エンドポイントへの接続をテストします)
- [**Microsoft.Compute/virtualMachines/extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions) :Web サーバーをインストールする拡張機能
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks) : 仮想マシンごとに 1 つ
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses) :2 つのパブリック IP アドレス (仮想マシンごとに 1 つ)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) :2 つのネットワーク インターフェイス (仮想マシンごとに 1 つ)

### <a name="deploy-the-template"></a>テンプレートのデプロイ

Azure への Resource Manager テンプレートのデプロイ:

1. **[Azure に配置する]** を選択して Azure にサインインし、テンプレートを開きます。 テンプレートは、仮想マシン、標準ロード バランサー、プライベート リンク サービス、プライベート エンドポイント、ネットワーク、および検証する仮想マシンを作成します。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. リソース グループを選択または作成します。
3. 仮想マシン管理者のユーザー名とパスワードを入力します。
4. **[上記の使用条件に同意する]** を選択し、 **[購入]** を選択します。

## <a name="validate-the-deployment"></a>デプロイの検証

> [!NOTE]
> ARM テンプレートにより、仮想マシン myConsumerVm<b>{uniqueid}</b> リソースの一意の名前が生成されます。<b>{uniqueid}</b> は実際に生成された値に置き換えてください。

### <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

次のように、インターネットから VM _myConsumerVm{uniqueid}_ に接続します。

1.  ポータルの検索バーに、「_myConsumerVm{uniqueid}_ 」と入力します。

2.  **[接続]** を選択します。 **[接続]** ボタンを選択すると、 **[Connect to virtual machine]\(仮想マシンに接続する\)** が開きます。

3.  **[RDP ファイルのダウンロード]** を選択します。 リモート デスクトップ プロトコル ( _.rdp_) ファイルが作成され、お使いのコンピューターにダウンロードされます。

4.  ダウンロードされた .rdp\* ファイルを開きます。

    a. メッセージが表示されたら、 **[Connect]** を選択します。

    b. VM の作成時に指定したユーザー名とパスワードを入力します。
    
    > [!NOTE]
    > 場合によっては、 **[その他]**  >  **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。

5.  **[OK]** を選択します。

6.  サインイン処理中に証明書の警告が表示される場合があります。 証明書の警告を受信する場合は、 **[はい]** または **[続行]** を選択します。

7.  VM デスクトップが表示されたら最小化してローカル デスクトップに戻ります。

### <a name="access-the-http-service-privately-from-the-vm"></a>VM から HTTP サービスにプライベートにアクセスする

このセクションでは、プライベート エンドポイントを使用して VM から HTTP サービスに接続します。

1.  _myConsumerVm{uniqueid}_ のリモート デスクトップに移動します
2.  ブラウザーを開いて、プライベート エンドポイント アドレス http://10.0.0.5/ を入力します
3.  既定の IIS ページが表示されます

## <a name="clean-up-resources"></a>リソースをクリーンアップする

プライベート リンク サービスと共に作成したリソースが不要になった場合は、リソース グループを削除します。 これにより、プライベート リンク サービスとすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>次のステップ

- [Azure Private Link](private-link-overview.md) の詳細
