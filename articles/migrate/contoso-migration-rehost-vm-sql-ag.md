---
title: Contoso アプリを Azure VM および SQL Server AlwaysOn 可用性グループに移行してリホストする | Microsoft Docs
description: Contoso が Azure VM および SQL Server AlwaysOn 可用性グループにオンプレミス アプリを移行してリホストする方法を学ぶ
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: raynew
ms.openlocfilehash: 0cfb583f9d16039249aaffe18f71039e91dc3705
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39359208"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-azure-vms-and-sql-server-alwayson-availability-group"></a>Contoso の移行: オンプレミス アプリを Azure VM および SQL Server AlwaysOn 可用性グループにリホストする

この記事では、Contoso が SmartHotel アプリを Azure でリホストする方法について説明します。 アプリのフロントエンド VM を Azure VM、アプリのデータベースを Azure SQL Server VM に移行し、SQL Server AlwaysOn 可能性グループを含む Windows Server フェールオーバー クラスターで実行するようにします。

このドキュメントは、架空の会社 Contoso がオンプレミス リソースを Microsoft Azure クラウドに移行する方法を説明するシリーズ記事の 1 つです。 このシリーズには背景情報やシナリオが含まれ、移行インフラストラクチャのセットアップ、移行のためのオンプレミス リソースへのアクセス、およびさまざまな種類の移行の実行が説明されています。 シナリオが複雑になってきているため、さらに記事が追加される予定です。

