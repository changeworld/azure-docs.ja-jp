---
title: 仮想ネットワークを管理する - Azure CLI - Azure Database for PostgreSQL - フレキシブル サーバー
description: Azure CLI を使用して Azure Database for PostgreSQL - フレキシブル サーバーの仮想ネットワークを作成して管理する
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 830a97db562820853efcd88b1ab8c0b729a5dc9a
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490137"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Azure CLI を使用して Azure Database for PostgreSQL - フレキシブル サーバーの仮想ネットワークを作成して管理する

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

Azure Database for PostgreSQL - フレキシブル サーバーによって、ご利用のフレキシブル サーバーに接続するために、2 種類の相互に排他的なネットワーク接続方法がサポートされています。 次の 2 つのオプションがあります。

* パブリック アクセス (許可された IP アドレス)
* プライベート アクセス (VNet 統合)

この記事では、 **プライベート アクセス (VNet 統合)** を使用する PostgreSQL サーバーを、Azure CLI を使用して作成することに焦点を当てます。 *プライベート アクセス (VNet 統合)* を使用すると、フレキシブル サーバーを独自の [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md) にデプロイできます。 Azure Virtual Network では、非公開で、セキュリティで保護されたネットワーク通信が提供されます。 プライベート アクセスでは、PostgreSQL サーバーへの接続は仮想ネットワーク内のみに制限されます。 詳細については、「[プライベート アクセス (VNet 統合)](./concepts-networking.md#private-access-vnet-integration)」を参照してください。

Azure Database for PostgreSQL - フレキシブル サーバーでは、サーバーの作成時にのみ、サーバーを仮想ネットワークとサブネットにデプロイできます。 フレキシブル サーバーを仮想ネットワークとサブネットにデプロイした後は、別の仮想ネットワーク、サブネット、または " *パブリック アクセス (許可された IP アドレス)* " に移動することはできません。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

[Azure Cloud Shell](../../cloud-shell/overview.md) は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を開くこともできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、 **[入力]** を選択して実行します。

CLI をローカルにインストールして使用する場合、このクイックスタートでは、Azure CLI バージョン 2.0 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

[az login](/cli/azure/reference-index#az-login) コマンドを使用してアカウントにサインインする必要があります。 **ID** プロパティに注意してください。これは、お使いの Azure アカウントの **サブスクリプション ID** のことです。

```azurecli-interactive
az login
```

[az account set](/cli/azure/account#az-account-set) コマンドを使用して、アカウントの特定のサブスクリプションを選択します。 **az login** 出力の **ID** の値をメモしておいて、このコマンドの **subscription** 引数の値として使用します。 複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 すべてのサブスクリプションを取得するには、[az account list](/cli/azure/account#az-account-list) を使用します。

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-postgresql---flexible-server-using-cli"></a>CLI を使用して Azure Database for PostgreSQL - フレキシブル サーバーを作成する
`az postgres flexible-server` コマンドを使用して、 *プライベート アクセス (VNet 統合)* を使用するフレキシブル サーバーを作成できます。 このコマンドでは、既定の接続方法としてプライベート アクセス (VNet 統合) を使用します。 何も指定しない場合は、仮想ネットワークとサブネットが自動的に作成されます。 サブネット ID を使用して、既に存在する仮想ネットワークとサブネットを指定することもできます。 <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> 次の例に示すように、CLI を使用してフレキシブル サーバーを作成するためのさまざまなオプションがあります。

>[!Important]
> このコマンドを使用すると、サブネットが **Microsoft.DBforPostgreSQL/flexibleServers** に委任されます。 この委任は、Azure Database for PostgreSQL フレキシブル サーバーのみがそのサブネットを使用できることを意味します。 委任されたサブネットに他の Azure リソースの種類を含めることはできません。
>
構成可能な CLI パラメーターの完全な一覧については、 <!--FIXME --> Azure CLI リファレンス ドキュメントを参照してください。 たとえば、次のコマンドでは、必要に応じてリソース グループを指定できます。

- 既定の仮想ネットワーク、既定のアドレス プレフィックスを持つサブネットを使用して、フレキシブル サーバーを作成する
    ```azurecli-interactive
    az postgres flexible-server create
    ```
<!--- Create a flexible server using already existing virtual network and subnet
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --subnet mySubnet
    ```-->
- 既存の仮想ネットワーク、サブネット、およびサブネット ID を使用して、フレキシブル サーバーを作成します。 指定するサブネット内に他のリソースがデプロイされていてはならず、このサブネットは、まだ委任されていない場合は **Microsoft.DBforPostgreSQL/flexibleServers** に委任されます。
    ```azurecli-interactive
    az postgres flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > 想ネットワークとサブネットは、フレキシブル サーバーと同じリージョンおよびサブスクリプション内に存在する必要があります。
<!--  
- Create a flexible server using new virtual network, subnet with non-default address prefix
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --vnet-address-prefix 10.0.0.0/24 --subnet mySubnet --subnet-address-prefix 10.0.0.0/24
    ```-->
構成可能な CLI パラメーターの完全な一覧については、 <!--FIXME --> Azure CLI リファレンス ドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ
- [Azure Database for PostgreSQL - フレキシブル サーバーのネットワーク](./concepts-networking.md)の詳細を確認します。
- [Azure portal を使用して Azure Database for PostgreSQL - フレキシブル サーバーの仮想ネットワークを作成し、管理します](./how-to-manage-virtual-network-portal.md)。
- [Azure Database for PostgreSQL - フレキシブル サーバーの仮想ネットワーク](./concepts-networking.md#private-access-vnet-integration)について理解を深めます。