---
title: チュートリアル - Azure に vSphere クラスターをデプロイする
description: Azure VMware Solution (AVS) を使用して Azure に vSphere クラスターをデプロイする方法について説明します
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: fc753f43563650357cf43c102e94f0057b62a406
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873735"
---
# <a name="tutorial-deploy-an-avs-private-cloud-in-azure"></a>チュートリアル:Azure に AVS プライベート クラウドをデプロイする

Azure VMware Solution (AVS) を使用すると、Azure に vSphere クラスターをデプロイできます。 初期デプロイ時の最小ホスト数は 3 つです。 追加のホストは、クラスターあたり最大 16 個まで一度に 1 つずつ追加できます。 

AVS では、最初はオンプレミスの vCenter でプライベート クラウドを管理することができないため、ローカルの vCenter インスタンス、仮想ネットワークなどに対して追加の構成と接続を行う必要があります。 このチュートリアル シリーズでは、これらの手順と、関連する前提条件について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * AVS プライベート クラウドを作成する
> * デプロイされたプライベート クラウドを確認する

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- プライベート クラウドを作成するための適切な管理者権限とアクセス許可。
- 適切なネットワークが構成されていることを確認するには、[ネットワークのチェックリストに関するチュートリアル](tutorial-network-checklist.md)をご確認ください。

## <a name="register-the-resource-provider"></a>リソース プロバイダーの登録

Azure VMware Solution を使用するには、最初にリソース プロバイダーを登録する必要があります。 次の例では、お使いのサブスクリプションにリソース プロバイダーを登録します。

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

リソース プロバイダーを登録するその他の方法については、「[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-private-cloud"></a>プライベート クラウドを作成する

[Azure portal](#azure-portal) または [Azure CLI](#azure-cli) を使用して、AVS プライベート クラウドを作成できます。

### <a name="azure-portal"></a>Azure portal

Azure portal で、 **[+ 新しいリソースの作成]** を選択します。 **[Marketplace を検索]** テキスト ボックスに「`Azure VMware Solution`」と入力し、一覧から **[Azure VMware Solution]** を選択します。 **[Azure VMware Solution]** ウィンドウで、 **[作成]** を選択します

**[基本]** タブで、各フィールドの値を入力します。 次の表に、詳細なプロパティ一覧を示します。

| フィールド   | 値  |
| ---| --- |
| **サブスクリプション** | デプロイに使用する予定のサブスクリプション。|
| **リソース グループ** | プライベート クラウド リソースのリソース グループ。 |
| **場所** | 場所 (**米国東部**など) を選択します。|
| **リソース名** | AVS プライベート クラウドの名前。 |
| **SKU** | 次の SKU 値を選択します。AV36 |
| **ホスト** | これは、プライベート クラウド クラスターに追加するホストの数です。 既定値は、3 です。 この値は、デプロイ後に増減できます。  |
| **vCenter 管理者パスワード** | クラウド管理者のパスワードを入力します。 |
| **NSX-T Manager のパスワード** | NSX-T 管理者のパスワードを入力します。 |
| **アドレス ブロック** | プライベート クラウドの CIDR ネットワークの IP アドレス ブロックを入力します。 例: 10.175.0.0/22。 |

:::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="プライベート クラウドを作成する" border="true":::

終わったら、 **[確認と作成]** を選択します。 次の画面で、入力した情報を確認します。 情報がすべて正しければ、 **[作成]** を選択します。

> [!NOTE]
> この手順には約 2 時間かかります。 

### <a name="azure-cli"></a>Azure CLI

また、Azure CLI を使用して、Azure に AVS プライベート クラウドを作成することもできます。 これを行うには、Azure Cloud Shell を使用します。次の手順では、これを実行する方法を示します。

#### <a name="open-azure-cloud-shell"></a>Azure Cloud Shell を開く

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 https://shell.azure.com/bash に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けます。その後、**Enter** キーを押してそれを実行します。

#### <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](/cli/azure/group) コマンドを使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>プライベート クラウドを作成する

AVS プライベート クラウドを作成するには、リソース グループ名、プライベート クラウド名、場所、クラスターのサイズを指定する必要があります


|プロパティ  |説明  |
|---------|---------|
|リソース グループ名     | プライベート クラウドのデプロイ先となるリソース グループの名前。        |
|プライベート クラウド名     | プライベート クラウドの名前。        |
|場所     | プライベート クラウドに使用される場所         |
|クラスター サイズ     | クラスターのサイズ。 最小値は 3 です。         |
|ネットワーク ブロック     | プライベート クラウドに使用する CIDR 範囲。 オンプレミス環境および Azure 環境で一意にすることをお勧めします。        |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22
```

## <a name="verify-deployment-was-successful"></a>デプロイが成功したことを確認する

作成したリソース グループに移動し、プライベート クラウドを選択します。デプロイが完了すると、次の画面が表示され、状態が **[成功]** と表示されます。

:::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="デプロイされたプライベート クラウドを検証する" border="true":::

## <a name="delete-a-private-cloud"></a>プライベート クラウドを削除する

検証した AVS プライベート クラウドがもう不要な場合は、削除できます。 プライベート クラウドを削除すると、すべてのクラスターとそのすべてのコンポーネントが削除されます。

これを行うには、Azure portal でプライベート クラウドに移動し、 **[削除]** を選択します。 確認ページでプライベート クラウドの名前を確認し、 **[はい]** を選択します。

> [!CAUTION]
> プライベート クラウドを削除する操作は、元に戻すことができません。 プライベート クラウドを削除すると、実行中のすべてのワークロードとコンポーネントが中止され、パブリック IP アドレスを含むすべてのプライベート クラウド データと構成設定が破棄されるので、データを回復することはできなくなります。 

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * AVS プライベート クラウドを作成する
> * デプロイされたプライベート クラウドを確認する

次のチュートリアルに進み、プライベート クラウド クラスターのローカル管理を設定する一環として、プライベート クラウドで使用する仮想ネットワークを作成する方法を学習します。

> [!div class="nextstepaction"]
> [仮想ネットワークの作成](tutorial-configure-networking.md)
