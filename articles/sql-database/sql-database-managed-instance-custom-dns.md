---
title: Azure SQL Database Managed Instance のカスタム DNS | Microsoft Docs
description: このトピックでは、Azure SQL Database マネージド インスタンスのカスタム DNS の構成オプションについて説明します。
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: d5bb2f2f4b79c4b03e631fc844a712f76fc69109
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258169"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance のカスタム DNS の構成

Azure SQL Database マネージド インスタンス (プレビュー) は、Azure [仮想ネットワーク (VNet)](../virtual-network/virtual-networks-overview.md) 内にデプロイする必要があります。 クラウドまたはハイブリッド環境内の他の SQL インスタンスにリンクされたサーバーのプライベート ホスト名をマネージド インスタンスから解決する必要のあるシナリオがいくつか存在します。 この場合は、Azure の内部でカスタム DNS を構成する必要があります。 マネージド インスタンスはその内側の作業に同じ DNS を使用するため、仮想ネットワークの DNS 構成をマネージド インスタンスと互換性があるようにする必要があります。 

カスタム DNS 構成をマネージド インスタンスと互換性があるようにするには、次の手順を完了する必要があります。 
- 要求を Azure DNS に転送するようにカスタム DNS を構成する 
- カスタム DNS を VNet のプライマリとして、Azure DNS をセカンダリとして設定する 
- カスタム DNS をプライマリとして、Azure DNS をセカンダリとして登録する

## <a name="configure-custom-dns-to-forward-requests-to-azure-dns"></a>要求を Azure DNS に転送するようにカスタム DNS を構成する 

Windows Server 2016 で DNS 転送を構成するには、次の手順を使用します。 

1. **[サーバー マネージャー]** で、**[ツール]**、**[DNS]** の順にクリックします。 

   ![DNS](./media/sql-database-managed-instance-custom-dns/dns.png) 

2. **[フォワーダー]** をダブルクリックします。

   ![[フォワーダー]](./media/sql-database-managed-instance-custom-dns/forwarders.png) 

3. **[編集]** をクリックします。 

   ![フォワーダーの一覧](./media/sql-database-managed-instance-custom-dns/forwarders-list.png) 

4. Azure の再帰リゾルバーの IP アドレス (168.63.129.16 など) を入力します。

   ![再帰リゾルバーの IP アドレス](./media/sql-database-managed-instance-custom-dns/recursive-resolvers-ip-address.png) 
 
## <a name="set-up-custom-dns-as-primary-and-azure-dns-as-secondary"></a>カスタム DNS をプライマリとして、Azure DNS をセカンダリとして設定する 
 
Azure VNet 上の DNS 構成では IP アドレスを入力する必要があるため、次の手順を使用して、静的 IP アドレスを持つ DNS サーバーをホストする Azure VM を構成します。 

1. Azure Portal で、カスタム DNS の VM ネットワーク インターフェイスを開きます。

   ![ネットワーク インターフェイス](./media/sql-database-managed-instance-custom-dns/network-interface.png) 

2. [IP 構成] セクションで、 IP 構成を選択します。 

   ![IP 構成](./media/sql-database-managed-instance-custom-dns/ip-configuration.png) 


3. プライベート IP アドレスを [静的] として設定します。 IP アドレス (このスクリーンショットでは 10.0.1.5) を書き留めます。 

   ![静的](./media/sql-database-managed-instance-custom-dns/static.png) 


## <a name="register-custom-dns-as-primary-and-azure-dns-as-secondary"></a>カスタム DNS をプライマリとして、Azure DNS をセカンダリとして登録する 

1. Azure Portal で、VNet のカスタム DNS オプションを見つけます。

   ![カスタム DNS オプション](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. [カスタム] に切り替え、カスタム DNS サーバーの IP アドレスや Azure の再帰リゾルバーの IP アドレス (168.63.129.16 など) を入力します。 

   ![カスタム DNS オプション](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > DNS の一覧で Azure の再帰リゾルバーを設定しないと、マネージド インスタンスが問題のある状態になります。 その状態から回復するには、準拠するネットワーク ポリシーを使用して VNet 内に新しいインスタンスを作成し、インスタンス レベル データを作成して、データベースを復元することが必要になる可能性があります。 「[VNet 構成](sql-database-managed-instance-vnet-configuration.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- 概要については、[マネージド インスタンス](sql-database-managed-instance.md)に関するページを参照してください。
- 新しいマネージド インスタンスの作成方法を紹介するチュートリアルが必要な場合、[マネージド インスタンスの作成](sql-database-managed-instance-create-tutorial-portal.md)に関するページを参照してください。
- マネージド インスタンスの VNet を構成する方法については、[マネージド インスタンスの VNet 構成](sql-database-managed-instance-vnet-configuration.md)に関するページを参照してください。
