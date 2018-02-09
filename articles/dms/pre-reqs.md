---
title: "Azure Database Migration Service を使用するための前提条件の概要 | Microsoft Docs"
description: "データベースの移行に Azure Database Migration Service を使用するための前提条件の概要について説明します。"
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: a103bb4802bc4a20b6d82f0c6bb427133d9e5643
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2018
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Azure Database Migration Service を使用するための前提条件の概要
データベースを移行するときに Azure Database Migration Service を円滑に動作させるには、いくつかの前提条件があります。 いくつかの前提条件は、サービスでサポートされているすべてのシナリオ (ソースとターゲットのペア) に適用されますが、その他の前提条件は特定のシナリオに固有のものです。

Azure Database Migration Service の使用に関連する前提条件は、以降のセクションに記載されています。

## <a name="prerequisites-common-across-migration-scenarios"></a>移行の複数のシナリオに共通の前提条件
サポートされているすべての移行シナリオで共通の、Azure Database Migration Service の前提条件は、次のとおりです。
- Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の VNET を作成します。これで、[ExpressRoute](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用したオンプレミスのソース サーバーとのサイト間接続を確立します。
- Azure Virtual Network (VNET) のネットワーク セキュリティ グループの規則によって、通信ポート 443、53、9354、445、12000 がブロックされていないことを確認します。 Azure VNET NSG トラフィックのフィルター処理の詳細については、「[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルタリング](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg)」を参照してください。
- ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>SQL Server から Azure SQL Database への移行の前提条件 
すべての移行シナリオに共通する Azure Database Migration Service の前提条件の他に、特定のシナリオだけに適用される前提条件もあります。

Azure Database Migration Service を使用して SQL Server から Azure SQL Database への移行を実行する場合は、すべての移行シナリオに共通する前提条件の他に、次の追加の前提条件にも対応してください。
- [データベース エンジン アクセスのために Windows ファイアウォール](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
- SQL Server Express のインストール時に既定では無効になっている TCP/IP プロトコルを有効にします。有効にする手順については、[サーバー ネットワーク プロトコルの有効化または無効化](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)に関する記事を参照してください。
- Azure SQL Database インスタンスを作成します。詳細な手順については、「[Azure Portal で Azure SQL データベースを作成する](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-portal)」を参照してください。
- [Data Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=53595) v3.3 以降をダウンロードしてインストールします。
- Windows ファイアウォールを開いて、Azure Database Migration Service がソース データベースにアクセスできるようにします。
- Azure SQL Database サーバーのサーバー レベルの[ファイアウォール規則](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure)を作成して、Azure Database Migration Service がターゲット データベースにアクセスできるようにします。 Azure Database Migration Service に使用される VNET のサブネット範囲を指定します。
- ソースの SQL Server インスタンスへの接続に使用される資格情報に、[CONTROL SERVER](https://docs.microsoft.com/en-us/sql/t-sql/statements/grant-server-permissions-transact-sql) アクセス許可を含めます。
- ターゲットの Azure SQL Database インスタンスへの接続に使用される資格情報に、ターゲットの Azure SQL データベースに対する CONTROL DATABASE アクセス許可を含めます。

   > [!NOTE]
   > Azure Database Migration Service を使用して SQL Server から Azure SQL Database への移行を行うために必要な前提条件の完全な一覧については、チュートリアルの「[SQL Server を Azure SQL Database に移行する](https://docs.microsoft.com/en-us/azure/dms/tutorial-sql-server-to-azure-sql)」を参照してください。
   > 

## <a name="next-steps"></a>次の手順
Azure Database Migration Service の概要と、パブリック プレビュー期間中のリージョンごとの利用可能性については、「[Azure Database Migration Service プレビューとは何ですか](dms-overview.md)」という記事を参照してください。 