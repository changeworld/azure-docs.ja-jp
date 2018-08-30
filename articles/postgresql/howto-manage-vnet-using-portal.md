---
title: Azure portal を使用した Azure Database for PostgreSQL VNet サービス エンドポイントおよびルールの作成と管理 | Microsoft Docs
description: Azure portal を使用した Azure Database for PostgreSQL VNet サービス エンドポイントおよびルールの作成と管理
services: postgresql
author: mbolz
ms.author: mbolz
manager: kfile
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 08/15/2018
ms.openlocfilehash: af5df507bd7a825ddfaaa7518691cb5fbec34d47
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "42140352"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Azure portal を使用した Azure Database for PostgreSQL VNet サービス エンドポイントおよび VNet ルールの作成と管理
仮想ネットワーク (VNet) サービス エンドポイントおよびルールは、仮想ネットワークのプライベート アドレス空間を Azure Database for PostgreSQL サーバーに拡張します。 制限事項も含め、Azure Database for PostgreSQL VNet サービス エンドポイントの概要については、[Azure Database for PostgreSQL サーバー VNet サービス エンドポイント](concepts-data-access-and-security-vnet.md)に関する記事をご覧ください。 VNet サービス エンドポイントは、Azure Database for PostgreSQL でサポートされるすべてのリージョンで利用できます。

> [!NOTE]
> VNet サービス エンドポイントは、汎用サーバーとメモリ最適化サーバーでのみサポートされています。

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>VNet ルールを作成し、Azure portal でサービス エンドポイントを有効にする

1. PostgreSQL サーバー ページの [設定] 見出しで、**[接続のセキュリティ]** をクリックして Azure Database for PostgreSQL の [接続のセキュリティ] ウィンドウを開きます。 次に、**[+ 既存の仮想ネットワークを追加]** をクリックします。 既存の VNet がない場合は、**[+ 新しい仮想ネットワークの作成]** をクリックして作成できます。 「[クイック スタート: Azure Portal を使用した仮想ネットワークの作成](../virtual-network/quick-create-portal.md)」をご覧ください

   ![Azure Portal - [接続のセキュリティ] のクリック](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. VNet ルール名を入力し、サブスクリプション、仮想ネットワーク、サブネット名を選択して、**[有効にする]** をクリックします。 これにより、**Microsoft.SQL** サービス タグを使用してサブネット上で VNet サービス エンドポイントが自動的に有効になります。

   ![Azure portal - VNet の構成](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   > [!IMPORTANT]
   > サービス エンドポイントを構成する前に、サービス エンドポイントの構成と考慮事項について、この記事を読むことを強くお勧めします。 **仮想ネットワーク サービス エンドポイント:** [仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)は、プロパティ値に 1 つ以上の正式な Azure サービスの種類名が含まれるサブネットです。 VNet サービス エンドポイントでは、SQL Database という名前の Azure サービスを参照する **Microsoft.Sql** というサービス種類名を使用します。 このサービス タグは、Azure SQL Database、Azure Database for PostgreSQL および MySQL サービスにも適用されます。 VNet サービス エンドポイントに **Microsoft.Sql** サービス タグを適用すると、サブネット上の Azure SQL Database、Azure Database for PostgreSQL、Azure Database for MySQL サーバーを含むすべての Azure Database サービスに対してサービス エンドポイント トラフィックが構成されることに注意することが重要です。 
   > 

3. 有効になったら、**[OK]** をクリックすると、VNet ルールと共に VNet サービス エンドポイントが有効になっていることが表示されます。

   ![VNet サービス エンドポイントが有効で、VNet ルールが作成されている](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>次の手順
- 同様に、スクリプトを作成して、[Azure CLI を使用して VNet サービス エンドポイントを有効にし、Azure Database for PostgreSQL の VNET ルールを作成](howto-manage-vnet-using-cli.md)できます。
- Azure Database for PostgreSQL サーバーに接続する方法のヘルプについては、「[Azure Database for PostgreSQL の接続ライブラリ](./concepts-connection-libraries.md)」をご覧ください。
