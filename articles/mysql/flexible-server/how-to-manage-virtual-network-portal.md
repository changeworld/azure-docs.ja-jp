---
title: 仮想ネットワークを管理する - Azure portal - Azure Database for MySQL - フレキシブル サーバー
description: Azure portal を使用した Azure Database for MySQL - フレキシブル サーバーの仮想ネットワークの作成と管理
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 076dc23568c2cc6570da8f7ee1e614a225568dc5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472677"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql-flexible-server-using-the-azure-portal"></a>Azure portal を使用した Azure Database for MySQL フレキシブル サーバーの仮想ネットワークの作成と管理

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


Azure Database for MySQL フレキシブル サーバーでは、フレキシブル サーバーに接続するために、2 種類の相互に排他的なネットワーク接続方法をサポートしています。 次の 2 つのオプションがあります。

- パブリック アクセス (許可された IP アドレス)
- プライベート アクセス (VNet 統合)

この記事では、Azure portal を使用した **プライベート アクセス (VNet 統合)** での MySQL サーバーの作成について重点的に説明します。 プライベート アクセス (VNet 統合) を使用すると、フレキシブル サーバーを独自の [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md) にデプロイできます。 Azure Virtual Network では、非公開で、セキュリティで保護されたネットワーク通信が提供されます。 プライベート アクセスでは、MySQL サーバーへの接続は仮想ネットワークに制限されます。 詳細については、「[プライベート アクセス (VNet 統合)](./concepts-networking-vnet.md#private-access-vnet-integration)」を参照してください。

>[!Note]
>サーバーの作成時に、フレキシブル サーバーを仮想ネットワークとサブネットにデプロイすることができます。 フレキシブル サーバーをデプロイした後は、そのサーバーを別の仮想ネットワーク、サブネット、または "*パブリック アクセス (許可された IP アドレス)* " に移動することはできません。

## <a name="prerequisites"></a>前提条件

仮想ネットワーク内にフレキシブル サーバーを作成するには、次が必要です。

- [仮想ネットワーク](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > 想ネットワークとサブネットは、フレキシブル サーバーと同じリージョンおよびサブスクリプション内に存在する必要があります。

- **Microsoft.DBforMySQL/flexibleServers** に [サブネットを委任](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service)すること。 この委任は、Azure Database for MySQL フレキシブル サーバーのみがそのサブネットを使用できることを意味します。 委任されたサブネットに他の Azure リソースの種類を含めることはできません。

## <a name="create-azure-database-for-mysql-flexible-server-in-an-already-existing-virtual-network"></a>既に存在する仮想ネットワークに Azure Database for MySQL フレキシブル サーバーを作成する

1. ポータルの左上隅にある **[リソースの作成]** (+) を選択します。
2. **[データベース]**  >  **[Azure Database for MySQL]** の順に選択します。 検索ボックスに「**MySQL**」と入力してサービスを検索することもできます。
3. デプロイ オプションとして **[フレキシブル サーバー]** を選択します。
4. **[基本]** フォームに入力します。
5. **[ネットワーク]** タブにアクセスします。
6. **[接続方法]** で、 **[プライベート アクセス (VNet 統合)]** を選択します。 **[仮想ネットワーク]** セクションに移動し、*Microsoft.DBforMySQL/flexibleServers* に委任されている既存の "*仮想ネットワーク*" と "*サブネット*" を選択するか、 *[仮想ネットワークの作成]* リンクをクリックして新たに作成します。
    > [!Note]
    > ドロップ ダウンに表示されるのは、同じリージョンとサブスクリプション内の仮想ネットワークとサブネットのみです。 </br>
    > 選択したサブネットは、*Microsoft.DBforMySQL/flexibleServers* に委任されます。 つまり、Azure Database for MySQL フレキシブル サーバーのみがそのサブネットを使用できます。</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/vnet-creation.png" alt-text="VNET 統合":::

7. 既存の **プライベート DNS ゾーン** を選択するか、新たに作成します。
    > [!NOTE]
    > プライベート DNS ゾーン名は、`mysql.database.azure.com` で終わる必要があります。 </br>
    > 新しいプライベート DNS ゾーンを作成するオプションが表示されない場合は、 **[基本]** タブにサーバー名を入力してください。</br>
    > フレキシブル サーバーを仮想ネットワークとサブネットにデプロイした後は、パブリック アクセス (許可された IP アドレス) に移動することはできません。</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/private-dns-zone.png" alt-text="dnsconfiguration":::
8. **[確認と作成]** を選択して、フレキシブル サーバーの構成を確認します。
9. **[作成]** を選択して、サーバーをプロビジョニングします。 プロビジョニングには数分かかる場合があります。

## <a name="next-steps"></a>次のステップ

- [Azure CLI を使用して Azure Database for MySQL フレキシブル サーバー仮想ネットワークを作成および管理](./how-to-manage-virtual-network-cli.md)します。
- [Azure Database for MySQL フレキシブル サーバーでのネットワーク](./concepts-networking.md)の詳細を確認します
- [Azure Database for MySQL フレキシブル サーバー仮想ネットワーク](./concepts-networking-vnet.md#private-access-vnet-integration)の詳細を理解します。
