---
title: Azure Purview ネットワーク アーキテクチャとベスト プラクティス
description: この記事では、Azure Purview ネットワーク アーキテクチャ オプションの例を提供し、ベスト プラクティスについて説明します。
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: 04121992c3de5e061e54687c8d23469850607870
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073983"
---
# <a name="azure-purview-network-architecture-and-best-practices"></a>Azure Purview ネットワーク アーキテクチャとベスト プラクティス

Azure Purview は、データ ガバナンス用のサービスとしてのプラットフォーム (PaaS) ソリューションです。 Azure Purview アカウントには、サービスに接続するためにインターネット経由でアクセスできるパブリック エンドポイントがあります。 しかし、すべてのエンドポイントは、Azure Active Directory (Azure AD) ログインおよびロールベースのアクセス制御 (RBAC) を使用してセキュリティで保護されます。

追加のセキュリティ層のために、Azure Purview アカウントのプライベート エンドポイントを作成することができます。 その後、Azure の仮想ネットワークから Azure Purview アカウントとその管理対象リソースへのプライベート IP アドレスを取得します。 このアドレスにより、ユーザーによる API と Azure Purview Studio の操作、またはスキャンとインジェストの際に、仮想ネットワークと Azure Purview アカウント間のすべてのトラフィックがプライベート リンクに制限されます。 

現在、Azure Purview ファイアウォールで、Purview アカウントのパブリック エンドポイントへのアクセスを制御できます。 ファイアウォールを使って、プライベート エンドポイントの使用時にパブリック エンドポイント経由のすべてのアクセスを許可、またはすべてのアクセスをブロックできます。 

ネットワーク、接続、およびセキュリティの要件に基づいて、Azure Purview アカウントを設定および管理し、基になるサービスやインジェストにアクセスできます。 このベスト プラクティス ガイドを使用して、ネットワークまたはクラウド内のさまざまな場所から、Azure Purview にアクセスし、データ ソースをスキャンできるようにネットワーク環境を定義して準備します。 

このガイドでは次のネットワーク オプションについて説明します。 

