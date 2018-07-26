---
title: Contoso での Azure への移行についてオンプレミスのワークロードを評価する | Microsoft Docs
description: Azure Migration と Database Migraton を使用した Azure への移行について、Contoso でオンプレミスのマシンを評価する方法を説明します
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: fa6fb4ffe1eea98392b2199f379431b0dffc6774
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006568"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Contoso での移行: Azure への移行の対象となるオンプレミスのワークロードを評価する

この記事では、Azure への移行の準備として、Contoso でオンプレミスの SmartHotel アプリを評価する方法を説明します。

このドキュメントは、架空の会社 Contoso がオンプレミス リソースを Microsoft Azure クラウドに移行する方法を説明するシリーズ記事の第 3 弾です。 このシリーズには背景情報や一連のデプロイ シナリオが含まれ、移行インフラストラクチャをセットアップする方法、移行のためのオンプレミス リソースの適合性を評価する方法、さまざまな種類の移行を実行する方法が説明されます。 シナリオが複雑になってきているため、徐々に記事が追加される予定です。

**記事** | **詳細** | **状態**
--- | --- | ---
[記事 1: 概要](contoso-migration-overview.md) | Contoso の移行戦略、記事シリーズ、および使用するサンプル アプリの概要を示します。 | 使用可能
[記事 2: Azure インフラストラクチャのデプロイ](contoso-migration-infrastructure.md) | Contoso が移行に備えてオンプレミスおよび Azure インフラストラクチャをどのように準備するかを説明します。 移行に関するすべてのアーティクルでは同じインフラストラクチャが使用されます。 | 使用可能
記事 3: Azure への移行の対象となるオンプレミスのリソースの評価  | VMware で実行されているオンプレミスの 2 階層 SmartHotel アプリの評価を Contoso が実行する方法を説明します。 Contoso は、アプリの VM は [Azure Migrate](migrate-overview.md) サービスを使用して、アプリの SQL Server データベースは [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) を使用して評価します。 | この記事の内容は次のとおりです。
[記事 4: Azure VM および SQL Managed Instance へのアプリのリホスト](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso が、オンプレミスの SmartHotel アプリの Azure へのリフトアンドシフト移行を実行する方法を説明します。 Contoso は、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用してアプリのフロントエンド VM を移行し、[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用してアプリのデータベースを SQL Managed Instance に移行します。 | 使用可能
[記事 5: Azure VM へのアプリの再ホスト](contoso-migration-rehost-vm.md) | Contoso が Site Recovery サービスを使用して SmartHotel アプリの VM を Azure VM に移行する方法を示します。 | 使用可能
[記事 6: Azure VM および SQL Server Always On 可用性グループへのアプリのリホスト](contoso-migration-rehost-vm-sql-ag.md) | Contoso が SmartHotel アプリを移行する方法を示します。 Contoso は、Site Recovery を使用してアプリの VM を移行し、Database Migration Service を使用してアプリのデータベースを AlwaysOn 可用性グループで保護されている SQL Server クラスターに移行します。 | 使用可能
[記事 7: Linux アプリの Azure VM への再ホスト](contoso-migration-rehost-linux-vm.md) | Contoso が Site Recovery を使用して Azure VM への Linux osTicket アプリのリフトアンドシフト移行を実行する方法を説明します。 | 使用可能
[記事 8: Azure VM と Azure MySQL への Linux アプリのリホスト](contoso-migration-rehost-linux-vm-mysql.md) | Contoso が Site Recovery を使用して Linux osTicket アプリを Azure VM に移行する方法、および MySQL Workbench を使用してアプリのデータベースを Azure MySQL Server インスタンスに移行する方法を説明します。 | 使用可能
[記事 9: Azure Web Apps と Azure SQL Database でのアプリのリファクター](contoso-migration-refactor-web-app-sql.md) | Contoso が SmartHotel アプリを Azure Web アプリに移行して、アプリ データベースを Azure SQL Server インスタンスに移行する方法を示します | 使用可能
[記事 10: Azure Web Apps と Azure MySQL での Linux アプリのリファクター](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso が Linux osTicket アプリを複数のサイトの (継続的デリバリーのために GitHub と統合された) Azure Web Apps に移行する方法を示します。 Contoso は、アプリ データベースを Azure MySQL インスタンスに移行します。 | 使用可能
[記事 11: VSTS での TFS のリファクター](contoso-migration-tfs-vsts.md) | Contoso がオンプレミスの Team Foundation Server (TFS) デプロイを Azure の Visual Studio Team Services (VSTS) に移行する方法を示します。 | 使用可能
[記事 12: Azure コンテナーと Azure SQL Database でのアプリの再構築](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行して再構築する方法を示します。 Contoso は、アプリの Web 階層を Windows コンテナーとして再構築し、Azure SQL Database でアプリ データベースを再構築します。 | 使用可能
[記事 13: Azure でのアプリのリビルド](contoso-migration-rebuild.md) | Contoso が Azure のさまざまな機能とサービス (App Services、Azure Kubernetes、Azure Functions、Cognitive Services、Cosmos DB など) を使用して SmartHotel アプリをリビルドする方法を示します。 | 使用可能


## <a name="overview"></a>概要

Azure への移行を検討する際、Contoso 社は技術的および財務的な評価を実行して、オンプレミスのワークロードがクラウドへの移行に適しているかどうかを確認する必要があります。 Contoso 社のチームは特に、移行に関してマシンおよびデータベースの互換性を評価し、Azure でリソースを実行するための容量とコストを見積もる必要があります。

取り組みを始めて関連するテクノロジの理解を深めるために、次の表にまとめたオンプレミス アプリのうち 2 つを評価します。 移行のためにアプリのリホストとリファクタリングを行う移行シナリオを評価することに注意してください。 リホストとリファクタリングの詳細については、[Contoso での移行の概要](contoso-migration-overview.md)に関する記事を参照してください。

**アプリ名** | **プラットフォーム** | **アプリ層** | **詳細**
--- | --- | --- | ---
SmartHotel<br/><br/> Contoso の旅行要件を管理します | SQL Server データベースを含む Windows 上で実行 | 1 つの VM (WEBVM) 上で実行されるフロントエンド ASP.NET Web サイトと、別の VM (SQLVM) 上で実行される SQL Server を含む 2 層アプリ | VM は VMware であり、vCenter サーバーによって管理される ESXi ホスト上で実行されます。<br/><br/> このサンプル アプリは [GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。
OSTicket<br/><br/> Contoso のサービス デスク アプリ | MySQL PHP (LAMP) を含む Linux/Apache 上で実行。 | 1 つの VM (OSTICKETWEB) 上のフロントエンド PHP Web サイトと、別の VM (OSTICKETMYSQL) 上で実行される MySQL データベースを含む 2 層アプリ | このアプリは、社内の従業員と外部の顧客の問題を追跡するために、顧客サービス アプリによって使用されます。<br/><br/> このサンプル アプリは [GitHub](https://github.com/osTicket/osTicket) からダウンロードできます。

## <a name="current-architecture"></a>現在のアーキテクチャ


以下の図は、現在の Contoso オンプレミス アーキテクチャを示しています。

![Contoso アーキテクチャ](./media/contoso-migration-assessment/contoso-architecture.png)  

- Contoso は、米国東部のニューヨーク市に 1 つのメイン データセンターを配置しています。
- Contoso は、米国の地方に 3 つの支店を配置しています。
- メイン データセンターは、光ファイバーによるイーサネット接続 (500 mbps) を通じて、インターネットに接続されています。
- 各支店は、ビジネス クラスの接続を使用して、インターネットにローカルで接続されています。メイン データセンターには、IPSec VPN トンネルで接続されています。 これにより、ネットワーク全体を永続的に接続でき、インターネット接続が最適化されます。
- メイン データセンターは、VMware によって完全に仮想化されています。 ESXi 6.5 仮想化ホストが 2 つあり、vCenter Server 6.5 で管理されています。
- Contoso は、ID 管理に Active Directory を使用しており、内部ネットワーク上で DNS サーバーを使用しています。
- データセンター内のドメイン コントローラーは、VMware VM 上で実行されています。 支店にあるドメイン コントローラーは、物理サーバー上で実行されています。





## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス部門のパートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応**: Contoso は成長を続けています。そのため、オンプレミス システムおよびインフラストラクチャに対する負荷が高まっています。
- **効率化**: Contoso は不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。  ビジネス部門は IT に対して、時間やコストを無駄にせず、迅速に作業を行ってもらう必要があります。これは、例えば、顧客の要求に素早く対応するためです。
- **敏捷性の強化**: Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 また、グローバル経済で成功を収めるために、市場の変化に対して、より迅速な対応ができる必要があります。  ビジネスの妨げになったり、ビジネスの機会を壊すようなことがあってはなりません。
- **拡張性**: ビジネスが順調に成長していく中で、Contoso IT は、同じペースで拡張可能なシステムを提供する必要があります。

## <a name="assessment-goals"></a>評価の目標

Contoso クラウド チームは、移行評価の目標を設定しました。

- 移行しても、Azure のアプリは、現在のオンプレミス VMWare 環境と同じパフォーマンスを発揮できる必要があります。  クラウドに移行したからといって、アプリのパフォーマンスの重要性が低下する訳ではありません。
- Contoso は自社のアプリケーションおよびデータベースと Azure の要件、および Azure のホスティング オプションとの互換性について理解する必要があります。
- アプリをクラウドに移動した後で、Contoso のデータベース管理を最小化する必要があります。  
- Contoso は移行オプションだけでなく、インフラストラクチャをクラウドに移動した後のインフラストラクチャに関連するコストも理解する必要があります。

## <a name="assessment-tools"></a>評価用ツール
Contoso では、Microsoft の評価用ツールを使用しています。 これらのツールは目標に合ったものであり、必要なすべての情報を提供します。

**テクノロジ** | **説明** | **コスト**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Contoso では DMA を使用して、Azure でのデータベースの機能に影響を与える可能性のある互換性の問題を評価し、検出します。 DMA は SQL のソースとターゲット間で機能パリティを評価し、パフォーマンスと信頼性の向上箇所を推奨します。 | これは無料でダウンロードできるツールです。
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso では、このサービスを使用して VMware VM を評価します。 マシンの移行適合性を評価し、Azure で実行する場合のサイズ設定とコストの見積もりを提供します。  | 現時点では (2018 年 5 月)、このサービスは無料で使用できます。
[サービス マップ](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate では、移行したいマシン間の依存関係を示すために Service Map が使用されます。 |  Service Map は Azure Log Analytics に含まれています。 これは現在、180 日間無料で使用できます。

このシナリオでは、Contoso は DMA をダウンロードして実行し、旅行アプリ用のオンプレミス SQL Server データベースを評価します。 Azure に移行する前に、Azure Migrate と依存関係マッピングを使用してアプリ VM を評価します。



## <a name="assessment-architecture"></a>評価のアーキテクチャ


![移行評価のアーキテクチャ](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso は一般的なエンタープライズ組織を表す架空の名前です。
- Contoso にはオンプレミスのデータセンター (**contoso-datacenter**) があり、そこにオンプレミスのドメイン コントローラー (CONTOSODC1、CONTOSODC2) が含まれています。
- VMware VM は、バージョン 6.5 を実行する VMware ESXI ホスト上にあります。 ホスト: **contosohost1**、**contosohost2**
- VMware 環境は、VM で実行中の vCenter サーバー 6.5 (**venter**) によって管理されます。
- SmartHotel 旅行アプリ:
    - アプリは 2 つの VMware VM (**WEBVM** および **SQLVM**) に階層化されています。
    - VM は、VMware ESXi ホスト **contosohost1.contoso.com** 上にあります。
    - VM は Windows Server 2008 R2 Datacenter SP1 を実行しています。
- VMware 環境は、VM で実行中の vCenter Server (**vcenter.contoso.com**) によって管理されます。
- OSTicket サービス デスク アプリ:
    - アプリは 2 つの VM (**OSTICKETWEB** および **OSTICKETMYSQL**) に階層化されています。
    - VM は Ubuntu Linux Server 16.04-LTS 上で実行されています。
    - OSTICKETWEB VM では Apache 2 および PHP 7.0 が実行されています。
    - OSTICKETMYSQL VM では MySQL 5.7.22 が実行されています。

![アーキテクチャ](./media/contoso-migration-assessment/architecture.png)


## <a name="prerequisites"></a>前提条件

評価で Contoso (およびお客様) に必要なことを以下に示します。

- Azure サブスクリプションまたは Azure サブスクリプション内のリソース グループに対する所有者アクセスまたは共同作成者アクセス。
- バージョン 5.5、6.0、または 6.5 を実行しているオンプレミスの vCenter サーバー。
- vCenter サーバーの読み取り専用アカウント、またはそれを作成するためのアクセス許可。
- .OVA テンプレートを使用して vCenter サーバー上に VM を作成するためのアクセス許可。
- バージョン 5.0 以上を実行している少なくとも 1 つの ESXi ホスト。
- 少なくとも 2 つのオンプレミス VMware VM (1 つは SQL Server データベースを実行)。
- Azure Migrate エージェントを各 VM にインストールするためのアクセス許可。
- VM には、インターネットへの直接接続が必要です。
        - [必要な URL](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites) へのインターネット アクセスを制限できます。
        - マシンにインターネット接続がない場合は、マシンに [OMS ゲートウェイ](../log-analytics/log-analytics-oms-gateway.md)をインストールする必要があります。
- SQL Server インスタンスを実行している VM の FQDN (データベース評価用)。
- SQL Server VM 上で実行されている Windows ファイアウォールでは、DMA が接続できるよう、TCP ポート 1433 (既定値) で外部接続が許可される必要があります。


## <a name="assessment-overview"></a>評価の概要

Contoso で評価を行う方法を以下に示します。


> [!div class="checklist"]
> * **手順 1: DMA をダウンロードしてインストールする**。オンプレミスの SQL Server データベースを評価するために DMA を準備します。
> * **手順 2: DMA を使用してデータベースを評価する**。データベース評価を実行して分析します。
> * **手順 3: Azure Migrate による VM 評価の準備をする**。オンプレミスのアカウントを設定し、VMware 設定を微調整します。
> * **手順 4: Azure Migrate を使用してオンプレミスの VM を検出する**。Azure Migrate コレクター VM を作成します。 次に、コレクターを実行して、評価する VM を検出します。
> * **手順 5: Azure Migrate による依存関係の分析の準備をする**。VM 間の依存関係マッピングを確認できるよう、Azure Migrate エージェントを VM にインストールします。
> * **手順 6: Azure Migrate を使用して VM を評価する**。依存関係を確認して VM をグループ化し、評価を実行します。 評価の準備ができたら、移行に向けてそれを分析します。


## <a name="step-1-download-and-install-the-dma"></a>手順 1: DMA をダウンロードしてインストールする

1. Contoso は [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=53595)から DMA をダウンロードします。
    - Assistant は、SQL インスタンスに接続可能なマシンであればインストールできます。 SQL Server マシンでこれを実行する必要はありません。
    - SQL Server ホスト マシンで実行しないようにしてください。
2. ダウンロードしたセットアップ ファイル (DownloadMigrationAssistant.msi) を実行して、インストールを開始します。
3. **[完了]** ページで、ウィザードを終了する前に **[Microsoft Data Migration Assistant の起動]** を選択します。

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel"></a>手順 2: SmartHotel のデータベース評価を実行して分析する

これで Contoso は評価を実行して、SmartHotel アプリ用のオンプレミス SQL Server を分析することができます。

1. Database Migration Assistant で、**[新規]** をクリックし、**[評価]** を選択して、評価に「**SmartHotel**」というプロジェクト名を付けます。
2. **[ソース サーバーの種類]** で **[Azure Virtual Machines 上の SQL Server]** を選択します。

    ![ソースの選択](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      現時点の DMA では、SQL マネージド インスタンスへの移行の評価はサポートされていません。 この問題を回避するために、Contoso では Azure VM 上の SQL Server を想定されるターゲットとして評価に使用します。

3. **[ターゲット バージョンを選択する]** で、SQL Server 2017 をターゲット バージョンとして選択します。 これは SQL マネージド インスタンスによって使用されるバージョンなので、これを選択する必要があります。
4. 互換性と新機能に関する情報の検出を選択します。
    - **[互換性の問題]** では、移行を中断させる可能性がある変更や、移行前に微調整が必要な変更についてわかります。 非推奨になった機能で現在使用中のものに関する情報を常に把握しておくことができます。 問題は、互換性レベルごとに整理されます。
    - **[新機能のお勧め]** では、移行後にデータベースで使用できる、ターゲット SQL Server プラットフォームの新機能についてわかります。 これらは、パフォーマンス、セキュリティ、ストレージごとに整理されます。

    ![ターゲット デバイスの選択](./media/contoso-migration-assessment/dma-assessment-2.png)

2. **[サーバーへの接続]** で、データベースを実行している VM の名前と、それにアクセスするための資格情報を入力します。 **[サーバー証明書を信頼する]** を有効にして、SQL Server に接続できるようにする必要があります。 次いで **[接続]** をクリックします。

    ![ターゲット デバイスの選択](./media/contoso-migration-assessment/dma-assessment-3.png)

3. **変換元の追加** で、評価対象のデータベースを追加し、**次へ)(** をクリックして評価を開始します。
4. 評価が作成されます。

    ![評価を作成する](./media/contoso-migration-assessment/dma-assessment-4.png)

5. **[結果の確認]** で、評価結果を確認できます。


### <a name="analyze-the-database-assessment"></a>データベース評価を分析する

結果は、利用可能になるとすぐに表示されます。 問題を修正する場合は、**[評価の再開]** をクリックして評価を再実行する必要があります。

1. **[互換性の問題]** レポートで、互換性の各レベルで問題をチェックします。 互換性レベルでは SQL Server バージョンが次のようにマップされます。

    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![互換性の問題](./media/contoso-migration-assessment/dma-assessment-5.png)

2. **[機能に関する推奨事項]** レポートでは、移行後に評価によって推奨されるパフォーマンス、セキュリティ、ストレージの機能を表示できます。 インメモリ OLTP と列ストア、Stretch Database、Always Encrypted、動的データ マスク、Transparent Data Encryption (TDE) など、さまざまな機能が推奨されます。

    ![機能に関する推奨事項](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > Contoso がすべての SQL Server データベースで [TDE を有効にする](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017)ことを推奨します。データベースがクラウドにある場合、これはさらに重要です。 TDE は移行が終わるまで有効にしないでください。 TDE が既に有効になっている場合は、証明書または非対称キーを対象サーバーのマスター データベースに移動する必要があります。 [詳細情報](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)。

2. 評価は JSON または CSV 形式でエクスポートできます。

より大規模に評価を実行する場合は、次のことができます。

- 複数の評価を同時に実行し、**[すべての評価]** ページを開いて評価の状態を表示する。
- [評価を SQL Server データベースに統合する](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database)。
- [評価を PowerBI レポートに統合する](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017)。


## <a name="step-3-prepare-for-vm-assessment-with-azure-migrate"></a>手順 3: Azure Migrate による VM 評価の準備をする

Contoso は、評価対象の VM を自動で検出するために Azure Migrate によって使用される VMware アカウントを作成する必要があります。次に、VM を作成するためのアクセス許可を確認します。さらに、開く必要があるポートを書き留め、統計情報設定レベルを設定します。

### <a name="set-up-a-vmware-account"></a>VMware アカウントを設定する

 VM の検出には、次のプロパティを持つ、vCenter の読み取り専用アカウントが必要です。

- ユーザーの種類: 読み取り専用以上。
- アクセス許可: データ センター オブジェクト –> 子オブジェクトへの伝達、ロール=読み取り専用。
- 詳細: ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。
- アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。

### <a name="verify-permissions-to-create-a-vm"></a>VM を作成するためのアクセス許可を確認する

Contoso は、.OVA 形式でファイルをインポートして、VM を作成するためのアクセス許可があることを確認します。 [詳細情報](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1)。

### <a name="verify-ports"></a>ポートを確認する

Contoso の評価では、依存関係のマッピングを使用します。 この機能を使用するには、評価したい VM にエージェントをインストールする必要があります。 このエージェントは、各 VM の TCP ポート 443 から Azure に接続できる必要があります。 接続要件の詳細については、[こちら](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid)をご覧ください。


### <a name="set-statistics-settings"></a>統計情報設定を行う

Contoso は、デプロイを開始する前に vCenter Server の統計情報設定をレベル 3 に設定する必要があります。 以下の点に注意してください。

- レベルを設定した後、少なくとも 1 日経ってから評価を実行する必要があります。 そうしないと、想定どおりに機能しない可能性があります。
- 3 より高いレベルにした場合、評価は機能します。ただし:
    - ディスクとネットワークのパフォーマンス データが収集されません。
    - ストレージに関して、オンプレミス ディスクとサイズが同じ Azure の Standard ディスクが Azure Migrate によって推奨されます。
    - ネットワークに関して、各オンプレミス ネットワーク アダプターごとに Azure のネットワーク アダプターが推奨されます。
    - コンピューティングに関して、Azure Migrate によって VM コアとメモリ サイズが調査され、同じ構成の Azure VM が推奨されます。 条件を満たす Azure VM サイズが複数ある場合、最も低コストのディスクをお勧めします。
- レベル 3 でのサイズ設定の詳細については、[こちら](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)をご覧ください。

レベルは次のように設定します。

1. vSphere Web Client で、vCenter サーバー インスタンスを開きます。
2. **[管理]** > **[設定]** > **[全般]** で、**[編集]** をクリックします。
3. **[統計]** で、統計情報レベル設定を **[レベル 3]** に設定します。

    ![vCenter の統計情報レベル](./media/contoso-migration-assessment/vcenter-statistics-level.png)



## <a name="step-4-discover-vms"></a>手順 4: VM を検出する

Contoso が VM を探索するには、Azure Migrate プロジェクトを作成します。 コレクター VM をダウンロードおよび設定し、コレクターを実行してオンプレミスの VM を検出します。

### <a name="create-a-project"></a>プロジェクトの作成

1. [Azure portal](https://portal.azure.com) で、**Azure Migrate** を検索して、プロジェクト (ContosoMigration) を作成します。
2. プロジェクト名、Azure サブスクリプションを指定し、新しい Azure リソース グループ、**ContosoFailoverRG** を作成します。 以下の点に注意してください。

    - Azure Migrate プロジェクトを作成できるのは、米国中西部または米国東部リージョンに限られます。
    - 移行は、任意のターゲットの場所で計画できます。
    - プロジェクトの場所は、オンプレミスの VM から収集されたメタデータを格納するためにのみ使用されます。

    ![Azure Migrate](./media/contoso-migration-assessment/project-1.png)


### <a name="download-the-collector-appliance"></a>コレクター アプライアンスをダウンロードする

Azure Migrate は、コレクター アプライアンスと呼ばれるオンプレミスの VM を作成します。 この VM はオンプレミスの VMware VM を検出し、それについてのメタデータを Azure Migrate サービスに送信します。 Contoso がコレクター アプライアンスをセットアップするには、.OVA テンプレートをダウンロードしてオンプレミスの vCenter サーバーにインポートし、VM を作成します。

1. Azure Migrate プロジェクトで **[開始]** > **[検出と評価]** > **[マシンの検出]** の順に選択し、.OVA テンプレート ファイルをダウンロードします。
2. プロジェクト ID とキーをコピーします。 これらはコレクターの構成に必要です。

    ![.ova ファイルをダウンロードする](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>コレクター アプライアンスを確認する

Contoso は、VM をデプロイする前に .OVA ファイルが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、OVA のハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 生成されたハッシュは、次の設定と一致する必要があります (バージョン 1.0.9.12)

**アルゴリズム** | **ハッシュ値**
--- | ---
MD5 | d0363e5d1b377a8eb08843cf034ac28a
SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

### <a name="create-the-collector-appliance"></a>コレクター アプライアンスを作成する

これで、Contoso はダウンロードしたファイルを vCenter Server にインポートして、構成サーバーの VM をプロビジョニングできます。

1. vSphere Client コンソールで、**[ファイル]** > **[OVF テンプレートのデプロイ]** の順にクリックします。

    ![OVF をデプロイする](./media/contoso-migration-assessment/vcenter-wizard.png)

2. [OVF テンプレートのデプロイ] ウィザードで **[ソース]** を選択し、.OVA ファイルの場所を指定します。
3. **[名前] と [場所]** で、コレクター VM のフレンドリ名と、VM がホストされるインベントリの場所を指定します。 コレクター アプライアンスが実行されるホストまたはクラスターも指定します。
5. **[ストレージ]** でストレージの場所を指定し、**[ディスク フォーマット]** でストレージをプロビジョニングする方法を指定します。
7. **[ネットワーク マッピング]** で、コレクター VM の接続先となるネットワークを指定します。 このネットワークには、Azure にメタデータを送信するためのインターネット接続が必要です。
8. 設定を確認し、**[デプロイ後に電源オン]**> **[完了]** の順に選択します。 アプライアンスの作成後、正常に完了したことを確認するメッセージが発行されます。

### <a name="run-the-collector-to-discover-vms"></a>コレクターを実行して VM を検出する

ここで、コレクターを実行して VM を検出します。 現時点でオペレーティング システムの言語およびコレクター インターフェイスの言語としてコレクターでサポートされるのは、"英語 (米国)" のみであることに注意してください。

1. vSphere Client コンソールで **[コンソールを開く]** を選択し、コレクター VM の言語、タイム ゾーン、およびパスワード設定を指定します。
2. デスクトップにある **[コレクターの実行]** ショートカットをクリックします。

    ![コレクターのショートカット](./media/contoso-migration-assessment/collector-shortcut.png)

4. Azure Migrate Collector で **[前提条件の設定]** を選択して、ライセンス条項に同意し、サード パーティの情報を確認します。
5. コレクターによって、VM にインターネット アクセスがあること、時間が同期されたこと、(既定で VM にインストールされる) コレクター サービスが実行されていることがチェックされます。 さらに、VMware PowerCLI がインストールされます。

    > [!NOTE]
    > VM に、プロキシを使用しないインターネットへの直接アクセスがあると仮定しています。

    ![前提条件を確認する](./media/contoso-migration-assessment/collector-verify-prereqs.png)


5. **[vCenter Server の詳細を指定する]** で、vCenter サーバーの名前 (FQDN) または IP アドレス、および検出に使用する読み取り専用の資格情報を指定します。
7. VM 検出のスコープを選択します。 コレクターは、指定されたスコープ内の VM のみを検出できます。 スコープは、指定のフォルダー、データセンター、またはクラスターに設定できます。 VM の数は 1,500 台を超えないようにします。

    ![vCenter に接続する](./media/contoso-migration-assessment/collector-connect-vcenter.png)

6. **[移行プロジェクトの指定]** で、ポータルからコピーされた Azure Migrate プロジェクトの ID とキーを指定します。 この ID とキーを再度取得するには、プロジェクトで **[概要]** ページ > **[マシンの検出]** と進みます。  

    ![Azure への接続](./media/contoso-migration-assessment/collector-connect-azure.png)

7. **[コレクションの進行状況を表示します]** で、Contoso は検出を監視し、VM から収集されたメタデータがスコープ内にあることを確認できます。 コレクターがおおよその検出時間を表示します。

    ![進行中の収集](./media/contoso-migration-assessment/collector-collection-process.png)



### <a name="verify-vms-in-the-portal"></a>ポータル内での VM の特定

収集の完了後、Contoso はポータルに表示された VM を確認します。

1. Azure Migrate プロジェクトで **[管理]** > **[マシン]** と進み、Contoso はお客様が検出したい VM が表示されることを確認します。

    ![検出されたマシン](./media/contoso-migration-assessment/discovery-complete.png)

3. 現時点では、マシンに Azure Migrate エージェントがインストールされていないことに注意してください。 依存関係を表示するには、Contoso はエージェントをインストールする必要があります。

    ![検出されたマシン](./media/contoso-migration-assessment/machines-no-agent.png)



## <a name="step-5-prepare-for-dependency-analysis"></a>手順 5: 依存関係の分析の準備をする

Contoso が評価対象の VM 間の依存関係を表示するには、エージェントをダウンロードしてアプリ VM にインストールします。 Contoso は、Windows と Linux の両方で、アプリのすべての VM でこれを行います。

### <a name="take-a-snapshot"></a>スナップショットを取得する

エージェントをインストールする前にスナップショットを取得することで、VM を変更する前に VM のコピーを保管しておきます。

![マシンのスナップショット](./media/contoso-migration-assessment/snapshot-vm.png)


### <a name="download-and-install-the-vm-agents"></a>VM エージェントをダウンロードしてインストールする

1. **[マシン]** ページでマシンを選択し、**[依存関係]** 列の **[インストールする必要があります]** を選択します。
2. **[マシンの検出]** ページで、以下の操作を行います。
    - Windows VM ごとに MMA と依存関係のエージェントをダウンロードする
    - Linux VM ごとに MMA と依存関係のエージェントをダウンロードする
3. ここで、ワークスペース ID とキーをコピーします。 この ID とキーは MMA をインストールするときに必要です。

    ![エージェントのダウンロード](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Windows VM にエージェントをインストールする

VM ごとにインストールを実行します。

#### <a name="install-the-mma-on-windows-vms"></a>Windows VM に MMA をインストールする

1. ダウンロードしたエージェントをダブルクリックします。
2. **[インストール先のフォルダー]** で、既定のインストール フォルダーをそのまま使用して、**[次へ]** をクリックします。
2. **[エージェントのセットアップ オプション]** で、**[Azure Log Analytics にエージェントを接続する]** > **[次へ]** の順にクリックします。

    ![MMA のインストール](./media/contoso-migration-assessment/mma-install.png)

5. **[Azure Log Analytics]** で、ポータルからコピーしたワークスペース ID とキーを貼り付けます。

    ![MMA のインストール](./media/contoso-migration-assessment/mma-install2.png)

6. **[インストールの準備完了]** で、MMA をインストールできるようになりました。

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Windows に依存関係エージェントをインストールする

1. ダウンロードした依存関係エージェントをダブルクリックします。
2. ライセンス条項に同意し、インストールが完了するまで待ちます。

    ![依存関係エージェント](./media/contoso-migration-assessment/dependency-agent.png)


### <a name="install-the-agents-on-linux-vms"></a>Linux VM にエージェントをインストールする

VM ごとにインストールを実行します。

#### <a name="install-the-mma-on-linux-vms"></a>Linux VM に MMA をインストールする

1. **sudo apt-get install python-ctypeslib** を使用して、VM ごとに Python の ctypes ライブラリをインストールします。
2. ルートとしてコマンドを実行して MMA エージェントをインストールする必要があります。  ルートになるには、コマンド **sudo-i** を実行し、ルート パスワードを入力します。
3. ここで、MMA エージェントをインストールします。
    - コマンドに正しいワークスペース ID とキーを挿入します。
    - コマンドは 64 ビット用です。
    - **ワークスペース ID** と**プライマリ キー**は OMS ポータルの **[設定]** の **[接続されたソース]** タブで確認できます。
    - OMS エージェントをダウンロードしてチェックサムを検証し、エージェントをインストールして利用できる状態にするためには、以下のコマンドを実行します。

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```



#### <a name="install-the-dependency-agent-on-linux-vms"></a>Linux VM に依存関係エージェントをインストールする

MMA をインストールした後、Contoso は Linux VM に依存関係エージェントをインストールすることができます。

1. Dependency Agent は、InstallDependencyAgent-Linux64.bin (自己解凍バイナリ ファイルを含むシェル スクリプト) を使用して Linux コンピューターにインストールされます。 sh を使用してファイルを実行するか、実行アクセス許可をファイル自体に追加します。

2. ルートとして Linux の依存関係エージェントをインストールします。

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```


## <a name="step-6-run-and-analyze-the-vm-assessment"></a>手順 6: VM 評価を実行して分析する

これで、Contoso はマシンの依存関係を確認し、グループを作成することができます。 次いで、そのグループの評価を実行します。

### <a name="verify-dependencies-and-create-a-group"></a>依存関係を確認し、グループを作成する


1. 分析するマシンで、**[依存関係の表示]** をクリックします。

    ![マシンの依存関係の表示](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. 依存関係マップでは、SQLVM について次の詳細が表示されます。

    - 指定した期間中 (既定では 1 時間) に SQLVM で実行される、アクティブなネットワーク接続を伴うプロセス グループ/プロセス
    - 依存関係にあるすべてのマシン間の受信 (クライアント) および送信 (サーバー) TCP 接続。
    - Azure Migrate エージェントがインストールされた依存関係にあるマシンは、別々のボックスに表示されます
    - エージェントがインストールされていないマシンには、ポートと IP アドレスの情報が表示されます。

3. エージェントがインストールされたマシン (WEBVM) について、マシンのボックスをクリックし、FQDN、オペレーティング システム、MAC アドレスなどの詳しい情報を表示します。

    ![グループの依存関係の表示](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. 次に、グループに追加する VM (SQLVM と WEBVM) を選択します。  Ctrl キーを押しながらクリックすると、複数の VM を選択できます。
5. **[グループの作成]** をクリックし、名前 (smarthotelapp) を指定します。

> [!NOTE]
    > さらに細かい依存関係を表示するために、時間の範囲を拡張できます。 特定の期間、つまり開始日と終了日を選択できます。


### <a name="run-an-assessment"></a>評価を実行する


1. **[グループ]**  ページで、グループ (smarthotelapp) を開いて、**[評価の作成]** をクリックします。

    ![評価を作成する](./media/contoso-migration-assessment/run-vm-assessment.png)

2. 評価が、**[管理]** > **[評価]** ページに表示されます。

Contoso では既定の評価の設定を使用しましたが、設定はカスタマイズできます。 [詳細情報](how-to-modify-assessment.md)。



### <a name="analyze-the-vm-assessment"></a>VM 評価を分析する

Azure Migrate 評価には、オンプレミスの VM と Azure の互換性、推奨される適切な Azure VM サイズ設定、推定月間 Azure コストに関する情報が含まれています。

![評価レポート](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>信頼度レーティングを確認する

![評価の表示](./media/contoso-migration-assessment/assessment-display.png)

評価には、1 つ星から 5 つ星 (1 つ星が最低で 5 つ星が最高) までの信頼度レーティングが付いています。
- 信頼度レーティングは、評価の計算に必要なデータ ポイントの可用性に基づいて、評価に割り当てられます。
- レーティングは、Azure Migrate による推奨サイズの信頼性を見積もるために役立ちます。
- 信頼度レーティングは、"*パフォーマンス ベースのサイズ変更*" を実施する際に役立てることができます。使用率ベースのサイズ変更を実施できるだけのデータ ポイントが Azure Migrate にはない場合があるためです。 "*オンプレミスと同じサイズ*" の場合、Azure Migrate には VM のサイズ変更に必要なすべてのデータ ポイントがあるため、信頼度レーティングは常に 5 つ星になります。
- 次のように、使用可能なデータ ポイントの割合に応じて、評価の信頼度レーティングが決まります。

   **データ ポイントの可用性** | **信頼度レーティング**
   --- | ---
   0% - 20% | 1 つ星
   21% - 40% | 2 つ星
   41% - 60% | 3 つ星
   61% - 80% | 4 つ星
   81% - 100% | 5 つ星

#### <a name="verify-readiness"></a>準備状態を確認する

![アセスメントの準備状態](./media/contoso-migration-assessment/azure-readiness.png)  

評価レポートには、情報が表でまとめられて表示されます。 パフォーマンス ベースのサイズ設定を表示するには、Azure Migrate に次の情報が必要であることに注意してください。 この情報を収集できない場合、サイズ設定評価は正確でない可能性があります。

- CPU とメモリの使用率データ。
- VM にアタッチされた各ディスクの読み取り/書き込み IOPS とスループット。
- VM にアタッチされた各ネットワーク アダプターのネットワーク受信/送信情報。


**設定** | **指示内容** | **詳細**
--- | --- | ---
**Azure VM 対応性** | VM が移行できる状態であるかどうかを示します | 次の状態があります。</br><br/>- Azure に対応<br/><br/>- 条件付きで対応 <br/><br/>- Azure に未対応<br/><br/>- 対応不明<br/><br/> VM の準備が整っていない場合、修正手順が表示されます。
**Azure VM サイズ** | 準備が完了している VM について、Azure VM サイズが推奨されます。 | サイズ設定の推奨事項は、評価のプロパティによって異なります。<br/><br/>- パフォーマンス ベースのサイズ設定を使用した場合、サイズ設定では VM のパフォーマンス履歴が考慮されます。<br/><br/>- "オンプレミス" を使用した場合、サイズ設定はオンプレミスの VM サイズに基づき、使用率データは使われません。
**推奨されるツール** | マシンではエージェントが実行されています。そのため、マシン内で実行されているプロセスが Azure Migrate によって確認され、そのマシンがデータベース マシンであるかどうかが特定されます。
**VM 情報** | レポートには、オペレーティング システム、ブートの種類、ディスクとストレージの情報など、オンプレミス VM の設定が表示されます。


#### <a name="review-monthly-cost-estimates"></a>推定月間コストを確認する

このビューには、Azure で VM を実行する際のコンピューティング コストおよびストレージ コストの合計と、各マシンの詳細が表示されます。

![アセスメントの準備状態](./media/contoso-migration-assessment/azure-costs.png)

- コストの見積もりは、マシンの推奨サイズを使って計算されます。
- コンピューティングとストレージの見積もり月額コストが、グループ内の全 VM について集計されます。


## <a name="clean-up-after-assessment"></a>評価後のクリーンアップ

- 評価の終了後、Contoso では今後の評価のために Azure Migration のアプライアンスを保持します。
- VM の VMware をオフにします。 追加の VM を評価するときに再度起動します。
- Contoso の移行プロジェクトを Azure に保管します。  現在、東部米国の Azure リージョンにある ContosoFailoverRG リソース グループにデプロイされています。
-  コレクター VM には 180 日間の評価版ライセンスがあります。 この制限の有効期限が切れた場合は、コレクターをダウンロードして再度設定する必要があります。


## <a name="conclusion"></a>まとめ

このシナリオでは、Contoso は DMA ツールを使用して SmartHotel アプリ データベースを評価し、Azure Migrate サービスを使用してオンプレミスの VM を評価しました。 次いで、オンプレミス リソースを Azure に移行する準備を整えるため、評価を確認しました。

## <a name="next-steps"></a>次の手順

このシリーズの次の記事では、Contoso はリフトアンドシフトの移行によって SmartHotel アプリを Azure でリホストします。 Contoso は Azure Site Recovery を使用してアプリのフロントエンド WEBVM を移行し、Database Migration Service を使用してアプリ データベースを Azure SQL Managed Instance に移行します。 このデプロイで[今すぐ開始](contoso-migration-rehost-vm-sql-managed-instance.md)。
