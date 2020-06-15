---
title: Azure プライベート エンドポイント ARM テンプレート
description: Azure Private Link について学習する
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: a91415e7e3d91c2950cc4df2235c3d58df284cc0
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235982"
---
# <a name="create-a-private-endpoint---resource-manager-template"></a>プライベート エンドポイントを作成する - Resource Manager テンプレート

このクイックスタートでは、Resource Manager テンプレートを使用してプライベート エンドポイントを作成します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

このクイック スタートは、[Azure portal](create-private-endpoint-portal.md)、[Azure PowerShell](create-private-endpoint-powershell.md)、または [Azure CLI](create-private-endpoint-cli.md) を使用して完了することもできます。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-private-endpoint"></a>プライベート エンドポイントを作成する

このテンプレートでは、Azure SQL サーバーのプライベート エンドポイントを作成します。

### <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-private-endpoint-sql/azuredeploy.json)からのものです

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json" range="001-295" highlight="131-156":::

このテンプレートには、次の複数の Azure リソースが定義されています。

- [**Microsoft.Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints): Azure SQL サーバーにプライベートにアクセスするためのプライベート エンドポイント
- [**Microsoft.Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones): プライベート エンドポイントの IP アドレスを解決するために使用されます
- [**Microsoft.Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft.Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups): プライベート エンドポイントとプライベート DNS ゾーンを関連付けます
- [**Microsoft.Sql/servers**](/azure/templates/microsoft.sql/servers): Azure SQL サーバーとサンプル データベース
- [**Microsoft.Sql/servers/databases**](/azure/templates/microsoft.sql/servers/databases): サンプル データベース
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): プライベート エンドポイントがデプロイされる Virtual Network
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): 仮想マシンにアクセスするためのパブリック IP アドレス
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): Azure SQL サーバーへのプライベート エンドポイントとのプライベート接続をテストする仮想マシン
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): 仮想マシンのネットワーク インターフェイス

### <a name="deploy-the-template"></a>テンプレートのデプロイ

Azure への Resource Manager テンプレートのデプロイ:

1. **[Azure に配置する]** を選択して Azure にサインインし、テンプレートを開きます。 このテンプレートによって、プライベート エンドポイント、Azure SQL サーバー、ネットワーク インフラストラクチャ、および検証する仮想マシンが作成されます。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. ご自分のリソース グループを選択または作成します。
3. SQL 管理者のログインとパスワードを入力します
4. 仮想マシン管理者のユーザー名とパスワードを入力します。
5. **[上記の使用条件に同意する]** を選択し、 **[購入]** を選択します。 このデプロイの完了には、20 分以上かかる場合があります。

## <a name="validate-the-deployment"></a>デプロイの検証

> [!NOTE]
> ARM テンプレートにより、仮想マシン myVm<b>{uniqueid}</b> リソースと、Azure SQL サーバー sqlserver<b>{uniqueid}</b> リソースの一意の名前が生成されます。<b>{uniqueid}</b> を実際に生成された値に置き換えてください。

### <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

次のように、インターネットから VM _myVm{uniqueid}_ に接続します。

1. ポータルの検索バーに、「_myVm{uniqueid}_ 」と入力します。

2. **[接続]** を選択します。 **[接続]** ボタンを選択すると、 **[Connect to virtual machine]\(仮想マシンに接続する\)** が開きます。

3. **[RDP ファイルのダウンロード]** を選択します。 リモート デスクトップ プロトコル ( _.rdp_) ファイルが作成され、お使いのコンピューターにダウンロードされます。

4. ダウンロードされた .rdp\* ファイルを開きます。

   a. メッセージが表示されたら、 **[Connect]** を選択します。

   b. VM の作成時に指定したユーザー名とパスワードを入力します。

      > [!NOTE]
      > 場合によっては、 **[その他]**  >  **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。

5. **[OK]** を選択します。

6. サインイン処理中に証明書の警告が表示される場合があります。 証明書の警告を受信する場合は、 **[はい]** または **[続行]** を選択します。

7. VM デスクトップが表示されたら最小化してローカル デスクトップに戻ります。

### <a name="access-sql-database-server-privately-from-the-vm"></a>VM から SQL Database サーバーにプライベートにアクセスする

このセクションでは、プライベート エンドポイントを使用して VM から SQL Database サーバーに接続します。

1.  _myVM{uniqueid}_ のリモート デスクトップで、PowerShell を開きます。
2.  「nslookup sqlserver{uniqueid}.database.windows.net」と入力します。 このようなメッセージを受け取ります。

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  SQL Server Management Studio をインストールします。
4.  [サーバーに接続] で、次の情報を入力または選択します。サーバーの種類:[データベース エンジン] を選択します。
    サーバー名:sqlserver{uniqueid}.database.windows.net を選択します。ユーザー名: 作成時に指定したユーザー名を入力します。
    Password (パスワード):作成時に指定したパスワードを入力します。
    パスワードを保存する:[はい] を選択します。

5.  **[接続]** を選択します。
6.  左側のメニューで **[データベース]** を参照します。
7.  (省略可能) 情報を作成するか、_sample-db_ に対して情報のクエリを実行します
8.  _myVm{uniqueid}_ へのリモート デスクトップ接続を閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

プライベート エンドポイントと共に作成したリソースが不要になった場合は、リソース グループを削除します。 これにより、プライベート エンドポイントとすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>次のステップ

- [Azure Private Link](private-link-overview.md) の詳細
