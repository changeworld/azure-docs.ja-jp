---
title: Azure VMware Solution のプライベート クラウドを作成する
description: Azure portal を使用して、Azure VMware Solution のプライベート クラウドを作成する手順です。
ms.topic: include
ms.date: 08/05/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: bf607b4db54b9ec3c4eb481cf9917873be4411ad
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638687"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Azure VMware Solution のプライベート クラウドを、Azure portal または Azure CLI を使用して作成できます。


### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[新しいリソースを作成]** を選択します。 

1. **[Marketplace を検索]** テキスト ボックスに「`Azure VMware Solution`」と入力して、それを結果から選択します。 

1. **[Azure VMware Solution]** ウィンドウで、 **[作成]** を選択します。

1. **[基本]** タブで、各フィールドに値を入力し、 **[確認と作成]** を選択します。 

   >[!TIP]
   >この情報は、このクイック スタートの[計画フェーズ](../plan-private-cloud-deployment.md)で収集しました。

   | フィールド   | 値  |
   | ---| --- |
   | **サブスクリプション** | デプロイに使用する予定のサブスクリプションを選択します。 Azure サブスクリプションのすべてのリソースがまとめて課金されます。|
   | **リソース グループ** | プライベート クラウドのリソース グループを選択します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 代わりに、プライベート クラウド用の新しいリソース グループを作成することもできます。 |
   | **リソース名** | Azure VMware Solution のプライベート クラウドの名前を指定します。 |
   | **場所** | 場所 (**米国東部** など) を選択します。 これは、計画フェーズ中に定義した "*リージョン*" です。 |
   | **ホストのサイズ** | **[AV36]** を選択します。 |
   | **ホストの数** | プライベート クラウド クラスターに割り当てられているホストの数。 既定値は 3 です。この値は、デプロイ後に増減できます。  |
   | **プライベート クラウドのアドレス ブロック** | プライベート クラウドの IP アドレス ブロックを指定します。  CIDR はプライベート クラウド管理ネットワークを表しており、クラスター管理サービス (vCenter Server、NSX-T Manager など) に使用されます。 10.175.0.0/22 など、/22 アドレス空間を使用します。  アドレスは一意であり、他の Azure Virtual Network やオンプレミス ネットワークと重複することはできません。 |
   

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="[プライベート クラウドの作成] ウィンドウの [基本] タブを示すスクリーンショット。" border="true":::

1. 入力した情報を確認し、正しい場合は **[作成]** を選択します。  

   > [!NOTE]
   > この手順には、3 時間から 4 時間ほどかかります。 既存のクラスターまたは同じクラスターに 1 つのホストを追加するのにかかる時間は、30 分から 45 分です。

1. デプロイが成功したことを確認します。 作成したリソース グループに移動し、プライベート クラウドを選択します。  デプロイが終了すると、状態が **[成功]** として表示されます。 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="デプロイが成功したことを示すスクリーンショット。" border="true":::


### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure portal の代わりに、Azure Cloud Shell が使用されている Azure CLI を使って、Azure VMware Solution のプライベート クラウドを作成できます。 Azure VMware Solution で使用できるコマンドの一覧については、[Azure VMware コマンド](/cli/azure/ext/vmware/vmware)のページを参照してください。

Azure CLI の使用を開始するには:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]


1. ['az group create'](/cli/azure/group) コマンドを使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

   ```azurecli-interactive
   
   az group create --name myResourceGroup --location eastus
   ```

2. リソース グループとプライベート クラウドの名前、場所、クラスターのサイズを指定します。

   | プロパティ  | 説明  |
   | --------- | ------------ |
   | **-g** (リソース グループ名)     | プライベート クラウド リソースのリソース グループの名前。        |
   | **-n** (プライベート クラウド名)     | Azure VMware Solution のプライベート クラウドの名前。        |
   | **--location**     | プライベート クラウドに使用されるリージョン。         |
   | **--cluster-size**     | クラスターのサイズ。 最小値は 3 です。         |
   | **--network-block**     | プライベート クラウドに使用する CIDR IP アドレスのネットワーク ブロック。 アドレス ブロックは、サブスクリプションとオンプレミス ネットワークにある他の仮想ネットワークで使用されているアドレス ブロックと重複しないようにする必要があります。        |
   | **--sku** | SKU 値: AV36 |

   ```azurecli-interactive 
   az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
   ```
