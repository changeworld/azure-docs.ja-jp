---
title: Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる
description: Azure-SSIS 統合ランタイムを Azure 仮想ネットワークに参加させる方法について説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 023b6898b5ba7d7d5b457f1e0f9c9051d74a1126
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2021
ms.locfileid: "129399776"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory で SQL Server Integration Services (SSIS) を使用する場合、次のシナリオでは、Azure-SSIS 統合ランタイム (IR) を Azure 仮想ネットワークに参加させる必要があります。

- セルフホステッド IR をプロキシとして構成または管理することなく、Azure-SSIS IR 上で実行される SSIS パッケージからオンプレミス データ ストアに接続する必要がある。 

- IP ファイアウォール規則または仮想ネットワーク サービス エンドポイントを備えた Azure SQL Database、あるいはプライベート エンドポイントを備えた SQL Managed Instance で SSIS カタログ データベース (SSISDB) をホストする必要がある。 

- Azure-SSIS IR 上で実行される SSIS パッケージから、仮想ネットワーク サービス エンドポイントで構成された Azure リソースに接続する必要がある。

- Azure-SSIS IR 上で実行される SSIS パッケージから、IP ファイアウォール規則で構成されたデータ ストアまたはリソースに接続する必要がある。

Data Factory では、クラシック デプロイ モデルまたは Azure Resource Manager デプロイ モデルで作成された仮想ネットワークに Azure-SSIS IR を参加させることができます。

> [!IMPORTANT]
> 従来の仮想ネットワークは非推奨になるため、代わりに Azure Resource Manager 仮想ネットワークを使用してください。  既に従来の仮想ネットワークを使っている場合は、できるだけ早期に Azure Resource Manager 仮想ネットワークに切り替えます。

「[仮想ネットワークに参加するように Azure-SQL Server Integration Services (SSIS) 統合ランタイム (IR) を構成する](tutorial-deploy-ssis-virtual-network.md)」のチュートリアルでは、Azure portal を使用した最小限の手順を示しています。 この記事ではそのチュートリアルをさらに掘り下げて、オプションのタスクすべてについて説明します。

- 仮想ネットワーク (クラシック) を使用する。
- Azure-SSIS IR に独自のパブリック IP アドレスを使用する。
- 独自のドメイン ネーム システム (DNS) サーバーを使用する。
- サブネット上でネットワーク セキュリティ グループ (NSG) を使用する。
- Azure ExpressRoute またはユーザー定義ルート (UDR) を使用する。
- カスタマイズされた Azure-SSIS IR を使用する。
- Azure PowerShell のプロビジョニングを使用する。

## <a name="access-to-on-premises-data-stores"></a>オンプレミスのデータ ストアにアクセスする

SSIS パッケージがオンプレミスのデータ ストアにアクセスする場合は、オンプレミスのネットワークに接続されている仮想ネットワークに Azure-SSIS IR を参加させることができます。 または、セルフホステッド IR を Azure-SSIS IR のプロキシとして構成して管理することができます。 詳細については、[セルフホステッド IR を Azure-SSIS IR のプロキシとして構成する](./self-hosted-integration-runtime-proxy-ssis.md)方法に関する記事を参照してください。 

Azure-SSIS IR を仮想ネットワークに参加させる場合は、次の重要な点に注意してください。 

