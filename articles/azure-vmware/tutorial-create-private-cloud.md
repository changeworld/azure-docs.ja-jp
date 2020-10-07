---
title: チュートリアル - Azure に vSphere クラスターをデプロイする
description: Azure VMware Solution を使用して Azure に vSphere クラスターをデプロイする方法について説明します
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 3fab49640364ef1b2e68953d366b20f77556b486
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578320"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>チュートリアル:Azure に Azure VMware Solution のプライベート クラウドをデプロイする

Azure VMware Solution を使用すると、Azure に vSphere クラスターをデプロイできます。 初期デプロイ時の最小ホスト数は 3 つです。 追加のホストは、クラスターあたり最大 16 個まで一度に 1 つずつ追加できます。 

Azure VMware Solution では、最初はオンプレミスの vCenter でプライベート クラウドを管理することができないため、追加の構成と接続が必要です。 このチュートリアルでは、これらの手順と、関連する前提条件について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure VMware Solution のプライベート クラウドを作成する
> * デプロイされたプライベート クラウドを確認する

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- プライベート クラウドを作成するための適切な管理者権限とアクセス許可。
- 適切なネットワークが構成されていることを確認するには、[ネットワークのチェックリストに関するチュートリアル](tutorial-network-checklist.md)をご確認ください。

## <a name="register-the-resource-provider"></a>リソース プロバイダーの登録

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="create-a-private-cloud"></a>プライベート クラウドを作成する

[Azure portal](#azure-portal) または [Azure CLI](#azure-cli) を使用して、Azure VMware Solution のプライベート クラウドを作成できます。

### <a name="azure-portal"></a>Azure portal

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

Azure portal の代わりに、Azure Cloud Shell が使用されている Azure CLI を使って、Azure VMware Solution のプライベート クラウドを作成できます。  Azure VMware Solution で使用できるコマンドの一覧については、[azure vmware コマンド](https://docs.microsoft.com/cli/azure/ext/vmware/vmware)のページを参照してください。

#### <a name="open-azure-cloud-shell"></a>Azure Cloud Shell を開く

コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けます。その後、**Enter** キーを押してそれを実行します。

#### <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](/cli/azure/group) コマンドを使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>プライベート クラウドを作成する

リソース グループとプライベート クラウドの名前、場所、クラスターのサイズを指定します。

| プロパティ  | 説明  |
| --------- | ------------ |
| **-g** (リソース グループ名)     | プライベート クラウド リソースのリソース グループの名前。        |
| **-n** (プライベート クラウド名)     | Azure VMware Solution のプライベート クラウドの名前。        |
| **--location**     | プライベート クラウドに使用される場所。         |
| **--cluster-size**     | クラスターのサイズ。 最小値は 3 です。         |
| **--network-block**     | プライベート クラウドに使用する CIDR IP アドレスのネットワーク ブロック。 アドレス ブロックは、サブスクリプションとオンプレミス ネットワークにある他の仮想ネットワークで使用されているアドレス ブロックと重複しないようにする必要があります。        |
| **--sku** | SKU 値: AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-an-azure-vmware-solution-private-cloud"></a>Azure VMware Solution のプライベート クラウドを削除する

Azure VMware Solution のプライベート クラウドが不要になった場合は、削除できます。 Azure VMware Solution のプライベート クラウドには、分離されたネットワーク ドメイン、専用のサーバー ノードにプロビジョニングされる 1 つ以上の vSphere クラスター、および通常は多くの仮想マシンが含まれています。 プライベート クラウドを削除すると、すべての仮想マシン、そのデータ、およびクラスターが削除されます。 専用のベアメタル ノードが安全にワイプされ、空きプールに返されます。 顧客用にプロビジョニングされたネットワーク ドメインは削除されます。  

> [!CAUTION]
> プライベート クラウドを削除する操作は、元に戻すことができません。 プライベート クラウドを削除すると、実行中のすべてのワークロードとコンポーネントが中止され、パブリック IP アドレスを含むすべてのプライベート クラウド データと構成設定が破棄されるので、データを復旧することはできなくなります。

### <a name="prerequisites"></a>前提条件

プライベート クラウドを削除した場合、仮想マシンとそのデータを復旧する方法はありません。 後で仮想マシンのデータが必要になる場合、管理者はまず、プライベート クラウドを削除する前に、すべてのデータをバックアップする必要があります。

### <a name="steps-to-delete-an-azure-vmware-solution-private-cloud"></a>Azure VMware Solution のプライベート クラウドを削除する手順

1. Azure portal で [Azure VMware Solutions] ページにアクセスします。

2. 削除するプライベート クラウドを選択します。
 
3. プライベート クラウドの名前を入力し、 **[はい]** を選択します。 数時間で、削除プロセスが完了します。  

## <a name="azure-vmware-commands"></a>Azure VMware コマンド

Azure VMware Solution で使用できるコマンドの一覧については、[azure vmware コマンド](https://docs.microsoft.com/cli/azure/ext/vmware/vmware)のページを参照してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure VMware Solution のプライベート クラウドを作成する
> * デプロイされたプライベート クラウドを確認する
> * Azure VMware Solution のプライベート クラウドを削除する

次のチュートリアルに進み、ジャンプ ボックスの作成方法を習得します。 プライベート クラウドにローカルで接続できるよう、ジャンプ ボックスを使用して環境に接続します。


> [!div class="nextstepaction"]
> [Azure VMware Solution プライベート クラウドにアクセスする](tutorial-access-private-cloud.md)