- [Azure パブリック エンドポイント](#option-1-use-public-endpoints)を使用する。 
- [プライベート エンドポイント](#option-2-use-private-endpoints)を使用する。 
- [プライベート エンドポイントを使用し、同じ Azure Purview アカウントでパブリック アクセスを許可する](#option-3-use-both-private-and-public-endpoints)。 

このガイドでは、Azure Purview の最も一般的なネットワーク アーキテクチャ シナリオのいくつかについて説明します。 これらのシナリオに限定されるわけではありませんが、Azure Purview アカウントのネットワークを計画している場合は、サービスの[制限事項](#current-limitations)に留意してください。 

このガイドの対象読者は次のとおりです。

- データ アーキテクチャ チーム 
- ネットワーク チーム  
- データ セキュリティ チーム 

## <a name="prerequisites"></a>前提条件

ご利用の環境に最適なネットワーク オプションを理解するために、まず次のアクションを実行することをお勧めします。 

- Azure Purview でデータ ソースを登録してスキャンする前に、ネットワーク トポロジとセキュリティ要件を確認します。 詳細については、「[Azure のネットワーク トポロジを定義する](/azure/cloud-adoption-framework/ready/azure-best-practices/define-an-azure-network-topology)」を参照してください。 

- [PaaS サービスのネットワーク接続モデル](/azure/cloud-adoption-framework/ready/azure-best-practices/connectivity-to-azure-paas-services)を定義します。 

## <a name="option-1-use-public-endpoints"></a>オプション 1: パブリック エンドポイントを使用する 

既定で、インターネットからアクセスできるパブリック エンドポイント経由で Azure Purview アカウントを使用できます。 次の要件がある場合は、Azure Purview アカウントでパブリック ネットワークを許可します。 

- Azure Purview エンドポイントをスキャンまたはそれに接続するときに、プライベート接続は必要ありません。 
- データ ソースはすべて SaaS アプリケーションのみです。 
- すべてのデータ ソースに、インターネット経由でアクセスできるパブリック エンドポイントがあります。 
- ビジネス ユーザーは、インターネット経由で Azure Purview アカウントと Azure Purview Studio にアクセスする必要があります。 

### <a name="integration-runtime-options"></a>統合ランタイムのオプション 

Azure Purview アカウント ファイアウォールがパブリック アクセスを許可するように設定されている場合にデータ ソースをスキャンするために、Azure 統合ランタイムと[セルフホステッド統合ランタイム](./manage-integration-runtimes.md)の両方を使用できます。 それらの使用方法は、[データ ソースのサポートの可否](manage-data-sources.md)によって異なります。  

いくつかのベスト プラクティスを次に示します。

- Azure 統合ランタイムまたはセルフホステッド統合ランタイムを使用して、Azure SQL Database や Azure Blob Storage などの Azure データ ソースをスキャンできます。 コストと管理オーバーヘッドを減らすために、可能であれば Azure 統合ランタイムを使用して、Azure データ ソースをスキャンすることをお勧めします。 
  
- 複数の Azure データ ソースをスキャンするには、パブリック ネットワークと Azure 統合ランタイムを使用します。 次の手順では、Azure 統合ランタイムを使用して Azure のデータ ソースをスキャンする場合の通信フローを大まかに示しています。

  :::image type="content" source="media/concept-best-practices/network-azure-runtime.png" alt-text="Azure Purview、Azure ランタイム、データ ソース間の接続フローを示すスクリーンショット。"lightbox="media/concept-best-practices/network-azure-runtime.png":::

  1. 手動または自動スキャンは、Azure 統合ランタイムを使用して Azure Purview データ マップから開始されます。 
   
  2. Azure 統合ランタイムでは、データ ソースに接続してメタデータが抽出されます。

  3. メタデータは Azure Purview のマネージド ストレージのキューに登録され、Azure Blob Storage に格納されます。 

  4. メタデータは Azure Purview データ マップに送信されます。 

- オンプレミスと VM ベースのデータ ソースのスキャンには、常にセルフホステッド統合ランタイムを使用する必要があります。 Azure 統合ランタイムは、これらのデータ ソースではサポートされていません。 次の手順では、セルフホステッド統合ランタイムを使用してデータ ソースをスキャンする場合の通信フローを大まかに示しています。

  :::image type="content" source="media/concept-best-practices/network-self-hosted-runtime.png" alt-text="Azure Purview、セルフホステッド ランタイム、データ ソース間の接続フローを示すスクリーンショット。"lightbox="media/concept-best-practices/network-self-hosted-runtime.png":::

  1. 手動または自動スキャンがトリガーされます。 Azure Purview では、Azure Key Vault に接続して、データ ソースにアクセスするための資格情報が取得されます。
   
  2. セルフホステッド統合ランタイムを使用して、Azure Purview データ マップからスキャンが開始されます。 
   
  3. VM からのセルフホステッド統合ランタイム サービスでは、データ ソースに接続してメタデータが抽出されます。

  4. メタデータは、セルフホステッド統合ランタイムの VM メモリで処理されます。 メタデータは、Azure Purview のマネージド ストレージのキューに登録されてから、Azure Blob Storage に格納されます。 

  5. メタデータは Azure Purview データ マップに送信されます。 

### <a name="authentication-options"></a>認証オプション  

Azure Purview でデータ ソースをスキャンする場合は、資格情報を指定する必要があります。 その後、Azure Purview では、ターゲット データ ソースで Azure 統合ランタイムを使用して、資産のメタデータを読み取ることができます。 パブリック ネットワークを使用する場合、認証オプションと要件は次の要因によって異なります。 

- **データ ソースの種類**。 たとえば、データ ソースが Azure SQL Database である場合、各データベースへの db_datareader アクセス権で SQL 認証を使用する必要があります。 これは、ユーザー マネージド ID または Azure Purview マネージド ID にすることができます。 あるいは、SQL Database に db_datareader として追加された Azure Active Directory 内のサービス プリンシパルにすることができます。 

  データ ソースが Azure Blob Storage である場合は、Azure ストレージ アカウントで Blob Storage のデータ閲覧者ロールとして追加された Azure Active Directory 内の Azure Purview マネージド ID またはサービス プリンシパルを使用することができます。 または、単にストレージ アカウントのキーを使用します。  

- **認証の種類**。 管理オーバーヘッドを減らすために、可能であれば Azure Purview マネージド ID を使用して、Azure データ ソースをスキャンすることをお勧めします。 その他の認証の種類については、[Azure Purview 内でソース認証用の資格情報を設定する](manage-credentials.md)必要があります。 

  1. Azure Key Vault 内にシークレットを生成します。 
  1. Azure Purview 内にキー コンテナーを登録します。  
  1. Azure Purview 内で、キー コンテナーに保存されているシークレットを使用して新しい資格情報を作成します。 

- **スキャンで使用されるランタイムの種類**。 現在、セルフホステッド統合ランタイムで Azure Purview マネージド ID を使用することはできません。 

### <a name="additional-considerations"></a>その他の注意点  

- パブリック エンドポイントを使用してデータ ソースをスキャンする場合は、オンプレミスまたは VM ベースのデータ ソースに Azure エンドポイントへの送信接続が必要です。 

- セルフホステッド統合ランタイム VM には、[Azure エンドポイントへの送信接続](manage-integration-runtimes.md#networking-requirements)が必要です。 

- Azure データ ソースではパブリック アクセスを許可する必要があります。 データ ソースでサービス エンドポイントが有効になっている場合は、必ず、Azure データ ソースへのアクセスを ''_信頼されているサービスの一覧にある Azure サービスに許可する_'' ようにしてください。 サービス エンドポイントでは、仮想ネットワークからのトラフィックが最適なパスを経由して Azure にルーティングされます。 

## <a name="option-2-use-private-endpoints"></a>オプション 2: プライベート エンドポイントを使用する 

Azure Purview アカウントに [Azure プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用することができます。 このオプションは、次のいずれかを実行する必要がある場合に便利です。

- プライベート接続を介して、Azure 仮想ネットワーク内の Azure のサービスとしてのインフラストラクチャ (IaaS) および PaaS データ ソースとオンプレミスのデータ ソースをスキャンする。
- 仮想ネットワーク上のユーザーが [Azure Private Link](../private-link/private-link-overview.md) 経由で Azure Purview に安全にアクセスできるようにする。 

他の PaaS ソリューションと同様に、Azure Purview では仮想ネットワークに直接デプロイすることがサポートされていません。 そのため、ネットワーク セキュリティ グループ、ルート テーブル、その他のネットワーク依存アプライアンス (Azure Firewall など) のようなオファリングのリソースでは、特定のネットワーク機能を使用できません。 代わりに、仮想ネットワークで有効にできるプライベート エンドポイントを使用できます。 その後、パブリック インターネット アクセスを無効にして、Azure Purview に安全に接続できます。 

次のいずれかの要件がある場合は、Azure Purview アカウントにプライベート エンドポイントを使用する必要があります。 

- Azure Purview アカウントとデータ ソースにエンドツーエンドのネットワークの分離を使用する必要がある。 

- Azure Purview アカウントへの[パブリック アクセスをブロックする](./catalog-private-link-end-to-end.md#firewalls-to-restrict-public-access)必要がある。 

- PaaS データ ソースがプライベート エンドポイントを使用してデプロイされており、パブリック エンドポイント経由のすべてのアクセスをブロックしている。 

- オンプレミスまたは IaaS データ ソースからパブリック エンドポイントに到達することができない。 

### <a name="design-considerations"></a>設計上の考慮事項  

- Azure Purview アカウントにプライベートでかつ安全に接続するには、アカウントおよびポータル プライベート エンドポイントをデプロイする必要があります。 たとえば、API 経由で Azure Purview に接続する場合や、Azure Purview Studio を使用する場合は、このデプロイが必要です。

- プライベート エンドポイントを使用して Azure Purview Studio に接続する必要がある場合は、アカウントおよびポータル プライベート エンドポイントの両方をデプロイする必要があります。 

- プライベート接続経由でデータ ソースをスキャンするには、Azure Purview 用に少なくとも 1 つのアカウントおよび 1 つのインジェスト プライベート エンドポイントを構成する必要があります。 Azure Purview マネージド ID 以外の認証方法を使用し、セルフホステッド統合ランタイムを使ってスキャンを構成する必要があります。 

- スキャンを設定する前に、「["インジェスト" プライベート エンドポイントを介してデータ ソースをスキャンするためのマトリックスをサポートする](catalog-private-link.md#support-matrix-for-scanning-data-sources-through-ingestion-private-endpoint)」を確認してください。

- [DNS の要件](catalog-private-link-name-resolution.md#deployment-options)を確認してください。 ネットワーク上でカスタム DNS サーバーを使用している場合は、クライアントで、Azure Purview アカウント エンドポイントの完全修飾ドメイン名 (FQDN) をプライベート エンドポイントの IP アドレスに解決できる必要があります。 

### <a name="integration-runtime-options"></a>統合ランタイムのオプション 

- データ ソースが Azure 内にある場合は、Azure Purview インジェスト プライベート エンドポイントがデプロイされている同じ仮想ネットワーク内にデプロイされている Windows 仮想マシンでセルフホステッド統合ランタイムを設定して使用する必要があります。 Azure 統合ランタイムは、インジェスト プライベート エンドポイントでは機能しません。 

- オンプレミスのデータ ソースをスキャンする場合は、オンプレミスの Windows マシンまたは Azure 仮想ネットワーク内の VM に、セルフホステッド統合ランタイムをインストールすることもできます。 

- Azure Purview でプライベート エンドポイントを使用する場合は、データ ソースから、Azure Purview プライベート エンドポイントがデプロイされている Azure 仮想ネットワーク上のセルフホステッド統合 VM へのネットワーク接続を許可する必要があります。  

- セルフホステッド統合ランタイムの自動アップグレードを許可することをお勧めします。 自動アップグレードを許可する場合は、必ず、Azure 仮想ネットワークまたは企業ファイアウォールで必要なアウトバウンド規則を公開してください。 詳細については、[セルフホステッド統合ランタイムのネットワーク要件](manage-integration-runtimes.md#networking-requirements)に関するセクションを参照してください。

### <a name="authentication-options"></a>認証オプション  

- Azure Purview マネージド ID を使用して、インジェスト プライベート エンドポイント経由でデータ ソースをスキャンすることはできません。 データ ソースの種類に基づいて、サービス プリンシパル、アカウント キー、または SQL 認証を使用します。

- 資格情報が Azure Key Vault に格納されており、Azure Purview 内に登録されていることを確認してください。

- Azure Key Vault に作成する各シークレットに基づいて、Azure Purview に資格情報を作成する必要があります。 Azure の Key Vault リソースに対して、少なくとも ''_取得_'' と ''_一覧表示_'' のアクセス権を Azure Purview のシークレットに割り当てる必要があります。 そうしないと、Azure Purview アカウントで資格情報が機能しません。 

### <a name="current-limitations"></a>現在の制限 

- インジェスト用にプライベート エンドポイントを使っている場合、インジェスト プライベート エンドポイントとセルフホステッド統合ランタイムを介した、サブスクリプションまたはリソース グループ全体を使用する複数の Azure ソースのスキャンはサポートされません。 代わりに、データ ソースを個別に登録およびスキャンできます。 

- Azure Purview プライベート エンドポイントに関連する制限については、「[既知の制限事項](catalog-private-link-troubleshoot.md#known-limitations)」を参照してください。

- Private Link サービスに関連する制限については、[Azure Private Link の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits)に関する記述を参照してください。 

### <a name="private-endpoint-scenarios"></a>プライベート エンドポイントのシナリオ 

#### <a name="single-virtual-network-single-region"></a>単一の仮想ネットワーク、単の一リージョン  

このシナリオでは、すべての Azure データ ソース、セルフホステッド統合ランタイム VM、および Azure Purview プライベート エンドポイントが、Azure サブスクリプションの同じ仮想ネットワークにデプロイされます。   

オンプレミスのデータ ソースが存在する場合、Azure Purview プライベート エンドポイントがデプロイされている Azure 仮想ネットワークへのサイト間 VPN または Azure ExpressRoute 接続によって、接続が提供されます。 

このアーキテクチャは、主に小規模な組織、または開発、テスト、概念実証のシナリオに適しています。 

:::image type="content" source="media/concept-best-practices/network-pe-single-vnet.png" alt-text="単一の仮想ネットワーク シナリオでプライベート エンドポイントを使用した Azure Purview を示すスクリーンショット。"lightbox="media/concept-best-practices/network-pe-single-vnet.png":::

#### <a name="single-region-multiple-virtual-networks"></a>単一のリージョン、複数の仮想ネットワーク 

Azure で 2 つ以上の仮想ネットワークを接続するには、[仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)を使用できます。 ピアリングされた仮想ネットワーク間のネットワーク トラフィックはプライベートであり、Azure のバックボーン ネットワーク上に保持されます。 

多くのお客様は、ハブアンドスポーク ネットワーク アーキテクチャを使用して、Azure でネットワーク インフラストラクチャを構築しています。そこでは、 

- ネットワーク共有サービス (ネットワーク仮想アプライアンス、ExpressRoute/VPN ゲートウェイ、DNS サーバーなど) がハブ仮想ネットワークにデプロイされます。 
- スポーク仮想ネットワークでは、仮想ネットワーク ピアリングを介して、これらの共有サービスが使用されます。 

ハブアンドスポーク ネットワーク アーキテクチャでは、組織のデータ ガバナンス チームに、仮想ネットワーク (ハブ) を含む Azure サブスクリプションを提供できます。 すべてのデータ サービスを、仮想ネットワーク ピアリングまたはサイト間 VPN 接続経由でハブ仮想ネットワークに接続されている他のいくつかのサブスクリプションに配置できます。 

ハブアンドスポーク アーキテクチャでは、ハブ サブスクリプションと仮想ネットワーク内に Azure Purview と 1 つまたは複数のセルフホステッド統合ランタイム VM をデプロイすることができます。 同じリージョン内の複数のサブスクリプションの他の仮想ネットワークから、データ ソースを登録およびスキャンすることができます。 

セルフホステッド統合ランタイム VM は、インジェスト プライベート エンドポイントと同じ仮想ネットワークにある必要がありますが、別のサブネットにあってもかまいません。

:::image type="content" source="media/concept-best-practices/network-pe-multi-vnet.png" alt-text="複数の仮想ネットワークのシナリオでプライベート エンドポイントを使用した Azure Purview を示すスクリーンショット。"lightbox="media/concept-best-practices/network-pe-multi-vnet.png":::

必要に応じて、スポーク仮想ネットワークに追加のセルフホステッド統合ランタイムをデプロイすることができます。 その場合は、追加のアカウントおよびインジェスト プライベート エンドポイントをスポーク仮想ネットワークにデプロイする必要があります。 

#### <a name="multiple-regions-multiple-virtual-networks"></a>複数のリージョン、複数の仮想ネットワーク

データ ソースが 1 つ以上の Azure サブスクリプションの複数の Azure リージョンに分散されている場合は、このシナリオを使用できます。

パフォーマンスとコストの最適化のために、データ ソースが配置されている各リージョンに 1 つまたは複数のセルフホステッド統合ランタイム VM をデプロイすることを強くお勧めします。 その場合は、セルフホステッド統合ランタイム VM が配置されているリージョンと仮想ネットワークに、Azure Purview アカウント用の追加のアカウントおよびインジェスト プライベート エンドポイントをデプロイする必要があります。  

他のリージョンから Azure Data Lake Storage (Gen 2) リソースを登録してスキャンする必要がある場合は、データソースが配置されているリージョンにローカルのセルフホステッド統合ランタイム VM が必要です。 

:::image type="content" source="media/concept-best-practices/network-pe-multi-region.png" alt-text="複数の仮想ネットワークと複数のリージョンのシナリオでプライベート エンドポイントを使用した Azure Purview を示すスクリーンショット。"lightbox="media/concept-best-practices/network-pe-multi-region.png":::

## <a name="option-3-use-both-private-and-public-endpoints"></a>オプション 3: プライベートとパブリックの両方のエンドポイントを使用する

データソースのサブセットでプライベート エンドポイントを使用するオプションを選択できます。また同時に、次のいずれかをスキャンする必要があります。

- [サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)で構成されているその他のデータ ソース
- インターネット経由でアクセスできるパブリック エンドポイントがあるデータ ソース

一部のデータ ソースはインジェスト プライベート エンドポイントを使用して、また一部のデータ ソースはパブリック エンドポイントまたはサービス エンドポイントを使用してスキャンする必要がある場合は、次のようにできます。

1. Azure Purview アカウントにプライベート エンドポイントを使用します。
1. Azure Purview アカウントで **パブリック ネットワーク アクセス** を **許可する** ように設定します。

### <a name="integration-runtime-options"></a>統合ランタイムのオプション 

- プライベート エンドポイントで構成されている Azure データ ソースをスキャンするには、Azure Purview インジェスト プライベート エンドポイントがデプロイされている同じ仮想ネットワーク内にデプロイされた Windows 仮想マシンで、セルフホステッド統合ランタイムを設定して使用する必要があります。 

  Azure Purview でプライベート エンドポイントを使用する場合は、データ ソースから、Azure Purview プライベート エンドポイントがデプロイされている Azure 仮想ネットワーク上のセルフホステッド統合 VM へのネットワーク接続を許可する必要があります。  

- パブリック エンドポイントを許可するように構成されている Azure データ ソースをスキャンする場合は、Azure 統合ランタイムを使用できます。 

- オンプレミスのデータ ソースをスキャンする場合は、オンプレミスの Windows マシンまたは Azure 仮想ネットワーク内の VM に、セルフホステッド統合ランタイムをインストールすることもできます。 

- セルフホステッド統合ランタイムの自動アップグレードを許可することをお勧めします。 自動アップグレードを許可する場合は、必ず、Azure 仮想ネットワークまたは企業ファイアウォールで必要なアウトバウンド規則を公開してください。 詳細については、[セルフホステッド統合ランタイムのネットワーク要件](manage-integration-runtimes.md#networking-requirements)に関するセクションを参照してください。

### <a name="authentication-options"></a>認証オプション  

- パブリック エンドポイントを許可するように構成されている Azure データ ソースをスキャンする場合は、データ ソースの種類に基づいて、任意の認証オプションを使用できます。
  
- インジェスト プライベート エンドポイントを使用して、プライベート エンドポイントで構成されている Azure データ ソースをスキャンする場合:

  - Azure Purview マネージド ID を使用することはできません。 代わりに、データ ソースの種類に基づいて、サービス プリンシパル、アカウント キー、または SQL 認証を使用します。 
  
  - 資格情報が Azure Key Vault に格納されており、Azure Purview 内に登録されていることを確認してください。

  - Azure Key Vault に作成する各シークレットに基づいて、Azure Purview に資格情報を作成する必要があります。 Azure の Key Vault リソースに対して、少なくとも ''_取得_'' と ''_一覧表示_'' のアクセス権を Azure Purview のシークレットに割り当てます。 そうしないと、Azure Purview アカウントで資格情報が機能しません。 

## <a name="next-steps"></a>次のステップ
-  [プライベート エンドポイントを使用して Azure Purview へのアクセスをセキュリティで保護する](./catalog-private-link.md)