**記事** | **詳細** | **状態**
--- | --- | ---
[記事 1: 概要](contoso-migration-overview.md) | Contoso の移行戦略、記事シリーズ、および使用するサンプル アプリの概要を示します。 | 使用可能
[記事 2: Azure インフラストラクチャのデプロイ](contoso-migration-infrastructure.md) | Contoso が移行に備えてオンプレミスおよび Azure インフラストラクチャをどのように準備するかを説明します。 移行に関するすべてのアーティクルでは同じインフラストラクチャが使用されます。 | 使用可能
[記事 3: オンプレミス リソースの評価](contoso-migration-assessment.md)  | VMware で実行されているオンプレミスの 2 階層 SmartHotel アプリの評価を Contoso が実行する方法を説明します。 Contoso は、アプリの VM は [Azure Migrate](migrate-overview.md) サービスを使用して、アプリの SQL Server データベースは [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) を使用して評価します。 | 使用可能
[記事 4: Azure VM および SQL Managed Instance でのアプリのリホスト](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso が、SmartHotel アプリの Azure へのリフトアンドシフト移行を実行する方法を説明します。 Contoso は、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用してアプリのフロントエンド VM を移行し、[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用してアプリのデータベースを SQL Managed Instance に移行します。 | 使用可能
[記事 5: Azure VM でのアプリのリホスト](contoso-migration-rehost-vm.md) | Contoso が Site Recovery のみを使用して SmartHotel アプリ VM を移行する方法を説明します。 | 使用可能
記事 6: Azure VM および SQL Server Always On 可用性グループへのアプリのリホスト | Contoso が SmartHotel アプリを移行する方法を示します。 Contoso は、Site Recovery を使用してアプリの VM を移行し、Database Migration Service を使用してアプリのデータベースを AlwaysOn 可用性グループで保護されている SQL Server クラスターに移行します。 | この記事の内容は次のとおりです。
[記事 7: Linux アプリの Azure VM への再ホスト](contoso-migration-rehost-linux-vm.md) | Contoso が Site Recovery を使用して Azure VM への Linux osTicket アプリのリフトアンドシフト移行を実行する方法を説明します。 | 使用可能
[記事 8: Azure VM および Azure MySQL Server での Linux アプリのリホスト](contoso-migration-rehost-linux-vm-mysql.md) | Contoso が Site Recovery を使用して Linux osTicket アプリを Azure VM に移行する方法、および MySQL Workbench を使用してアプリのデータベースを Azure MySQL Server インスタンスに移行する方法を説明します。 | 使用可能
[記事 9: Azure Web Apps および Azure SQL Database でのアプリのリファクター](contoso-migration-refactor-web-app-sql.md) | Contoso が SmartHotel アプリを Azure Web アプリに移行して、アプリ データベースを Azure SQL Server インスタンスに移行する方法を示します | 使用可能
[記事 10: Azure Web Apps と Azure MySQL での Linux アプリのリファクター](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso が Linux osTicket アプリを複数のサイトの (継続的デリバリーのために GitHub と統合された) Azure Web Apps に移行する方法を示します。 Contoso は、アプリ データベースを Azure MySQL インスタンスに移行します。 | 使用可能
[記事 11: VSTS での TFS のリファクター](contoso-migration-tfs-vsts.md) | Contoso がオンプレミスの Team Foundation Server (TFS) の展開を Azure の Visual Studio Team Services (VSTS) に移行する方法を示します。 | 使用可能
[記事 12: Azure コンテナーと Azure SQL Database でのアプリの再構築](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行して再構築する方法を示します。 Contoso は、アプリの Web 階層を Windows コンテナーとして再構築し、Azure SQL Database にアプリ データベースを再構築します。 | 使用可能
[記事 13: Azure へのアプリのリビルド](contoso-migration-rebuild.md) | Contoso が Azure のさまざまな機能とサービス (App Services、Azure Kubernetes、Azure Functions、Cognitive Services、Cosmos DB など) を使用して SmartHotel アプリをリビルドする方法を示します。 | 使用可能



この記事では、Contoso は、VMware VM で実行されている 2 階層 Windows .NET SmartHotel アプリを Azure に 移行します。 このアプリを使用したい場合は、オープン ソースとして提供されていますので、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードしてください。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応**: Contoso は成長を続けています。そのため、オンプレミス システムおよびインフラストラクチャに対する負荷が高まっています。
- **効率化**: Contoso は不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。  ビジネス部門は IT に対して、時間やコストを無駄にせず、迅速に作業を行ってもらう必要があります。たとえば、顧客の要求に対して、素早く対応する必要があります。
- **敏捷性の強化**: Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 また、グローバル経済で成功を収めるために、市場の変化に対して、より迅速な対応ができる必要があります。  ビジネスの妨げになったり、ビジネスの機会を壊すようなことがあってはなりません。
- **拡張性**: ビジネスが順調に成長していく中で、Contoso IT は、同じペースで拡張可能なシステムを提供する必要があります。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を設定しました。 これらの目標を使用して、最良の移行方法を決定しました。

- 移行しても、Azure のアプリは、現在の VMWare と同じパフォーマンスを発揮できる必要があります。  このアプリは、オンプレミスの場合と同じくクラウドでもクリティカルです。
- Contoso は、このアプリへの投資は望んでいません。  このアプリはビジネスにとって重要なものですが、現在の形で確実にクラウドに移行することだけを望んでいます。
- このアプリのオンプレミス データベースでは、可用性の問題が発生しています。 このデータベースをフェールオーバー機能を備えた高可用性クラスターとして Azure にデプロイすることを望んでいます。
- Contoso は、現在の SQL Server 2008 R2 プラットフォームを SQL Server 2017 にアップグレードしようとしています。
- Contoso は、このアプリに Azure SQL Database を使用したくないと考えており、別のデータベースを探しています。

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

このシナリオでは:

- アプリは 2つの VM (WEBVM および SQLVM) に階層化されています。
- VM は、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上にあります。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。
- Contoso にはオンプレミスのデータセンター (contoso-datacenter) があり、そこにオンプレミスのドメイン コントローラー (**contosodc1**) が含まれています。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

![シナリオのアーキテクチャ](media/contoso-migration-rehost-vm-sql-ag/architecture.png) 

### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | Contoso は、DMS を使用してアプリ データ層を移行します。 DMS が、サイト間 VPN を介してオンプレミス SQLVM マシンに接続して移行します。DMS によって、最小限のダウンタイムで複数のデータベース ソースから Azure データ プラットフォームへのシームレスな移行が可能になります。 | DMS の[サポートされるリージョンに関する記事](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability)と、[価格の詳細に関する記事](https://azure.microsoft.com/pricing/details/database-migration/)を参照しくてださい。
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Contoso は、アプリ フロントエンド VM のリフトアンドシフト移行で Site Recovery を使用します。 Site Recovery は、Azure VM、オンプレミス VM、物理サーバーの移行とディザスター リカバリーを調整および管理します。  | Azure へのレプリケーションの間に、Azure Storage の料金が発生します。  フェールオーバーが発生すると、Azure VM が作成されて料金がかかります。 料金と価格について[詳しくはこちら](https://azure.microsoft.com/pricing/details/site-recovery/)をご覧ください。

 

## <a name="migration-process"></a>移行プロセス

- Contoso はアプリの VM を Azure に移行します。
- フロントエンド VM は、Site Recovery を使用して Azure VM に移行します。
    - 最初の手順として、Azure コンポーネントを準備およびセットアップし、オンプレミス VMware インフラストラクチャを準備します。
    - すべての準備ができたら、VM のレプリケートを開始できます。
    - レプリケーションが有効になり作動したら、VM を Azure にフェールオーバーすることによって移行します。
- データベースは、Data Migration Service (DMS) を使用して、Azure の SQL Server クラスターに移行します。
    - 最初の手順として、Azure の SQL Server VM をプロビジョニングし、クラスターと内部ロード バランサーをセットアップし、AlwaysOn 可用性グループを構成します。
    - このように配置された状態で、データベースを移行することができます。
- 移行後に、データベースの AlwaysOn 保護を有効にします。

![移行プロセス](media/contoso-migration-rehost-vm-sql-ag/migration-process.png) 
 
## <a name="prerequisites"></a>前提条件

このシナリオを実行するために作業担当者 (と Contoso) が必要とするものを以下に示します。

**要件** | **詳細**
--- | ---
**Azure サブスクリプション** | このシリーズの最初の記事で評価を行ったときに、サブスクリプションは既に作成しているはずです。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成してください。<br/><br/> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。<br/><br/> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。<br/><br/> さらに詳細なアクセス許可が必要な場合は、[こちらの記事](../site-recovery/site-recovery-role-based-linked-access-control.md)をご覧ください。 
**Azure インフラストラクチャ** | [Contoso で Azure インフラストラクチャを設定する方法](contoso-migration-infrastructure.md)を確認してください。<br/><br/> Site Recovery 用の[ネットワーク](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)と[ストレージ](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)の具体的な要件の詳細を確認してください。
**Site Recovery (オンプレミス)** | オンプレミスの vCenter Server は、バージョン 5.5、6.0、または 6.5 を実行している必要があります。<br/><br/> バージョン 5.5、6.0、または 6.5 を実行している ESXi ホスト<br/><br/> ESXi ホスト上で実行している 1 つ以上の VMware VM。<br/><br/> VM は [Azure の要件](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)を満たす必要があります。<br/><br/> サポートされている[ネットワーク](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)および[ストレージ](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)の構成。<br/><br/> レプリケートする VM は、[Azure の要件](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)を満たしている必要があります。
**DMS** | DMS では、[互換性のあるオンプレミスの VPN デバイス](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)が必要です。<br/><br/> オンプレミスの VPN デバイスを構成できる必要があります。 外部接続用のパブリック IPv4 アドレスを持っている必要があり、アドレスが NAT デバイスの内側に存在することはできません。<br/><br/> オンプレミスの SQL Server データベースにアクセスできることを確認します。<br/><br/> Windows ファイアウォールは、移行元のデータベース エンジンにアクセスできる必要があります。 [詳細情報](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。<br/><br/> データベース マシンの前面にファイアウォールがある場合は、SMB ポート 445 経由でのデータベースおよびファイルへのアクセスを許可する規則を追加します。<br/><br/> 移行元の SQL Server および移行先のマネージド インスタンスへの接続に使用する資格情報は、sysadmin サーバー ロールのメンバーである必要があります。<br/><br/> DMS が移行元データベースのバックアップに使用できるネットワーク共有がオンプレミスのデータベースに存在する必要があります。<br/><br/> 移行元の SQL Server インスタンスを実行しているサービス アカウントに、ネットワーク共有に対する書き込み権限があることを確認します。<br/><br/> ネットワーク共有に対するフル コントロール権限を持つ Windows ユーザー (とパスワード) をメモしておきます。 Azure Database Migration Service は、これらのユーザーの資格情報を偽装して、Azure ストレージ コンテナーにバックアップ ファイルをアップロードします。<br/><br/> SQL Server Express のインストール プロセスでは、TCP/IP プロトコルが既定で**無効化**されます。 これが有効になっていることを確認します。


## <a name="scenario-steps"></a>シナリオのステップ

Contoso が移行を実行する方法を次に示します。

> [!div class="checklist"]
> * **ステップ 1: Azure に SQL Server VM を作成する**: 高可用性を実現するため、Contoso はクラスター化されたデータベースを Azure にデプロイすることを望んでいます。 2 つの SQL Server VM と 1 つの Azure 内部ロード バランサーをデプロイします。
> * **ステップ 2: クラスターをデプロイする**: SQL Server VM をデプロイした後で、Azure SQL Server クラスターを準備します。  事前に作成したクラスターにデータベースを移行します。
> * **ステップ 3: DMS を準備する**: DMS を準備するために、データベース移行プロバイダーを登録し、DMS インスタンスおよびプロジェクトを作成します。 Shared Access Signature (SAS) の Uniform Resource Identifier (URI) を設定します。 サービスが SQL Server のバックアップ ファイルをアップロードするストレージ アカウント コンテナーに、DMS は SAS URI を使用してアクセスします。
> * **ステップ 4: Site Recovery 用に Azure を準備する**: レプリケートされたデータを保持するための Azure ストレージ アカウントと Recovery Services コンテナーを作成します。
> * **ステップ 5: Site Recovery 用のオンプレミスの VMware を準備する**: VM の検出とエージェントのインストール用のアカウントを準備し、フェールオーバー後に Azure VM に接続できるようにオンプレミス VM を準備します。
> * **ステップ 6: VM をレプリケートする**:レプリケーション設定を構成し、VM レプリケーションを有効にします。
> * **ステップ 7: DMS を使用してデータベースを移行する**: データベースを移行します。
> * **ステップ 8: Site Recovery を使用して VM を移行する**: テスト フェールオーバーを実行してすべてが機能していることを確認した後、完全フェールオーバーを実行して VM を移行します。


## <a name="step-1-prepare-a-sql-server-alwayson-availability-group-cluster"></a>ステップ 1: SQL Server AlwaysOn 可用性グループ クラスターを準備する

Contoso は、Azure 内のクラスターにデータベースをデプロイすることで、事前に計画しようとしています。 この後、このクラスターは他のデータベースで使用できます。 

- SQL Server VM は、ContosoRG リソース グループ (運用リソースで使用される) にデプロイされます。
- 両方の VM は、一意の名前を除いて同じ設定を使用します。
- 内部ロード バランサーは、ContosoNetworkingRG (ネットワーク リソースで使用される) にデプロイされます。
- VM では、Windows Server 2016 と SQL Server 2017 Enterprise Edition が実行されます。 Contoso はこのオペレーティング システムのライセンスを所有していないため、ライセンスを提供する Azure Marketplace のイメージを使用します (これは Azure EA コミットメントに請求されます)。

Contoso がクラスターを設定する方法は次のとおりです。

1. Azure Marketplace で SQL Server 2017 Enterprise Windows Server 2016 イメージを選択して、2 つの SQL Server VM を作成します。 

    ![SQL VM SKU](media/contoso-migration-rehost-vm-sql-ag/sql-vm-sku.png)

2. **仮想マシンの作成ウィザード** > **[基本]** で、次のように構成します。

    - VM の名前: **SQLAOG1** および **SQLAOG2**
    - マシンは Bus Critical であるため、VM ディスクの種類について SSD を有効にします。
    - コンピューターの資格情報を指定します。
    - VM を、米国東部 2 プライマリ リージョンおよび ContosoRG リソース グループにデプロイします。

3. **[サイズ]** については、両方の VM で D2s_V3 SKU から開始します。 必要に応じて後から拡大縮小します。
4. **[設定]** では、次のようにします。

    - これらの VM はアプリの重要なデータベースであるため、マネージド ディスクを使用します。
    - 米国東部 2 プライマリ リージョン (**VNET-PROD-EUS2**) の運用ネットワーク、データベース サブネット (**PROD-DB-EUS2**) にマシンを配置します。
    - 2 つの障害ドメインと 5 つの更新ドメインを含む新しい可用性セット **SQLAOGAVSET** を作成します。

    ![SQL VM](media/contoso-migration-rehost-vm-sql-ag/sql-vm-settings.png)

4. **[SQL Server の設定]** で、SQL 接続をデフォルト ポート 1433 上の仮想ネットワーク (プライベート) に制限します。 認証については、オンサイトで使用するのと同じ資格情報 (**contosoadmin**) を使用します。

    ![SQL VM](media/contoso-migration-rehost-vm-sql-ag/sql-vm-db.png)

**さらにサポートが必要な場合**

- SQL Server VM のプロビジョニングの[手順を参照](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#1-configure-basic-settings)します。
- さまざまな SQL Server SKU の VM の構成方法を[参照します](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-prereq#create-sql-server-vms)。

## <a name="step-2-deploy-the-failover-cluster"></a>ステップ 2: フェールオーバー クラスターをデプロイする

Contoso がクラスターを設定する方法は次のとおりです。

1. クラウド監視として動作するように Azure Storage アカウントを設定します。
2. SQL Server VM を Contoso オンプレミス データセンターの Active Directory ドメインに追加します。
3. Azure においてクラスターを作成します。
4. クラウド監視を構成します。
5. 最後に SQL Always On 可用性グループを有効にします。

### <a name="set-up-a-storage-account-as-cloud-witness"></a>ストレージ アカウントをクラウド監視として設定する

Contoso は、クラウド監視を設定するために、クラスターの調停に使用される BLOB ファイルを保持する Azure Storage アカウントが必要です。 複数のクラスターのクラウド監視の設定に同じストレージ アカウントを使用できます。 

Contoso は次のようにストレージ アカウントを作成します。

1. アカウントのわかりやすい名前 (**contosocloudwitness**) を指定します。
2. LRS の汎用アカウントをデプロイします。
3. サード リージョン (米国中南部) にアカウントを配置します。 リージョンで障害が発生た場合にも使用できるように、プライマリ リージョンやセカンダリ リージョン以外に配置します。
4. インフラストラクチャ リソースを保持するリソース グループ (**ContosoInfraRG**) に配置ます。

    ![クラウド監視](media/contoso-migration-rehost-vm-sql-ag/witness-storage.png)

5. ストレージ アカウントを作成すると、対応するプライマリおよびセカンダリ アクセス キーが生成されます。 クラウド監視を作成するにはプライマリ アクセス キーが必要です。 このキーはストレージ アカウント名の下の **[アクセス キー]** に表示されます。

    ![アクセス キー](media/contoso-migration-rehost-vm-sql-ag/access-key.png)

### <a name="add-sql-server-vms-to-contoso-domain"></a>Contoso ドメインに SQL Server VM を追加する

1. Contoso は SQLAOG1 および SQLAOG2 を contoso.com domain に追加します。
2. 次に、各 VM に Windows フェールオーバー クラスター機能およびツールをインストールします。

## <a name="set-up-the-cluster"></a>クラスターをセットアップする

クラスターを設定する前に、Contoso は、各マシン上の OS ディスクのスナップショットを取得します。

![スナップショット](media/contoso-migration-rehost-vm-sql-ag/snapshot.png)

2. 次に、Windows フェールオーバー クラスターを作成するためにまとめたスクリプトを実行します。

    ![クラスターの作成](media/contoso-migration-rehost-vm-sql-ag/create-cluster1.png)

3. クラスターを作成したら、VM がクラスター ノードとして表示されることを確認します。

     ![クラスターの作成](media/contoso-migration-rehost-vm-sql-ag/create-cluster2.png)

## <a name="configure-the-cloud-witness"></a>クラウド監視を構成する

1. Contoso は、フェールオーバー クラスター マネージャーの**クォーラム構成ウィザード**を使用してクラウド監視を構成します。
2. ウィザードで、ストレージ アカウントでクラウド監視を作成することを選択します。
3. クラウド監視が構成されると、フェールオーバー クラスター マネージャー スナップインに表示されます。

    ![クラウド監視](media/contoso-migration-rehost-vm-sql-ag/cloud-witness.png)
            

## <a name="enable-sql-server-always-on-availability-groups"></a>SQL Server Always On 可用性グループを有効にする

これで、Contoso は Always On を有効にできます。

1. SQL Server 構成マネージャーで、**SQL Server (MSSQLSERVER)** サービスについて **AlwaysOn 可用性グループ**を有効にします。

    ![AlwaysOn の有効化](media/contoso-migration-rehost-vm-sql-ag/enable-alwayson.png)

2. 変更を有効にするためにサービスを再起動します。

AlwaysOn を有効にした状態で、Contoso は SmartHotel データベースを保護する AlwaysOn 可用性グループを設定できます。

**さらにサポートが必要な場合**

- クラウド監視とそのためのストレージ アカウントの設定について[こちらをお読みください](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)。
- クラスターの設定と可用性グループの作成の[手順はこちらを参照してください](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial)。

## <a name="step-3-deploy-the-azure-load-balancer"></a>ステップ 3: Azure Load Balancer をデプロイする

ここで、Contoso は、クラスター ノードの前に置く内部ロード バランサーをデプロイしようとします。 ロード バランサーは、トラフィックをリッスンし、適切なノードにリダイレクトします。

![負荷分散](media/contoso-migration-rehost-vm-sql-ag/architecture-lb.png)

次のようにロード バランサーを作成します。

1. Azure Portal で **[ネットワーク]** > **[ロード バランサー]** の順に選択し、新しい内部ロード バランサー **ILB-PROD-DB-EUS2-SQLAOG** を設定します。
2. 運用ネットワーク **VNET-PROD-EUS2**、データベース サブネット **PROD-DB-EUS2** にロード バランサーを配置します。
3. 静的 IP アドレス 10.245.40.100 を割り当てます。
4. ロード バランサーをネットワークの要素としてネットワーク リソース グループ **ContosoNetworkingRG** にデプロイします。

    ![負荷分散](media/contoso-migration-rehost-vm-sql-ag/lb-create.png)

内部ロード バランサーがデプロイされたら設定します。 バックエンド アドレス プールを作成し、正常性プローブを設定し、負荷分散規則を構成します。

### <a name="add-a-backend-pool"></a>バックエンド プールを追加する

Contoso は、クラスターの VM にトラフィックを分散させるために、ロード バランサーからネットワーク トラフィックを受け取る VM の NIC の IP アドレスを含むバックエンド アドレス プールを設定します。

1. Contoso は、ポータルのロード バランサー設定にバックエンド プール **ILB-PROD-DB-EUS-SQLAOG-BEPOOL** を追加します。
2. このプールには可用性セット SQLAOGAVSET を関連付けます。 セット内の VM (**SQLAOG1** および **SQLAOG2**) がプールに追加されます。

    ![バックエンド プール](media/contoso-migration-rehost-vm-sql-ag/backend-pool.png)

### <a name="create-a-health-probe"></a>正常性プローブの作成

Contoso は正常性プローブを作成して、ロード バランサーがアプリの正常性を監視できるようにします。 プローブは、正常性チェックへの VM の応答に基づき、ロード バランサーのローテーションに対して VM を動的に追加または削除します。

次のようにプローブを作成します。 

1. Contoso は、ポータルのロード バランサー設定で正常性プローブ **SQLAlwaysOnEndPointProbe** を作成します。
2. TCP ポート 59999 上で VM を監視するようにプローブを設定します。
3. プローブの間隔は 5 秒、しきい値は 2 に設定します。 プローブが 2 回失敗すると、VM が異常と見なされます。

    ![プローブ](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

### <a name="configure-the-load-balancer-to-receive-traffic"></a>トラフィックを受信するロード バランサーを構成する


ここで、Contoso は、VM に対するトラフィックの分散方法を定義するロード バランサー規則が必要です。

- フロントエンド IP アドレスが受信トラフィックを処理します。
- バックエンド IP プールがトラフィックを受信します。

次のように規則を作成します。

1. ポータルのロード バランサー設定に、新しい負荷分散規則 **SQLAlwaysOnEndPointListener** を追加します。
2. Contoso は、フロントエンド リスナーが TCP 1433 に着信する SQL クライアント トラフィックを受信するように設定します。
3. トラフィックのルーティング先のバックエンド プールと、VM がトラフィックをリッスンするポートを指定します。
4. Contoso は Floating IP (Direct Server Return) を有効にします。 これは SQL AlwaysOn では常に必要です。

    ![プローブ](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

**さらにサポートが必要な場合**

- Azure Load Balancer の[概要を参照してください](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。
- ロード バランサーを作成する方法を[参照します](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-basic-internal-portal)。



## <a name="step-4-prepare-azure-for-the-site-recovery-service"></a>ステップ 4: Site Recovery サービス用に Azure を準備する

Contoso が Site Recovery をデプロイする必要がある Azure コンポーネントは次のとおりです。

- フェールオーバー時に作成される VM が配置される VNet。
- レプリケートされたデータを保持する Azure Storage アカウント。 
- Azure 内の Recovery Services コンテナー。

これらを次のように設定します。

1.  Contoso は、[Azure インフラストラクチャのデプロイ](contoso-migration-rehost-vm-sql-ag.md)を行ったときに、Site Recovery 用に使用できるネットワークとサブネットを既に作成しています。

    - SmartHotel アプリは運用アプリであり、WEBVM はプライマリ リージョンである米国東部 2 の Azure 運用ネットワーク (VNET PROD EUS2) に移行されます。
    - WEBVM は、運用リソースのために使用される ContosoRG リソース グループと、運用サブネット (PROD-FE-EUS2) に配置されます。

2. Contoso は、プライマリ リージョンに Azure ストレージ アカウント (contosovmsacc20180528) を作成します。

    - 標準ストレージと LRS レプリケーション付きの汎用アカウントを使用します。
    - アカウントは、コンテナーと同じリージョンにある必要があります。

    ![Site Recovery ストレージ](media/contoso-migration-rehost-vm-sql-ag/asr-storage.png)

3. ネットワークとストレージ アカウントが用意できたので、Recovery Services コンテナー (**ContosoMigrationVault**) を作成し、それを米国東部 2 プライマリ リージョンの **ContosoFailoverRG** リソース グループ内に配置します。

    ![Recovery Services コンテナー](media/contoso-migration-rehost-vm-sql-ag/asr-vault.png)

**さらにサポートが必要な場合**

Site Recovery のために Azure を設定することについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure)。


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>ステップ 4: Site Recovery のためにオンプレミスの VMware を準備する

Contoso は、オンプレミスで以下を準備します。

- VM の検出を自動化するための vCenter サーバーまたは vSphere ESXi ホストのアカウント。
- レプリケートする VMware VM 上にモビリティ サービスを自動インストールできるアカウント。
- フェールオーバー後にレプリケートされた Azure VM に Contoso が接続できるようにするためのオンプレミスの VM の設定。


### <a name="prepare-an-account-for-automatic-discovery"></a>自動検出用のアカウントを準備する

Site Recovery では、次のことを実行するために、VMware サーバーへのアクセスが必要です。

- VM を自動的に検出します。 
- レプリケーション、フェールオーバー、およびフェールバックを調整します。
- 少なくとも読み取り専用のアカウントが必要です。 ディスクを作成および削除する、VM の電源をオンにするなどの操作を実行できるアカウントが必要です。

Contoso は、アカウントを以下のように設定します。

1. Contoso は、vCenter レベルでロールを作成します。
2. Contoso は次に、必要なアクセス許可をそのロールに割り当てます。



### <a name="prepare-an-account-for-mobility-service-installation"></a>モビリティ サービスのインストール用のアカウントを準備する

各 VM にモビリティ サービスをインストールする必要があります。

- VM のレプリケーションが有効になっていると、Site Recovery はこのコンポーネントの自動プッシュ インストールを行うことができます。
- プッシュ インストールの場合は、Site Recovery で VM へのアクセスに使用できるアカウントを準備する必要があります。 Azure コンソールでレプリケーションを設定するときに、このアカウントを指定します。
- このアカウントは、VM にインストールする権限があれば、ドメイン アカウントでもローカル アカウントでもかまいません。




### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための準備をする

Contoso は、フェールオーバー後に Azure VM に接続することを望んでいます。 これを行うには、移行前に以下の操作を行います。

1. インターネットでアクセスする場合:

 - フェールオーバーの前に、オンプレミスの VM で RDP を有効にします。
 - **パブリック** プロファイルに TCP 規則と UDP 規則が追加されていることを確認します。
 - **[Windows ファイアウォール]** > **[許可されたアプリ]** で、すべてのプロファイルで RDP が許可されていることを確認します。
 
2. サイト間 VPN でアクセスする場合:

 - オンプレミスのマシンで RDP を有効にします。
 - **[Windows ファイアウォール]** -> **[Allowed apps and features]\(許可されたアプリと機能\)** で、**ドメイン ネットワークとプライベート ネットワーク**での RDP を許可します。
 - オンプレミスの VM 上のオペレーティング システムの SAN ポリシーを **[OnlineAll]** に設定します。

さらに、フェールオーバーを実行するときに、以下を確認する必要があります。

- フェールオーバーをトリガーするときに、VM 上に保留中の Windows 更新プログラムがないようにします。 ある場合は、更新が完了するまで、VM にログインすることはできません。
- フェールオーバー後、**ブート診断**を調べて、VM のスクリーンショットを確認できます。 これが機能しない場合は、VM が実行中であることを確認し、こちらの[トラブルシューティングのヒント](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を参照してください。


**さらにサポートが必要な場合**

- 自動検出のためにロールを作成して割り当てることについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery)。
- モビリティ サービスのプッシュ インストールのためにアカウントを作成することについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation)。


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>ステップ 5: Site Recovery を使用して、オンプレミス VM を Azure にレプリケートする

Contoso は、Azure への移行を実行する前に、レプリケーションを設定して有効にする必要があります。

### <a name="set-a-replication-goal"></a>レプリケーションの目標を設定する

1. コンテナー内のコンテナー名 (ContosoVMVault) で、レプリケーションの目標を設定します (**[作業の開始]** > **[Site Recovery]** > **[インフラストラクチャの準備]**)。
2. 各マシンがオンプレミスに配置されていること、VMware で実行されていること、および Azure にレプリケートすることを指定します。

    ![レプリケーションの目標](./media/contoso-migration-rehost-vm-sql-ag/replication-goal.png)

### <a name="confirm-deployment-planning"></a>展開の計画を確認する

続行するには、デプロイ計画が完了したことを確認するために、**[はい、完了しました]** を選択する必要があります。 このシナリオでは、Contoso が移行するのは 1 つの VM だけなので、デプロイ計画は不要です。

### <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

Contoso はソース環境を構成する必要があります。 これを行うには、OVF テンプレートをダウンロードし、それを使用して、Site Recovery 構成サーバーを高可用性オンプレミス VMware VM としてデプロイします。 構成サーバーを起動して実行したら、それをコンテナーに登録します。

構成サーバーでは、いくつかのコンポーネントが実行されます。

- オンプレミスと Azure の間の通信を調整し、データのレプリケーションを管理する構成サーバー コンポーネント。
- プロセス サーバー: レプリケーション ゲートウェイとして機能します。 レプリケーション データを受信し、そのデータをキャッシュ、圧縮、暗号化によって最適化して、Azure Storage に送信します。
- また、プロセス サーバーは、レプリケートする VM へのモビリティ サービスのインストールや、オンプレミスの VMware VM の自動検出も行います。

Contoso は、これらのステップを以下のように実行します。


1. コンテナーで、**[インフラストラクチャの準備]** > **[ソース]** > **[構成サーバー]** から OVF テンプレートをダウンロードします。
    
    ![OVF をダウンロードする](./media/contoso-migration-rehost-vm-sql-ag/add-cs.png)

2. VMware にテンプレートをインポートし、VM を作成してデプロイします。

    ![OVF テンプレート](./media/contoso-migration-rehost-vm-sql-ag/vcenter-wizard.png)

3. 初めて VM をオンにすると、Windows Server 2016 インストール環境が起動します。 使用許諾契約書に同意し、管理者パスワードを入力します。
4. インストールの完了後に、管理者として VM にサインインします。 初めてサインインしたときは、Azure Site Recovery 構成ツールが既定で実行されます。
5. このツールでは、構成サーバーをコンテナーに登録するために使用する名前を指定します。
6. このツールは、VM が Azure に接続できることを確認します。 接続が確立されたら、Azure サブスクリプションにサインインします。 この資格情報は、構成サーバーを登録するコンテナーにアクセスできる必要があります。

    ![構成サーバーの登録](./media/contoso-migration-rehost-vm-sql-ag/config-server-register2.png)

7. ツールがいくつかの構成タスクを実行した後、再起動されます。
8. マシンに再度サインインすると、構成サーバーの管理ウィザードが自動的に起動されます。
9. ウィザードで、レプリケーション トラフィックを受信する NIC を選択します。 この設定は、構成後に変更することはできません。
10. サブスクリプション、リソース グループ、および構成サーバーを登録するコンテナーを選択します。
        ![コンテナー](./media/contoso-migration-rehost-vm-sql-ag/cswiz1.png) 

10. MySQL Server と VMWare PowerCLI をダウンロードしてインストールします。 
11. 検証が終わったら、vCenter サーバーまたは vSphere ホストの FQDN または IP アドレスを指定します。 ポートは既定のままにして、vCenter サーバーには、わかりやすい名前を指定します。
12. 自動検出のために作成したアカウントと、モビリティ サービスを自動的にインストールするために使用する資格情報を指定します。 Windows マシンの場合、このアカウントには、VM に対するローカル管理者特権が必要です。

    ![vCenter](./media/contoso-migration-rehost-vm-sql-ag/cswiz2.png)

7. 登録が完了したら、Azure Portal で、構成サーバーと VMware サーバーがコンテナーの **[ソース]** ページの一覧に表示されていることを再確認します。 検出には 15 分以上かかる場合があります。 
8. Site Recovery が、指定された設定を使用して VMware サーバーに接続し、VM を検出します。

### <a name="set-up-the-target"></a>ターゲットをセットアップする

次に、Contoso は、ターゲットのレプリケーション設定を構成します。

1. **[インフラストラクチャの準備]**(インフラストラクチャの準備) > **[ターゲット]** で、ターゲットの設定を選択します。
2. Site Recovery は、指定されたターゲットに Azure のストレージ アカウントとネットワークが存在することを確認します。

### <a name="create-a-replication-policy"></a>レプリケーション ポリシーを作成する

次に、Contoso は、レプリケーション ポリシーを作成できます。

1. **[インフラストラクチャの準備]** > **[レプリケーション設定]** > **[レプリケーション ポリシー]** >  **[作成と関連付け]** で、ポリシー **ContosoMigrationPolicy** を作成します。
2. 以下の既定の設定を使用します。
    - **[RPO しきい値]**: 既定値は 60 分です。 この値で、復旧ポイントの作成頻度を指定します。 継続的なレプリケーションがこの制限を超えると、アラートが生成されます。
    - **[復旧ポイントのリテンション期間]**。 既定値は 24 時間です。 この値は、各復旧ポイントのリテンション期間の長さを指定します。 レプリケートされた VM は、期間内の任意の時点に復旧できます。
    - **[アプリ整合性スナップショットの頻度]**。 既定値は 1 時間です。 この値は、アプリケーション整合性スナップショットが作成される頻度を指定します。
 
        ![レプリケーション ポリシーの作成](./media/contoso-migration-rehost-vm-sql-ag/replication-policy.png)

5. このポリシーは自動的に構成サーバーに関連付けられます。 

    ![レプリケーション ポリシーを関連付ける](./media/contoso-migration-rehost-vm-sql-ag/replication-policy2.png)



### <a name="enable-replication"></a>レプリケーションを有効にする

Contoso は、WebVM のレプリケートを開始できるようになりました。

1. **[アプリケーションのレプリケート]** > **[ソース]** > **[+Replicate]\(+ レプリケート\)** で、ソースの設定を選択します。
2. VM を有効にすることを指定し、vCenter Server と構成サーバーを選択します。

    ![レプリケーションを有効にする](./media/contoso-migration-rehost-vm-sql-ag/enable-replication1.png)

3. ここで、ターゲットの設定を指定します。これには、リソース グループと VNet、レプリケートされたデータが保存されるストレージ アカウントを指定することも含まれます。

     ![レプリケーションを有効にする](./media/contoso-migration-rehost-vm-sql-ag/enable-replication2.png)

3. WebVM のレプリケーションを選択し、レプリケーション ポリシーを確認し、レプリケーションを有効にします。 レプリケーションを有効にすると、Site Recovery が VM にモビリティ サービスをインストールします。
 
    ![レプリケーションを有効にする](./media/contoso-migration-rehost-vm-sql-ag/enable-replication3.png)

4. レプリケーションの進行状況は、**[ジョブ]** で追跡します。 **保護の最終処理**ジョブが実行されると、マシンはフェールオーバーできる状態になります。
5. Azure Portal の **[要点]** で、Azure にレプリケートする VM の構造を確認できます。

    ![インフラストラクチャ ビュー](./media/contoso-migration-rehost-vm-sql-ag/essentials.png)


**さらにサポートが必要な場合**

- これらすべてのステップの詳細な手順については、[オンプレミス VMware VM のディザスター リカバリーの設定に関する記事](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)を参照してください。
- [ソース環境の設定](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[構成サーバーのデプロイ](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)、および[レプリケーション設定の構成](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)に役立つ詳細な手順が記載されています。
- [レプリケーションの有効化](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication)の詳細を確認します。


## <a name="step-5-install-the-database-migration-assistant-dma"></a>ステップ 5: Database Migration Assistant (DMA) をインストールする

Contoso は、DMA を使用して SmartHotel データベースを Azure VM **SQLAOG1** に移行します。 次のように DMA を設定します。

1. [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=53595) からオンプレミスの SQL Server VM (**SQLVM**) にツールをダウンロードします。
2. VM 上でセットアップ (DownloadMigrationAssistant.msi) を実行します。
3. **[完了]** ページで、ウィザードを終了する前に **[Microsoft Data Migration Assistant の起動]** を選択します。

## <a name="step-6-migrate-the-database-with-dma"></a>ステップ 6: DMA を使用してデータベースを移行する

1. DMA で、新しい移行 **SmartHotel** を実行します。
2. **[Target server type]\(対象サーバーの種類\)** で **[Azure Virtual Machines 上の SQL Server]** を選択します。 

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-1.png)

3. 移行の詳細では、**SQLVM** をソース サーバーとして、**SQLAOG1** を対象サーバーとして追加します。 各マシンの資格情報を指定します。

     ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-2.png)

4. データベースと構成情報についてローカルの共有を作成します。 これには、SQLVM および SQLAOG1 上の SQL Service アカウントが 書き込みアクセス権を使用してアクセスできる必要があります。

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-3.png)

5. Contoso は、移行する必要があるログインを選択し、移行を開始します。 これが完了すると、DMA は移行が正常に完了したことを示します。

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-4.png)

6. データベースが **SQLAOG1** 上で実行されていることを確認します。

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-5.png)

DMS は、Contoso データセンターと Azure 間でサイト間 VPN 接続を通じてオンプレミス SQL Server VM に接続し、データベースを移行します。

## <a name="step-7-protect-the-database"></a>ステップ 7: データベースを保護する

アプリのデータベースが **SQLAOG1** で実行されており、Contoso は AlwaysOn 可用性グループを使用してこれを保護できます。 SQL Management Studio を使用して AlwaysOn を構成し、Windows クラスタリングを使用してリスナーを割り当てます。 

### <a name="create-an-alwayson-availability-group"></a>AlwaysOn 可用性グループを作成する

1. SQL Management Studio で **[Always On 高可用性]** を右クリックして、**新しい可用性グループ ウィザード**を開始します。
2. **[オプションの指定]** で、可用性グループ **SHAOG** を指定します。 **[データベースの選択]** で、SmartHotel データベースを選択します。

    ![AlwaysOn 可用性グループ](media/contoso-migration-rehost-vm-sql-ag/aog-1.png)

3. **[レプリカの指定]** で、2 つの SQL ノードを可用性レプリカとして追加し、同期コミットを備えた自動フェールオーバーを提供するように構成します。

     ![AlwaysOn 可用性グループ](media/contoso-migration-rehost-vm-sql-ag/aog-2.png)

4. グループ (**SHAOG**) のリスナーとポートを構成します。 内部ロード バランサーの IP アドレスは、静的 IP アドレス (10.245.40.100) として追加されます。

    ![AlwaysOn 可用性グループ](media/contoso-migration-rehost-vm-sql-ag/aog-3.png)

5. **[データ同期の選択]** で、自動シード処理を有効にします。 このオプションでは、グループ内のすべてのデータベースのセカンダリ レプリカが SQL Server によって自動的に作成されるため、Contoso がバックアップと復元を手動で行う必要はありません。 検証後、可用性グループが作成されます。

    ![AlwaysOn 可用性グループ](media/contoso-migration-rehost-vm-sql-ag/aog-4.png)

6. Contoso がグループを作成するときに問題が発生しました。 Active Directory Windows 統合セキュリティを使用していないため、SQL ログインにアクセス許可を付与して、Windows フェールオーバー クラスター ロールを作成する必要があります。

    ![AlwaysOn 可用性グループ](media/contoso-migration-rehost-vm-sql-ag/aog-5.png)

6. グループが作成されると、Contoso は SQL Management Studio で確認できます。

### <a name="configure-a-listener-on-the-cluster"></a>クラスターでリスナーを構成する

SQL デプロイを設定する最後のステップとして、Contoso は内部ロード バランサーをクラスター上のリスナーとして構成し、リスナーをオンラインにします。 これにはスクリプトを使用します。

![クラスターのリスナー](media/contoso-migration-rehost-vm-sql-ag/cluster-listener.png)


### <a name="verify-the-configuration"></a>構成を確認する

このとき、すべての設定が終了し、機能する可用性グループが Azure に存在し、移行したデータベースを使用しています。 これを確認するには、SQL Management Studio で内部ロード バランサーに接続します。

![ILB 接続](media/contoso-migration-rehost-vm-sql-ag/ilb-connect.png)

**さらにサポートが必要な場合**
- [可用性グループ](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#create-the-availability-group)と[リスナー](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#configure-listener)を作成する方法を参照します。
- 手動で[クラスターがロード バランサーの IP アドレスを使用するように設定](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener#configure-the-cluster-to-use-the-load-balancer-ip-address)します。
- SAS の作成および使用については、[こちら](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)をご覧ください。


## <a name="step-8-migrate-the-vm-with-site-recovery"></a>ステップ 8: Site Recovery で VM を移行する

Contoso は、簡単なテスト フェールオーバーを実行してから VM を移行します。

### <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

テスト フェールオーバーを実行すると、移行の前に、すべてが正常に機能していることを確認する助けになります。 

1. Contoso では、利用可能な最新の時点 (**[最後に処理があった時点]**) へのテスト フェールオーバーを実行します。
2. **[Shut down machine before beginning failover]\(フェールオーバー前にマシンをシャットダウンする\)** を選択します。これにより、Site Recovery は、フェールオーバーをトリガーする前にソース VM のシャットダウンを試みます。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 
3. テスト フェールオーバーでは、以下が実行されます。 

    - 移行を実行するために必要なすべての条件が満たされていることを確認する前提条件チェックが実行されます。
    - Azure VM を作成できるように、フェールオーバーによってデータが処理されます。 最新の復旧ポイントを選択した場合は、データから復旧ポイントが作成されます。
    - 前の手順で処理されたデータを使用して、Azure VM が作成されます。

3. フェールオーバーが完了すると、Azure Portal にはレプリカの Azure VM が表示されます。 VM が適切なサイズであること、適切なネットワークに接続されていること、実行中であることを確認します。 
4. Contoso は、確認後、フェールオーバーをクリーンアップして、確認された事柄をすべて記録して保存します。 

### <a name="run-a-failover"></a>フェールオーバーの実行

1. テスト フェールオーバーが正常に機能することを確認したら、移行のための復旧計画を作成し、WEBVM を計画に追加します。

     ![復旧計画](./media/contoso-migration-rehost-vm-sql-ag/recovery-plan.png)

2. 計画に基づいてフェールオーバーを実行します。 最新の復旧ポイントを選択し、Site Recovery では、フェールオーバーをトリガーする前にオンプレミス VM のシャットダウンを試みる必要があることを指定します。

    ![フェールオーバー](./media/contoso-migration-rehost-vm-sql-ag/failover1.png)

3. フェールオーバーが完了したら、Azure VM が想定どおりに Azure Portal に表示されることを確認します。

    ![復旧計画](./media/contoso-migration-rehost-vm-sql-ag/failover2.png)

6. Azure の VM を検査したら、移行を完了して移行プロセスを終了し、VM のレプリケーションを停止して、VM の Site Recovery の課金を停止します。

    ![フェールオーバー](./media/contoso-migration-rehost-vm-sql-ag/failover3.png)

### <a name="update-the-connection-string"></a>接続文字列を更新する

移行プロセスの最終ステップとして、アプリケーションの接続文字列を更新して、SHAOG リスナーで実行されている移行されたデータベースを指すようにします。 この構成は、現在 Azure で実行されている WEBVM 上で変更します。  この構成は ASP アプリケーションの web.config にあります。 

1. C:\inetpub\SmartHotelWeb\web.config でファイルを確認します。サーバーの名前が、AOG の FQDN (shaog.contoso.com) を反映するように変更します。

    ![フェールオーバー](./media/contoso-migration-rehost-vm-sql-ag/failover4.png)  

2. ファイルを更新して保存した後、WEBVM 上で IIS を再起動します。 これは、コマンド プロンプトから IISRESET /RESTART を使って行います。
2. IIS が再起動すると、アプリケーションは SQL MI 上で実行されているデータベースを使用しています。


**さらにサポートが必要な場合**

- テスト フェールオーバーの実行に関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)。 
- 復旧計画の作成方法に関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)。
- Azure へのフェールオーバーに関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行後、SmartHotel アプリは Azure VM 上で実行されており、SmartHotel データベースは Azure SQL クラスター上にあります。

次に、Contoso は、以下のクリーンアップ手順を完了する必要があります。  

- vCenter のインベントリからオンプレミスの VM を削除します。
- ローカルのバックアップ ジョブからから VM を削除します。
- VM の新しい場所と IP アドレスを示すように社内ドキュメントを更新します。
- 使用停止されている VM と対話しているリソースがないか確認します。また、関連する設定やドキュメントがあれば、更新して新しい構成を反映します。
- 2 つの新しい VM (SQLAOG1 および SQLAOG2) を運用監視システムに追加します。

## <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティ保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso のセキュリティ チームは、Azure VM (WEBVM、SQLAOG1 および SQLAOG2) を再調査して、セキュリティの問題を特定します。 

- VM がアクセスを制御するためのネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、アプリケーションに対して許可されるトラフィックのみが通過できるようにするために使用されます。
- ディスク上のデータ保護のために、Azure Disk Encryption と KeyVault の使用を検討します。
- Transparent Data Encryption (TDE) を評価してから、新しい SQL AOG で実行されている SmartHotel データベース上で有効にします。 [詳細情報](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017)。

VM に関するセキュリティの実務の[詳細については、こちら](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control)を参照してください。

### <a name="backups"></a>バックアップ

Contoso は、Azure Backup サービスを使用して、WEBVM、SQLAOG1 および SQLAOG2 上のデータをバックアップします。 [詳細情報](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

1. Contoso は、WEBVM の既存のライセンスを所有しており、Azure ハイブリッド特典を活用します。  この特典を活用するために、既存の Azure VM を変換します。
2. Contoso は、Microsoft の子会社である Cloudyn からライセンスが供与される Azure Cost Management を有効にします。 これは、Azure やその他のクラウド リソースの利用や管理に役立つ、マルチクラウド対応のコスト管理ソリューションです。  Azure Cost Management の詳細については、[こちら](https://docs.microsoft.com/azure/cost-management/overview)を参照してください。 

## <a name="conclusion"></a>まとめ

この記事では、Contoso が Site Recovery サービスを使用して、アプリ フロントエンド VM を Azure に移行し、Azure 内で SmartHotel アプリをリホストしました。 Azure にプロビジョニングされた SQL Server クラスターにアプリのデータベースを移行して、SQL Server AlwaysOn 可用性グループ内で保護します。

## <a name="next-steps"></a>次の手順

シリーズの次の記事では、Contoso が、Linux 上で実行され、MySQL データベースに対してデプロイされているサービス デスク osTicket アプリをどのようにリホストするかを説明します。