- 仮想ネットワークがオンプレミス ネットワークに接続されていない場合は、まず Azure-SSIS IR を参加させる [Azure Resource Manager 仮想ネットワーク](../virtual-network/quick-create-portal.md#create-a-virtual-network)を作成します。 次に、その仮想ネットワークからオンプレミス ネットワークへのサイト間 [VPN ゲートウェイ接続](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)または [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 接続を構成します。 

- Azure Resource Manager 仮想ネットワークが既に Azure-SSIS IR と同じ場所にあるオンプレミス ネットワークに接続されている場合は、その仮想ネットワークに IR を参加させることができます。 

- 従来の仮想ネットワークが、Azure-SSIS IR とは異なる場所にあるオンプレミス ネットワークに既に接続されている場合は、Azure-SSIS IR を参加させる [Azure Resource Manager 仮想ネットワーク](../virtual-network/quick-create-portal.md#create-a-virtual-network)を作成できます。 次に、[クラシックと Azure Resource Manager の間の仮想ネットワーク](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)接続を構成します。 
 
- Azure Resource Manager 仮想ネットワークが Azure-SSIS IR とは異なる場所にあるオンプレミス ネットワークに既に接続されている場合は、まず Azure-SSIS IR を参加させる [Azure Resource Manager 仮想ネットワーク](../virtual-network/quick-create-portal.md#create-a-virtual-network)を作成できます。 次に、[Azure Resource Management と Azure Resource Manager 間の仮想ネットワーク](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)接続を構成します。 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>SQL Database での SSIS カタログのホスト

仮想ネットワーク サービス エンドポイントを備えた Azure SQL Database で SSIS カタログをホストする場合は、必ず、Azure SSIS IR を同じ仮想ネットワークとサブネットに参加させるようにします。

プライベート エンドポイントを備えた SQL Managed Instance で SSIS カタログをホストする場合は、必ず、Azure-SSIS IR を同じ仮想ネットワークに参加させる必要があります (ただし、サブネットはマネージド インスタンスと異なる)。 SQL Managed Instance とは異なる仮想ネットワークに Azure-SSIS IR を参加させるには、仮想ネットワーク ピアリング (同じリージョンに限定される)、または仮想ネットワークから仮想ネットワークへの接続のいずれかをお勧めします。 詳細については、「[Azure SQL Managed Instance にアプリケーションを接続する](../azure-sql/managed-instance/connect-application-instance.md)」を参照してください。

## <a name="access-to-azure-services"></a>Azure サービスへのアクセス

SSIS パッケージで、[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)をサポートする Azure リソースにアクセスするときに、Azure-SSIS IR からのそれらのリソースへのアクセスをセキュリティで保護する場合は、仮想ネットワーク サービス エンドポイント用に構成された仮想ネットワーク サブネットに Azure-SSIS IR を参加させてから、同じサブネットからのアクセスを許可するために関連する Azure リソースに仮想ネットワーク規則を追加できます。

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>IP ファイアウォール規則によって保護されているデータ ソースへのアクセス

SSIS パッケージで、特定の静的パブリック IP アドレスのみを許可するデータ ストアおよびリソースにアクセスするときに、Azure-SSIS IR からのそれらのリソースへのアクセスをセキュリティで保護する必要がある場合は、Azure-SSIS IR を仮想ネットワークに参加させながら[パブリック IP アドレス](../virtual-network/virtual-network-public-ip-address.md)を関連付け、それらの IP アドレスからのアクセスを許可するために関連するリソースに IP ファイアウォール規則を追加することができます。 これを行うには、次の 2 つの方法があります。 

- Azure-SSIS IR を作成するときに、独自のパブリック IP アドレスを使用し、[Azure Data Factory Studio UI](join-azure-ssis-integration-runtime-virtual-network-ui.md) または [Azure PowerShell SDK](join-azure-ssis-integration-runtime-virtual-network-powershell.md) を介して指定することができます。 指定したパブリック IP アドレスが使用されるのは、Azure-SSIS IR の送信インターネット接続のみであり、サブネット内の他のデバイスでは使用されません。
- サブネットに対して、Azure-SSIS IR を参加させ、このサブネット内のすべての送信接続で指定したパブリック IP アドレスが使用されるように [Virtual Network NAT](../virtual-network/nat-gateway/nat-overview.md) を設定することもできます。

すべての場合に、仮想ネットワークは Azure Resource Manager デプロイ モデルでのみデプロイすることができます。

## <a name="next-steps"></a>次のステップ

- [Azure-SSIS 統合ランタイムの仮想ネットワーク構成の詳細](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Azure Data Factory Studio UI を使用して Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Azure PowerShell を使用して Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR の詳細については、次の記事を参照してください。 
- 「[Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)」。 この記事では、Azure-SSIS IR を含め、IR に関する全般的な概念情報が説明されています。 
- [チュートリアル:Azure への SSIS パッケージのデプロイに関するチュートリアル](./tutorial-deploy-ssis-packages-azure.md)の手順に従って作成します。 このチュートリアルでは、Azure-SSIS IR の作成手順を示しています。 Azure SQL Database を使って SSIS カタログをホストします。 
- 「[Azure-SSIS IR を作成する](create-azure-ssis-integration-runtime.md)」。 この記事は、このチュートリアルを拡張しています。 仮想ネットワーク サービス エンドポイントまたは仮想ネットワーク内の SQL Managed Instance を含む Azure SQL Database を使用して、SSIS カタログをホストする手順について説明されます。 Azure-SSIS IR を仮想ネットワークに参加させる方法が示されます。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR に関する情報を取得する方法が説明されています。 返された情報の状態の説明が提供されます。 
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示しています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。
