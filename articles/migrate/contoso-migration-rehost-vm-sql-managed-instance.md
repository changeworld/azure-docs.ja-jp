---
title: Contoso オンプレミス アプリを Azure VM および Azure SQL Managed Instance に移行してリホストする | Microsoft Docs
description: Contoso がオンプレミス アプリを Azure VM および Azure SQL Managed Instance 上でリホストする方法を説明します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 49a5fb13a881b206c36dcd08a4c2945880e9a4bd
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002298"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-azure-vms-and-azure-sql-managed-instance"></a>Contoso の移行: オンプレミス アプリを Azure VM および Azure SQL Managed Instance にリホストする

この記事では、Contoso が Azure Site Recovery サービスを使用して、SmartHotel アプリ フロントエンド VM を Azure VM に移行する方法と、アプリ データベースを Azure SQL Managed Instance に移行する方法を説明します。

> [!NOTE]
> Azure SQL Managed Instance は現在、プレビュー段階にあります。

このドキュメントは、架空の会社 Contoso がオンプレミス リソースを Microsoft Azure クラウドに移行する方法を説明するシリーズ記事の 1 つです。 このシリーズは、背景情報と一連のシナリオが含まれており、移行インフラストラクチャのセットアップ方法や、さまざまな種類の移行方法を理解できるようになっています。 シナリオが複雑になってきているため、徐々に記事が追加される予定です。


