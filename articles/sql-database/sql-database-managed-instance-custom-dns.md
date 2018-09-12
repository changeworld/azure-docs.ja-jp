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
ms.openlocfilehash: be32bc4b3f91fbc71305ee9f16fec17ff472cd4c
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44049577"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance のカスタム DNS の構成

Azure SQL Database マネージド インスタンス (プレビュー) は、Azure [仮想ネットワーク (VNet)](../virtual-network/virtual-networks-overview.md) 内にデプロイする必要があります。 プライベート ホスト名をマネージド インスタンスから解決する必要のあるシナリオがいくつか存在します (つまり、クラウドまたはハイブリッド環境内の他の SQL インスタンスにリンクされたサーバー)。 この場合は、Azure の内部でカスタム DNS を構成する必要があります。 マネージド インスタンスはその内側の作業に同じ DNS を使用するため、仮想ネットワークの DNS 構成をマネージド インスタンスと互換性があるようにする必要があります。 

カスタム DNS 構成をマネージド インスタンスと互換性があるようにするには、以下が必要です。 
- パブリック ドメイン名を解決できるようにカスタム DNS サーバーを構成する 
- Azure 回帰リゾルバーの DNS IP アドレス 168.63.129.16 を、仮想ネットワーク DNS リストの末尾に配置する 
 
## <a name="setting-up-custom-dns-servers-configuration"></a>カスタム DNS サーバー構成の設定

1. Azure Portal で、VNet のカスタム DNS オプションを見つけます。

   ![カスタム DNS オプション](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. [カスタム] に切り替え、カスタム DNS サーバーの IP アドレスや Azure の再帰リゾルバーの IP アドレス 168.63.129.16 を入力します。 

   ![カスタム DNS オプション](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > DNS の一覧で Azure の再帰リゾルバーを設定しないと、マネージド インスタンスが問題のある状態になります。 その状態から回復するには、準拠するネットワーク ポリシーを使用して VNet 内に新しいインスタンスを作成し、インスタンス レベル データを作成して、データベースを復元することが必要になる可能性があります。 「[VNet 構成](sql-database-managed-instance-vnet-configuration.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- 概要については、[マネージド インスタンス](sql-database-managed-instance.md)に関するページを参照してください。
- 新しいマネージド インスタンスの作成方法を紹介するチュートリアルが必要な場合、[マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)に関するページを参照してください。
- マネージド インスタンスの VNet を構成する方法については、[マネージド インスタンスの VNet 構成](sql-database-managed-instance-vnet-configuration.md)に関するページを参照してください。