**記事** | **詳細** | **状態**
--- | --- | ---
[記事 1: 概要](contoso-migration-overview.md) | Contoso の移行戦略、記事シリーズ、および使用するサンプル アプリの概要を示します。 | 使用可能
[記事 2: Azure インフラストラクチャのデプロイ](contoso-migration-infrastructure.md) | Contoso が移行に備えてオンプレミスおよび Azure インフラストラクチャをどのように準備するかを説明します。 移行に関するすべてのアーティクルでは同じインフラストラクチャが使用されます。 | 使用可能
[記事 3: Azure への移行の対象となるオンプレミスのリソースの評価](contoso-migration-assessment.md)  | VMware で実行されているオンプレミスの 2 階層 SmartHotel アプリの評価を Contoso が実行する方法を説明します。 Contoso は、アプリの VM は [Azure Migrate](migrate-overview.md) サービスを使用して、アプリの SQL Server データベースは [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) を使用して評価します。 | 使用可能
記事 4: Azure VM および SQL Managed Instance でのアプリのリホスト | Contoso が、オンプレミスの SmartHotel アプリの Azure へのリフトアンドシフト移行を実行する方法を説明します。 Contoso は、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用してアプリのフロントエンド VM を移行し、[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用してアプリのデータベースを SQL Managed Instance に移行します。 | この記事の内容は次のとおりです。
[記事 5: Azure VM へのアプリの再ホスト](contoso-migration-rehost-vm.md) | Contoso が Site Recovery サービスを使用して SmartHotel アプリの VM を Azure VM に移行する方法を示します。 | 使用可能
[記事 6: Azure VM および SQL Server Always On 可用性グループへのアプリのリホスト](contoso-migration-rehost-vm-sql-ag.md) | Contoso が SmartHotel アプリを移行する方法を示します。 Contoso は、Site Recovery を使用してアプリの VM を移行し、Database Migration Service を使用してアプリのデータベースを AlwaysOn 可用性グループで保護されている SQL Server クラスターに移行します。 | 使用可能
[記事 7: Linux アプリの Azure VM への再ホスト](contoso-migration-rehost-linux-vm.md) | Contoso が Site Recovery を使用して Azure VM への Linux osTicket アプリのリフトアンドシフト移行を実行する方法を説明します。 | 使用可能
[記事 8: Azure VM と Azure MySQL への Linux アプリのリホスト](contoso-migration-rehost-linux-vm-mysql.md) | Contoso が Site Recovery を使用して Linux osTicket アプリを Azure VM に移行する方法、および MySQL Workbench を使用してアプリのデータベースを Azure MySQL Server インスタンスに移行する方法を説明します。 | 使用可能
[記事 9: Azure Web Apps と Azure SQL Database でのアプリのリファクター](contoso-migration-refactor-web-app-sql.md) | Contoso が SmartHotel アプリを Azure Web App に移行して、アプリ データベースを Azure SQL Server インスタンスに移行する方法を示します。 | 使用可能
[記事 10: Azure Web Apps と Azure MySQL での Linux アプリのリファクター](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso が Linux osTicket アプリを複数のサイトの (継続的デリバリーのために GitHub と統合された) Azure Web Apps に移行する方法を示します。 Contoso は、アプリ データベースを Azure MySQL インスタンスに移行します。 | 使用可能
[記事 11: VSTS での TFS のリファクター](contoso-migration-tfs-vsts.md) | Contoso がオンプレミスの Team Foundation Server (TFS) の展開を Azure の Visual Studio Team Services (VSTS) に移行する方法を示します。 | 使用可能
[記事 12: Azure コンテナーと Azure SQL Database でのアプリの再構築](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行して再構築する方法を示します。 Contoso は、アプリの Web 階層を Windows コンテナーとして再構築し、Azure SQL Database 内のアプリ データベースを再構築します。 | 使用可能
[記事 13: Azure でのアプリのリビルド](contoso-migration-rebuild.md) | Contoso が Azure のさまざまな機能とサービス (App Services、Azure Kubernetes、Azure Functions、Cognitive サービス、Cosmos DB など) を使用して SmartHotel アプリをリビルドする方法を示します。 | 使用可能


この記事で使用されているサンプルの SmartHotel アプリをご利用になりたい場合は、[github](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。

## <a name="on-premises-architecture"></a>オンプレミス アーキテクチャ

以下の図は、現在の Contoso オンプレミス アーキテクチャを示しています。

![Contoso アーキテクチャ](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Contoso は、米国東部のニューヨーク市に 1 つのメイン データセンターを配置しています。
- Contoso は、米国内に 3 つの支店を配置しています。
- メイン データセンターは、光ファイバーによるイーサネット接続 (500 mbps) を通じて、インターネットに接続されています。
- 各支店は、ビジネス クラスの接続を使用して、インターネットにローカルで接続されています。メインデータセンターには、IPSec VPN トンネルで接続されています。 これにより、ネットワーク全体を永続的に接続でき、インターネット接続が最適化されます。
- メイン データセンターは、VMware によって完全に仮想化されています。 ESXi 6.5 仮想化ホストが 2 つあり、vCenter Server 6.5 で管理されています。
- Contoso は、ID 管理に Active Directory を使用しており、内部ネットワーク上で DNS サーバーを使用しています。
- データセンター内のドメイン コントローラーは、VMware VM 上で実行されています。 支店にあるドメイン コントローラーは、物理サーバー上で実行されています。



## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス部門のパートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応**: Contoso は成長を続けています。そのため、オンプレミス システムおよびインフラストラクチャに対する負荷が高まっています。
- **効率化**: Contoso は不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。  ビジネス部門は IT に対して、時間やコストを無駄にせず、迅速に作業を行ってもらう必要があります。これは、例えば、顧客の要求に素早く対応するためです。
- **敏捷性の強化**: Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 また、グローバル経済で成功を収めるために、市場の変化に対して、より迅速な対応ができる必要があります。  ビジネスの妨げになったり、ビジネスの機会を壊すようなことがあってはなりません。
- **拡張性**: ビジネスが順調に成長していく中で、Contoso IT は、同じペースで拡張可能なシステムを提供する必要があります。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を設定しました。 これらの目標を使用して、最良の移行方法を決定します。

- 移行しても、Azure のアプリは、現在のオンプレミス VMWare 環境と同じパフォーマンスを発揮できる必要があります。  クラウドに移行したからといって、アプリのパフォーマンスの重要性が低下するわけではありません。
- Contoso は、このアプリに投資したくないと考えています。  このアプリはビジネスにとって重要なものですが、現状のままクラウドに移行したいと思っています。
- アプリを移行した後、データベース管理タスクを最小限に抑える必要があります。
- Contoso は、このアプリに Azure SQL Database を使用したくないと考えており、別のデータベースを探しています。

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

このシナリオでは:

- Contoso は、2 階層のオンプレミス旅行アプリを移行したいと考えています。
- このアプリは、2 つの VM (WEBVM と SQLVM) に階層化されており、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上に配置されています。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されます。
- Azure SQL Managed Instance にアプリのデータベース (SmartHotelDB) を移行します。
- Azure VM にオンプレミス VMware VM を移行します。
- Contoso にはオンプレミスのデータセンター (contoso-datacenter) があり、そこにオンプレミスのドメイン コントローラー (**contosodc1**) が含まれています。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

![シナリオのアーキテクチャ](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | DMS を使うと、複数のデータベース ソースから Azure データ プラットフォームに、最小限のダウンタイムでシームレスに移行できます。 | DMS の[サポートされるリージョンに関する記事](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability)と、[価格の詳細に関する記事](https://azure.microsoft.com/pricing/details/database-migration/)を参照しくてださい。

  [Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Managed Instance は、Azure クラウド内の完全に管理された SQL Server インスタンスを表すマネージド データベース サービスです。 最新バージョンの SQL Server データベース エンジンと同じコードを共有し、最新の機能、パフォーマンスの向上、およびセキュリティ更新プログラムが適用されています。 | Azure SQL Database Managed Instance を使うと、容量に基づく料金がかかります。 [詳細情報](https://azure.microsoft.com/pricing/details/sql-database/managed/)。 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | このサービスは、Azure VM、オンプレミス VM、物理サーバーの移行とディザスター リカバリーを調整および管理します。  | Azure へのレプリケーションの間に、Azure Storage の料金が発生します。  フェールオーバーが発生すると、Azure VM が作成されて料金がかかります。 料金と価格について[詳しくはこちら](https://azure.microsoft.com/pricing/details/site-recovery/)をご覧ください。

 

## <a name="migration-process"></a>移行プロセス

Contoso は、SmartHotel アプリの Web とデータ層の両方を Azure に移行します。

1. Contoso は既に Azure インフラストラクチャを稼働しています。そのため、このシナリオでは、いくつかの特定の Azure コンポーネントを追加するだけでかまいません。
2. データ層を移行するときは、Data Migration Service (DMS) を使用します。  DMS は、Contoso データセンターと Azure 間でサイト間 VPN 接続を通じてオンプレミス SQL Server VM に接続し、データベースを移行します。
3. Web 層を移行するときは、 Azure Site Recovery によるリフトアンドシフト移行を使用します。 このためには、オンプレミス VMware 環境の準備を整え、レプリケーションを設定して有効にし、VM を Azure にフェールオーバーして移行することが必要です。

     ![移行のアーキテクチャ](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 


## <a name="prerequisites"></a>前提条件

このシナリオで Contoso (およびお客様) に必要なことを以下に示します。

**要件** | **詳細**
--- | ---
**プレビューへの登録** | SQL マネージド インスタンスの限定パブリック プレビューに登録する必要があります。 [サインアップ](https://portal.azure.com#create/Microsoft.SQLManagedInstance)するには、Azure サブスクリプションが必要です。 サインアップが完了するには数日かかることがあるので、このシナリオの展開を始める前に行っておいてください。
**Azure サブスクリプション** | このシリーズの最初の記事で評価を行ったときに、サブスクリプションは既に作成しているはずです。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成してください。<br/><br/> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。<br/><br/> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。<br/><br/> さらに詳細なアクセス許可が必要な場合は、[こちらの記事](../site-recovery/site-recovery-role-based-linked-access-control.md)をご覧ください。 
**Site Recovery (オンプレミス)** | オンプレミスの vCenter Server は、バージョン 5.5、6.0、または 6.5 を実行している必要があります。<br/><br/> バージョン 5.5、6.0、または 6.5 を実行している ESXi ホスト<br/><br/> ESXi ホスト上で実行している 1 つ以上の VMware VM。<br/><br/> VM は [Azure の要件](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)を満たす必要があります。<br/><br/> サポートされている[ネットワーク](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)および[ストレージ](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)の構成。
**DMS** | DMS では、[互換性のあるオンプレミスの VPN デバイス](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)が必要です。<br/><br/> オンプレミスの VPN デバイスを構成できる必要があります。 外部接続用のパブリック IPv4 アドレスを持っている必要があり、アドレスが NAT デバイスの内側に存在することはできません。<br/><br/> オンプレミスの SQL Server データベースにアクセスできることを確認します。<br/><br/> Windows ファイアウォールは、移行元のデータベース エンジンにアクセスできる必要があります。 [詳細情報](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。<br/><br/> データベース マシンの前面にファイアウォールがある場合は、SMB ポート 445 経由でのデータベースおよびファイルへのアクセスを許可する規則を追加します。<br/><br/> 移行元の SQL Server および移行先のマネージド インスタンスへの接続に使用する資格情報は、sysadmin サーバー ロールのメンバーである必要があります。<br/><br/> DMS が移行元データベースのバックアップに使用できるネットワーク共有がオンプレミスのデータベースに存在する必要があります。<br/><br/> 移行元の SQL Server インスタンスを実行しているサービス アカウントに、ネットワーク共有に対する書き込み権限があることを確認します。<br/><br/> ネットワーク共有に対するフル コントロール権限を持つ Windows ユーザー (とパスワード) をメモしておきます。 Azure Database Migration Service は、これらのユーザーの資格情報を偽装して、Azure ストレージ コンテナーにバックアップ ファイルをアップロードします。<br/><br/> SQL Server Express のインストール プロセスでは、TCP/IP プロトコルが既定で**無効化**されます。 これが有効になっていることを確認します。


## <a name="scenario-steps"></a>シナリオのステップ

Contoso がデプロイ環境を設定する方法を以下に示します。

> [!div class="checklist"]
> * **ステップ 1: SQL Azure Managed Instance を設定する**: オンプレミス SQL Server データベースの移行先になるマネージド インスタンスを事前に作成しておく必要があります。
> * **ステップ 2: DMS を準備する**: Database Migration プロバイダーを登録し、インスタンスを作成してから、DMS プロジェクトを作成します。 DMS の SA URI を設定する必要もあります。 ストレージ オブジェクトに制限されたアクセス許可を付与できるように、Shared Access Signature (SAS) の Uniform Resource Identifier (URI) によってストレージ アカウント内のリソースへの委任アクセスが提供されます。 サービスが SQL Server のバックアップ ファイルをアップロードするストレージ アカウント コンテナーに DMS がアクセスできるように、SAS URI を設定します。
> * **ステップ 3: Site Recovery 用に Azure を準備する**: Site Recovery では、レプリケートされたデータを保持するための Azure ストレージ アカウントと、Microsoft Azure Recovery Services のコンテナーを作成する必要があります。
> * **ステップ 4: Site Recovery 用にオンプレミス VMware を準備する**: Contoso は、VM を検出し、エージェントをインストールするためのアカウントを準備します。また、フェールオーバー後に Azure VM に接続するための準備を行う必要もあります。
> * **ステップ 5: VM を複製する**: レプリケーションを設定するために、Site Recovery の移行元環境と移行先環境を構成し、レプリケーション ポリシーを設定して、Azure ストレージへの VM のレプリケーションを開始します。
> * **ステップ 6: DMS を使用してデータベースを移行する**: データベースを移行できるようになりました。
> * **ステップ 7: Site Recovery を使用して VM を移行する**: テスト フェールオーバーを実行し、すべてが機能することを確認した後、完全なフェールオーバーを実行し、VM を Azure に移行します。


## <a name="step-1-prepare-an-azure-sql-managed-instance"></a>ステップ 1: Azure SQL Managed Instance を準備する

Azure SQL Managed Instance を設定する際、Contoso はサブネットが必要です。

- サブネットは専用でなければなりません。 また、サブネットは空で他のクラウド サービスを含んでいない必要があり、ゲートウェイ サブネットであってはなりません。
- マネージド インスタンスを作成した後、そのインスタンスにリソースを追加してはなりません。
- サブネットに NSG が関連付けられていてはなりません。
- サブネットには、割り当てられている唯一のルートとして次ホップ インターネットが 0.0.0.0/0 のユーザー ルート テーブル (UDR) が必要です。 
- 省略可能なカスタム DNS: カスタム DNS が VNet で指定されている場合、Azure の再帰的なリゾルバーの IP アドレス (168.63.129.16 など) をリストに追加する必要があります。 [詳細情報](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)。
- サブネットにサービス エンドポイント (ストレージまたは SQL) を関連付けることはできません。 仮想ネットワークではサービス エンドポイントを無効にする必要があります。
- サブネットには 16 個以上の IP アドレスが必要です。 マネージド インスタンス サブネットのサイズ指定についての[詳細](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances)。
- ハイブリッド環境では、カスタム DNS 設定が必要です。 Contoso は、1 つ以上の Azure DNS サーバーを使用するように DNS 設定を構成します。 DNS のカスタマイズについて詳しくは、[こちら](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)をご覧ください。

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>マネージド インスタンス用の仮想ネットワークを設定する

Contoso は、VNet を以下のように設定します。 

1. Contoso は、ContosoNetworkingRG リソース グループ内にあるプライマリ米国東部 2 リージョン内に新しい VNet (VNET-SQLMI-EU2) を作成します。
2. Contoso は、アドレス空間 10.235.0.0/24 を割り当てて、範囲が Contoso の他のどのネットワークとも重ならないことを確認します。
2. ネットワークに以下の 2 つのサブネットを追加します。
    - SQLMI-DS-EUS2 (10.235.0.0.25)
    - SQLMI-SAW-EUS2 (10.235.0.128/29)。 このサブネットは、マネージド インスタンス (SQLMI) にディレクトリを接続するために使用されます。

    ![Managed Instance のネットワーク](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. VNet とサブネットがデプロイされると、Contoso は、ネットワークを以下のようにピアリングします。

    - VNET-SQLMI-EUS2 と VNET-HUB-EUS2 (米国東部 2 のハブ VNet) をピアリングします。
    - VNET-SQLMI-EUS2 と VNET-PROD-EUS2 (実稼働ネットワーク) をピアリングします。

    ![ネットワーク ピアリング](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso は、カスタム DNS 設定を行います。 DNS は最初に Azure DC を示します。 Azure DNS はセカンダリになります。 Contoso Azure DC は、以下のように配置されます。

    - 米国東部 2 の実稼働ネットワーク (VNET-PROD-EUS2) 内の PROD-DC-EUS2 サブネットに配置されます。
    - CONTOSODC3 のアドレス: 10.245.42.4
    - CONTOSODC4 のアドレス: 10.245.42.5
    - Azure DNS リゾルバー: 168.63.129.16

     ![ネットワーク DNS](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

**さらにサポートが必要な場合**

- Azure SQL Managed Instance の概要については、[こちら](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)をご覧ください。
- SQL Managed Instance 用の VNet の作成方法については、[こちら](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances)をご覧ください。
- ピアリングの設定については、[こちら](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)をご覧ください。
- Azure AD DNS 設定の更新については、[こちら](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns)をご覧ください。



### <a name="set-up-routing"></a>ルーティングを設定する

Managed Instance は、プライベート VNET に配置されます。そのため、Managed Instance が Azure Management Service と通信するには、ルート テーブルが必要です。 Managed Instance が、それを管理する Azure Management Service と通信できない場合は、Managed Instance へのアクセスができなくなります。

- ルート テーブルには、Managed Instance から送信されたパケットを VNet 内でルーティングする方法を指定した一連ルール (ルート) が含まれています。
- ルート テーブルは、Managed Instance がデプロイされているサブネットに関連付けられています。 サブネットから離れる各パケットは、関連付するルート テーブルに基づいて処理されます。
- 1 つのサブネットは、1 つのルート テーブルにのみ関連付けることができます。
- Microsoft Azure では、ルート テーブルの作成に追加料金はかかりません。

1. Contoso は、ユーザー定義のルート テーブルを作成します。 ルート テーブルは、ContosoNetworkingRG リソース グループ内に作成されます。

    ![ルート テーブル](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Managed Instance の要件に準拠するため、Contoso は、ルート テーブル (MIRouteTable) をデプロイした後、アドレス プレフィックスが 0.0.0.0/0、**[次ホップの種類]** が **[インターネット]** に設定されたルートを追加します。

    ![ルート テーブルのプレフィックス](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso は、(VNET-SQLMI-EUS2 ネットワーク内の) SQLMI-DB-EUS2 サブネットにルート テーブルを関連付けます。 

    ![ルート テーブルのサブネット](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
**さらにサポートが必要な場合**

マネージド インスタンスのルートの設定方法については、[こちら](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route)をご覧ください。

### <a name="create-a-managed-instance"></a>マネージド インスタンスを作成する

以上で Contoso は、SQL Database Managed Instance をプロビジョニングできるようになりました。

1. Managed Instance は、ビジネス アプリにサービスを提供します。そのため、Contoso は、ContosoRG リソース グループ内にあるプライマリ米国東部 2 リージョン内に Managed Instance をデプロイします。 
2. 価格レベルを選択し、インスタンスの計算およびストレージのサイズを設定します。 価格に関して詳しくは、[こちら](https://azure.microsoft.com/pricing/details/sql-database/managed/)をご覧ください。

    ![マネージド インスタンス](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Managed Instance をデプロイすると、ContosoRG リソース グループ内に 2 つの新しいリソースが表示されます。

    - 仮想クラスター (複数のマネージド インスタンスが存在する場合)
    - SQL Server Managed Instance 

    ![マネージド インスタンス](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**さらにサポートが必要な場合**

Managed Instance のプロビジョニングについては、[こちら](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal)をご覧ください。

## <a name="step-2-prepare-dms"></a>ステップ 2: DMS を準備する

DMS を準備するために、Contoso は、以下のことを行う必要があります。

- Azure で DMS プロバイダーを登録します。
- DMS に Azure ストレージへのアクセス権を付与し、データベースの移行に使用するバックアップ ファイルをアップロードできるようにします。 これを行うために、BLOB ストレージ コンテナーを作成し、その Shared Access Signature (SAS) URI を生成します。 
- DMS プロジェクトを作成します。


以下のステップを実行します。

1. Contoso は、そのサブスクリプションでデータベース移行プロバイダーを登録します。
    ![DMS の登録](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Contoso は、BLOB ストレージ コンテナーを作成し、その SAS URI を生成して、DMS がアクセスできるようにします。

    ![SAS URI](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. 最後に Contoso は、DMS インスタンスを作成します。 

    ![DMS インスタンス](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Contoso は、VNET-PROD-DC-EUS2 VNet の PROD-DC-EUS2 サブネット内に DMS インスタンスを配置します。
    - これは、DMS インスタンスを含む VNet は、VPN ゲートウェイ経由でオンプレミス SQL Server VM にアクセスできる必要があるためです。
    - VNET-PROD-EUS2 は、VNET-HUB-EUS2 に対してピアリングされ、リモート ゲートウェイの使用が許可されます。  これにより、DMS は、必要に応じて通信を行えるようになります。

        ![DMS ネットワーク](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

**さらにサポートが必要な場合**
- DMS の設定については、[こちら](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal)をご覧ください。
- SAS の作成および使用については、[こちら](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)をご覧ください。


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>ステップ 3: Site Recovery サービス用に Azure を準備する

Web 層 VM (WEBMV) の移行のために Site Recovery を設定するには、多数の Azure 要素が必要です。

- フェールオーバーされるリソースが配置された VNet。
- レプリケートされたデータを保持する Azure ストレージ アカウント。 
- Azure の Recovery Services のコンテナー。

Site Recovery は、以下のように設定します。

1. VM は、SmartHotel アプリに対する Web フロントエンドです。そのため、VM は、米国東部 2 リージョン内の既存の実稼働ネットワーク (VNET-PROD-EUS2) とサブネット (PROD-FE-EUS2) にフェールオーバーされます。 このネットワークは、[Azure インフラストラクチャのデプロイ](contoso-migration-infrastructure.md)時に設定します。
2. Azure ストレージ アカウント (contosovmsacc20180528) を作成します。 Contoso は、標準のストレージと LRS レプリケーションが設定された汎用のアカウントを使用しています。

    ![Site Recovery ストレージ](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. ネットワークおよびストレージ アカウントを設定したので、Contoso は、コンテナー (ContosoMigrationVault) を作成し、それをプライマリ米国東部 2 リージョン内の ContosoFailoverRG リソース グループ内に配置できるようになりました。

    ![Recovery Services コンテナー](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

**さらにサポートが必要な場合**

Site Recovery のために Azure を設定することについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure)。


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>ステップ 4: Site Recovery 用にオンプレミス VMware を準備する

Site Recovery 用に VMware を準備する場合、Contoso が行うべきことを以下に示します。

- VM の検出を自動化するために、vCenter Server または vSphere ESXi ホストのアカウントを準備します。
- レプリケートする VMware VM にモビリティ サービスを自動インストールできるアカウントを準備します。
- オンプレミス VM を準備し、この VM が、フェールオーバー後に作成されたときに、Azure VM に接続できるようにします。


### <a name="prepare-an-account-for-automatic-discovery"></a>自動検出用のアカウントを準備する

Site Recovery では、次のことを実行するために、VMware サーバーへのアクセスが必要です。

- VM を自動的に検出します。 少なくとも読み取り専用のアカウントが必要です。
- レプリケーション、フェールオーバー、およびフェールバックを調整します。 ディスクを作成および削除する、VM の電源をオンにするなどの操作を実行できるアカウントが必要です。

Contoso は、アカウントを以下のように設定します。

1. vCenter レベルでロールを作成します。
2. 必要なアクセス許可をそのロールに割り当てます。

**さらにサポートが必要な場合**

自動検出のためにロールを作成して割り当てることについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery)。

### <a name="prepare-an-account-for-mobility-service-installation"></a>モビリティ サービスのインストール用のアカウントを準備する

モビリティ サービスは、レプリケートする VM にインストールする必要があります。

- VM のレプリケーションが有効になっていると、Site Recovery はこのコンポーネントの自動プッシュ インストールを行うことができます。
- 自動的にプッシュ インストールする場合は、Site Recovery で VM へのアクセスに使用するアカウントを準備する必要があります。
- Azure コンソールでレプリケーションを設定するときに、このアカウントを指定します。
- VM にインストールするアクセス許可を持つドメイン アカウントまたはローカル アカウントが必要です。

**さらにサポートが必要な場合**

モビリティ サービスのプッシュ インストールのためにアカウントを作成することについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation)。


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための準備をする

Contoso は、Azure へのフェールオーバー後に、Azure 内のレプリケートされた VM に接続できることを望んでいます。 これを行うには、移行を実行する前に、オンプレミス VM 上でいくつかのステップを処理する必要があります。 

1. インターネット経由でアクセスするため、フェールオーバー前にオンプレミス VM 上で RDP を有効にします。また、TCP および UDP ルールが**パブリック** プロファイルに追加されていること、および **[Windows ファイアウォール]** > **[許可されたアプリ]** を選択し、すべてのプロファイルで RDP が許可されていることを確認します。
2. サイト間 VPN 経由でアクセスするため、オンプレミス マシン上で RDP を有効にします。また、**[Windows ファイアウォール]** -> **[Allowed apps and features]\(許可されたアプリおよび機能\)** (**[Domain and Private]\(ドメインおよびプライベート\)** ネットワーク) を選択して、RDP を許可します。
3. オンプレミス VM 上のオペレーティング システムの SAN ポリシーを **OnlineAll** に設定します。

また、フェールオーバーを実行するときは、以下を確認する必要があります。

- フェールオーバーをトリガーするときに、VM 上に保留中の Windows 更新プログラムがないようにします。 ある場合は、更新が完了するまで、仮想マシンにログインすることはできません。
- フェールオーバー後、**[ブート診断]** をオンにして、VM のスクリーンショットを確認します。 スクリーンショットを表示できない場合は、VM が実行中であることを確認したうえで、[トラブルシューティングのヒント](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を確認してください。


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>手順 5: Site Recovery を使用して、オンプレミス VM を Azure にレプリケートする

Azure への移行を実行する前に、Contoso は、レプリケーションを設定し、オンプレミス VM のレプリケーションを有効にする必要があります。

### <a name="set-a-replication-goal"></a>レプリケーションの目標を設定する

1. コンテナーのコンテナー名 (ContosoVMVault) の下で、レプリケーションの目標を設定します (**[作業の開始]** > **[Site Recovery]** > **[インフラストラクチャの準備]**)。
2. マシンがオンプレミスに配置されていること、それらのマシンは VMware VM であること、およびレプリケート先は Azure にすることを指定します。

    ![レプリケーションの目標](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>展開の計画を確認する

続行するには、デプロイ計画が完了したことを確認するために、**[はい、完了しました]** を選択する必要があります。 このデプロイでは、Contoso が移行する VM は 1 つのみです。そのため、デプロイ計画は不要です。

### <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

ここで、ソース環境を構成する必要があります。 これを行うには、OVF テンプレートをダウンロードし、そのテンプレートを使用して、構成サーバーとそれに関連するコンポーネントを高可用性オンプレミス VMware VM としてデプロイします。 サーバーのコンポーネントは次のとおりです。

- 構成サーバー: オンプレミスと Azure の間の通信を調整し、データのレプリケーションを管理します。
- プロセス サーバー: レプリケーション ゲートウェイとして機能します。 レプリケーション データを受信し、そのデータをキャッシュ、圧縮、暗号化によって最適化して、Azure Storage に送信します。
- また、プロセス サーバーは、レプリケートする VM へのモビリティ サービスのインストールや、オンプレミスの VMware VM の自動検出も行います。
- 構成サーバー VM を作成して起動した後、その VM をコンテナーに登録します。


Contoso は、これらのステップを以下のように実行します。

1. OVF テンプレートを Azure portal からダウンロードします (**[インフラストラクチャの準備]** > **[ソース]** > **[構成サーバー]**)。
    
    ![OVF をダウンロードする](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. VMware にテンプレートをインポートし、VM を作成してデプロイします。

    ![OVF テンプレート](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  初めて VM をオンにすると、Windows Server 2016 インストール環境が起動します。 使用許諾契約書に同意し、管理者パスワードを入力します。
4. インストールの完了後に、管理者として VM にサインインします。 初めてサインインしたときは、Azure Site Recovery 構成ツールが既定で実行されます。
5. ツールでは、構成サーバーをコンテナーに登録するときに使用する名前を指定します。
6. このツールは、VM が Azure に接続できることを確認します。 接続が確立された後、**[サインイン]** を選択して、Azure サブスクリプションにサインインします。 この資格情報は、構成サーバーを登録するコンテナーにアクセスできる必要があります。 

    [構成サーバーの登録](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. ツールがいくつかの構成タスクを実行した後、再起動されます。 マシンに再度サインインすると、構成サーバーの管理ウィザードが自動的に起動されます。
8. ウィザードで、レプリケーション トラフィックを受信する NIC を選択します。 この設定は、構成後に変更することはできません。
9. サブスクリプション、リソース グループ、および構成サーバーを登録するコンテナーを選択します。
        ![コンテナー](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png) 

10. MySQL Server と VMWare PowerCLI をダウンロードしてインストールします。 次に、サーバーの設定を検証します。
11. 検証が終わったら、vCenter Server または vSphere ホストの FQDN または IP アドレスを指定します。 ポートは既定のままにし、Azure 内の vCenter Server に対して、わかりやすい名前を付けます。
12. レプリケーション可能な VMware VM を Site Recovery が自動的に検出できるようにするために、以前作成したアカウントを指定する必要があります。 
13. レプリケーションが有効なときにモビリティ サービスを自動的にインストールするための資格情報を指定します。 Windows マシンの場合、このアカウントには、VM に対するローカル管理者特権が必要です。 

    ![vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. 登録が完了したら、Azure Portal で、構成サーバーと VMware サーバーがコンテナーの **[ソース]** ページの一覧に表示されていることを再確認します。 検出には 15 分以上かかる場合があります。 
8. Site Recovery は指定された設定を使用して VMware サーバーに接続し、VM を検出します。

### <a name="set-up-the-target"></a>ターゲットをセットアップする

ここで Contoso は、ターゲット レプリケーション環境を構成する必要があります。

1. **[インフラストラクチャの準備]** > **[ターゲット]** で、ターゲットの設定を選択します。
2. Site Recovery は、指定されたターゲットに Azure のストレージ アカウントとネットワークが存在することを確認します。

### <a name="create-a-replication-policy"></a>レプリケーション ポリシーを作成する

ソースとターゲットの設定が完了すると、Contoso は、レプリケーション ポリシーを作成して、構成サーバーに関連付けることができるようになります。

1. **[インフラストラクチャの準備]** > **[レプリケーションの設定]** > **[レプリケーション ポリシー]** >  **[Create and Associate]\(作成および関連付け\)** で、ポリシー **ContosoMigrationPolicy** を作成します。
2. 以下の既定の設定を使用します。
    - **[RPO しきい値]**: 既定値は 60 分です。 この値で、復旧ポイントの作成頻度を指定します。 継続的なレプリケーションがこの制限を超えると、アラートが生成されます。
    - **[復旧ポイントのリテンション期間]**。 既定値は 24 時間です。 この値は、各復旧ポイントのリテンション期間の長さを指定します。 レプリケートされた VM は、期間内の任意の時点に復旧できます。
    - **[アプリ整合性スナップショットの頻度]**。 既定値は 1 時間です。 この値は、アプリケーション整合性スナップショットが作成される頻度を指定します。
 
        ![レプリケーション ポリシーの作成](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

5. このポリシーは自動的に構成サーバーに関連付けられます。 

    ![レプリケーション ポリシーを関連付ける](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)


**さらにサポートが必要な場合**

- これらすべてのステップの詳細な手順については、[オンプレミス VMware VM のディザスター リカバリーの設定に関する記事](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)を参照してください。
- [ソース環境の設定](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[構成サーバーのデプロイ](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)、および[レプリケーション設定の構成](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)に役立つ詳細な手順が記載されています。

### <a name="enable-replication"></a>レプリケーションを有効にする

Contoso は、WebVM のレプリケートを開始できるようになりました。

1. **[アプリケーションのレプリケート]** > **[ソース]** > **[+Replicate]\(+ レプリケート\)** で、ソースの設定を選択します。
2. 仮想マシンを有効にすることを指定して、vCenter Server と構成サーバーを選択します。

 ![レプリケーションを有効にする](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. ターゲットの設定を指定します。これには、フェールオーバー後に Azure VM が配置されるリソース グループおよびネットワークを指定することも含まれます。

     ![レプリケーションを有効にする](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. レプリケーションの対象として WebVM を選択します。 VM のレプリケーションを有効にすると、Site Recovery は各 VM にモビリティ サービスをインストールします。 

    ![レプリケーションを有効にする](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. 目的のレプリケーション ポリシーが選択されていることを確認し、WEBVM のレプリケーションを有効にします。 レプリケーションの進行状況は、**[ジョブ]** で追跡します。 **保護の最終処理**ジョブが実行されると、マシンはフェールオーバーできる状態になります。
6. Azure Portal の **[要点]** で、Azure にレプリケートする VM の構造を確認できます。

    ![インフラストラクチャ ビュー](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)


**さらにサポートが必要な場合**

これらのステップの詳細な手順については、[レプリケーションの有効化に関する記事](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication)をご覧ください。

## <a name="step-6-migrate-the-database-with-dms"></a>ステップ 6: DMS を使用してデータベースを移行する

Contoso は、DMS プロジェクトを作成して、データベースを移行する必要があります。

### <a name="create-a-dms-project"></a>DMS プロジェクトを作成する
1. DMS プロジェクトを作成します。 ソース サーバーのタイプとして SQL Server、ターゲットとして Azure SQL Database Managed Instance を指定します。

     ![DMS プロジェクト](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. プロジェクトを作成した後、移行ウィザードが開きます。

### <a name="migrate-the-database"></a>データベースを移行する 

1. 移行ウィザードで、オンプレミス データベースが配置されるソース VM と、そのデータベースにアクセスするための資格情報を指定します。

    ![DMS ソース](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. 移行するデータベース (SmartHotel.Registration) を選択します。

    ![DMS ソース データベース](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. ターゲットとして、Azure の Managed Instance の名前とアクセス資格情報を指定します。

    ![DMS ターゲット](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. **[+New Activity]\(+ 新規アクティビティ\)** > **[移行の実行]** で、移行を実行する際の設定を指定します。
    - ソースおよびターゲットの資格情報。
    - 移行するデータベース。
    - オンプレミス VM 上で作成したネットワーク共有。 DMS は、この共有に移行元のバックアップを取得します。
        - 移行元の SQL Server インスタンスを実行しているサービス アカウントには、この共有に対する書き込み権限が必要です。
        - 共有に対する FQDN パスを指定します。
    - ストレージ アカウント コンテナーへのアクセス権を DMS に付与する SAS URI。このアクセス権により、DMS は、アップロードされた移行用バックアップ ファイルにアクセスできるようになります。

        ![DMS 設定](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. 移行を保存して、実行します。
6. **[Overview]\(概要\)** で、移行のステータスを監視します。

    ![DMS モニター](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. 移行が完了した後、マネージド インスタンス上に移行先のデータベースが存在することを確認します。

    ![DMS モニター](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-with-site-recovery"></a>ステップ 7: Site Recovery で VM を移行する

Contoso は、クイック テスト フェールオーバーを実行してから VM を移行します。

### <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

WEBVM を移行する前にテスト フェールオーバーを実行すると、すべてが正常に機能しているかどうかを確認できます。 

1. テスト フェールオーバーを実行し、選択可能な最新の時点 (**最後に処理された時点**) を復帰させます。
2. **[Shut down machine before beginning failover]\(フェールオーバー前にマシンをシャットダウンする\)** を選択します。これにより、Site Recovery は、フェールオーバーをトリガーする前にソース VM のシャットダウンを試みます。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 
3. テスト フェールオーバーでは、以下が実行されます。 

    - 移行を実行するために必要なすべての条件が満たされていることを確認する前提条件チェックが実行されます。
    - Azure VM を作成できるように、フェールオーバーによってデータが処理されます。 最新の復旧ポイントを選択した場合は、データから復旧ポイントが作成されます。
    - 前の手順で処理されたデータを使用して、Azure VM が作成されます。
3. フェールオーバーが完了すると、Azure Portal にはレプリカの Azure VM が表示されます。 Contoso は、すべてが正常に機能していることを確認します。 つまり、VM が適切なサイズであること、VM が適切なネットワークに接続されていること、および VM が実行されていることを確認します。 
4. テスト フェールオーバーの検査が完了したら、フェールオーバーをクリーンアップし、観察されたことをすべて記録して保存します。 

### <a name="migrate-the-vm"></a>VM の移行

1. テスト フェールオーバーが想定どおりに機能することを確認したら、移行するための復旧計画を作成します。 WEBVM を計画に追加します。

     ![復旧計画](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. 計画に基づいて、フェールオーバーを実行します。 最新の復旧ポイントを選択し、Site Recovery では、フェールオーバーをトリガーする前にオンプレミス VM のシャットダウンを試みる必要があることを指定します。

    ![フェールオーバー](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. フェールオーバーの後、Azure VM が Azure Portal に正常に表示されることを確認します。

   ![復旧計画](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Azure の VM を検査したら、移行を完了して移行プロセスを終了し、VM のレプリケーションを停止して、VM の Site Recovery の課金を停止します。

    ![フェールオーバー](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>接続文字列を更新する

移行プロセスの最終ステップとして、アプリケーションの接続文字列を更新して、SQL MI で実行されている移行されたデータベースを指すようにします。

1. Azure portal で **[設定]** > **[接続文字列]** をクリックして、接続文字列を探します。

    ![フェールオーバー](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. SQL Managed Instance のユーザー名とパスワードで文字列を更新します。
3. 文字列を構成した後は、アプリケーションの web.config ファイルに含まれる現在の接続文字列を置き換えます。
4. ファイルを更新して保存した後、WEBVM 上で IIS を再起動します。 これを行うには、コマンド プロンプトから **IISRESET /RESTART** を実行します。
5. IIS が再起動すると、アプリは SQL Managed Instance 上で実行されているデータベースを使用するようになります。
6. この時点で、オンプレミスの SQLVM マシンをシャットダウンすれば、移行は完了します。

**さらにサポートが必要な場合**

- テスト フェールオーバーの実行に関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)。 
- 復旧計画の作成方法に関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)。
- Azure へのフェールオーバーに関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了しました。SmartHotel アプリは Azure VM 上で実行されており、SmartHotel データベースは Azure SQL Managed Instance 上で使用可能です。  

ここで、以下のようにクリーンアップを実行する必要があります。  

- WEBVM マシンを vCenter インベントリから削除します。
- SQLVM マシンを vCenter インベントリから削除します。
- WEBVM と SQLVM をローカル バックアップ ジョブから削除します。
- WEBVM の新しい場所 (IP アドレス) が示されるように内部ドキュメントを更新します。
- SQLVM をドキュメントから削除します。 あるいは、SQLVM を削除済みで VM インベントリには存在しないものとしてマーキングすることもできます。
- 使用停止されている VM と対話しているリソースがないか確認します。また、関連する設定やドキュメントがあれば、更新して新しい構成を反映します。

## <a name="review-the-deployment"></a>デプロイ環境を検討する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティ保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso セキュリティ チームは、Azure VM と SQL Managed Instance を調査し、その実装に関して、セキュリティ上の問題がないかを判断します。

- アクセスを制御する VM のネットワーク セキュリティ グループ (NSG) を調査します。 NSG は、アプリに許可されたトラフィックのみが通過できるようにするために使用されます。
- ディスク上のデータ保護のために、Azure Disk Encryption と Azure KeyVault の使用も検討します。
- SQL Managed Instance (SQLMI) 上で脅威検出を有効にします。 脅威が検出された場合は、セキュリティ チーム/サービス デスク システムにアラートを送信して、チケットを開きます。 [詳細情報](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection)。

     ![Managed Instance のセキュリティ](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

VM に関するセキュリティの実務の[詳細については、こちら](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control)を参照してください。

### <a name="backups"></a>バックアップ
Contoso は、Azure Backup サービスを使用して、WEBVM 上のデータをバックアップします。 [詳細情報](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

1. Contoso は、WEBVM の既存のライセンスを所有しており、Azure ハイブリッド特典を活用します。  また、この価格を利用するために、既存の Azure VM を変換します。
2. Contoso は、Microsoft の子会社である Cloudyn からライセンスが供与される Azure Cost Management を有効にします。 これは、Azure やその他のクラウド リソースを利用したり、管理したりできるようにするマルチクラウド コスト管理ソリューションです。  Azure Cost Management の詳細については、[こちら](https://docs.microsoft.com/azure/cost-management/overview)を参照してください。 


## <a name="conclusion"></a>まとめ

この記事では、Contoso が Site Recovery サービスを使用して、アプリ フロントエンド VM を Azure に移行し、Azure 内で SmartHotel アプリをリホストしました。 また、DMS を使用して、オンプレミス データベースを Azure SQL Managed Instance に移行しました。

## <a name="next-steps"></a>次の手順

このシリーズの[次の記事](contoso-migration-rehost-vm.md)では、Contoso が Azure Site Recovery サービスを使用して、SmartHotel アプリを Azure VM にリホストする手順を説明します。

