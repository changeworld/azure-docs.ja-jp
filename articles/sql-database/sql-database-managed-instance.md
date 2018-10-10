---
title: Azure SQL Database Managed Instance の概要 | Microsoft Docs
description: このトピックでは、Azure SQL Database マネージド インスタンスについて説明し、さらにそのしくみと Azure SQL Database の単一データベースとの相違点についても説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 09/26/2018
ms.openlocfilehash: 626dd362248027831c78d1505662ca12d2ff334d
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392824"
---
# <a name="what-is-a-managed-instance"></a>Managed Instance とは

Azure SQL Database Managed Instance は、Azure SQL Database の新しいデプロイ モデルです。最新の SQL Server オンプレミス (Enterprise Edition) Database Engine との 100% 近い互換性を備えています。また、セキュリティに関する一般的な懸念事項に対処するネイティブの[仮想ネットワーク](../virtual-network/virtual-networks-overview.md) (VNet) 実装を提供し、オンプレミスの SQL Server の顧客に有利な[ビジネス モデル](https://azure.microsoft.com/pricing/details/sql-database/)を提供します。 マネージド インスタンスにより、既存の SQL Server の顧客は最小限のアプリケーションおよびデータベース変更のみで、オンプレミスのアプリケーションをクラウドに移行 (リフト アンド シフト) することができます。 これと同時に、Managed Instance では、管理のオーバーヘッドと TCO を大幅に削減するすべての PaaS 機能 (自動的な修正プログラムの適用およびバージョン更新、[自動バックアップ](sql-database-automated-backups.md)、[高可用性](sql-database-high-availability.md)) を維持します。

> [!IMPORTANT]
> 現在マネージド インスタンスが提供されているリージョンの一覧については、「[Migrate your databases to a fully managed service with Azure SQL Database Managed Instance (Azure SQL Database マネージド インスタンスを使用してデータベースを完全管理型サービスに移行する)](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/)」を参照してください。
 
次の図では、マネージド インスタンスの主な機能について概説します。

![主な機能](./media/sql-database-managed-instance/key-features.png)

Azure SQL Database Managed Instance は、多数のアプリをオンプレミスまたは IaaS、自作、あるいは ISV 提供の環境から完全なマネージド PaaS クラウド環境に、できるだけ手間をかけずに移行しようとしているユーザー用に設計されています。 Azure で完全に自動化された[データ移行サービス (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) を使用することにより、顧客はオンプレミスの SQL Server をマネージド インスタンスに移行 (リフト アンド シフト) することができます。この場合、VNET のネイティブ サポートにより、SQL Server オンプレミスとの互換性の維持、顧客のインスタンスの完全な分離が実現されます。  ソフトウェア アシュアランスに基づき、SQL Database マネージド インスタンスでは [SQL Server 用の Azure ハイブリッド使用特典](../virtual-machines/windows/hybrid-use-benefit-licensing.md)を利用して、顧客の既存のライセンスを割引料金のライセンスに交換することができます。  高度なセキュリティと豊富なプログラミング サーフェスを必要とする SQL Server インスタンスにとって、SQL Database Managed Instance はクラウド内の最適な移行先です。 

マネージド インスタンスでは一般提供までに段階的リリース計画に基づいて、最新のオンプレミスの SQL Server バージョンとの 100% 近いセキュリティ、外部からのアクセス互換性を実現することを目的としています。 

Azure SQL Database Single Database、Azure SQL Database Managed Instance、仮想マシンでホストされる SQL Server IaaS のいずれにするかを決定するには、[Azure クラウドで適切なバージョンの SQL Server を選択する方法](sql-database-paas-vs-sql-server-iaas.md)に関するページを参照してください。

## <a name="key-features-and-capabilities"></a>主な機能 

Azure SQL Database Managed Instance は、Azure SQL Database と SQL Server Database Engine の両方で利用可能な最高の機能を組み合わせています。

> [!IMPORTANT]
> マネージド インスタンスは、オンライン操作、計画の自動修正、その他のエンタープライズ パフォーマンス拡張機能など、最新バージョンの SQL Server の機能すべてを実行します。 

| **PaaS の特典** | **ビジネス継続性** |
| --- | --- |
|ハードウェアの購入と管理が不要 <br>基になるインフラストラクチャを管理するための管理オーバーヘッドが不要 <br>迅速なプロビジョニングとサービスのスケーリング <br>自動的に行われる修正プログラムの適用とバージョンのアップグレード <br>その他の PaaS データ サービスとの統合 |99.99% アップタイム SLA  <br>組み込みの[高可用性](sql-database-high-availability.md) <br>[自動バックアップ](sql-database-automated-backups.md)によるデータの保護 <br>バックアップのリテンション期間を顧客が構成可能 (パブリック プレビューでは 7 日間に固定) <br>ユーザーによって開始される[バックアップ](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[データベースのポイントインタイム リストア](sql-database-recovery-using-backups.md#point-in-time-restore)機能 |
|**セキュリティとコンプライアンス** | **管理**|
|分離環境 ([VNet 統合](sql-database-managed-instance-vnet-configuration.md)、シングル テナント サービス、専用のコンピューティングおよびストレージ) <br>[透過的なデータ暗号化 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure AD 認証](sql-database-aad-authentication.md)、シングル サインオンのサポート <br>Azure SQL Database と同じコンプライアンス標準に準拠 <br>[SQL 監査](sql-database-managed-instance-auditing.md) <br>[脅威の検出](sql-database-managed-instance-threat-detection.md) |サービスのプロビジョニングとスケーリングを自動化するための Azure Resource Manager API <br>手動でのサービスのプロビジョニングとスケーリングに対応する Azure Portal の機能 <br>データ移行サービス 

## <a name="vcore-based-purchasing-model"></a>仮想コアベースの購入モデル

[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)では、柔軟性、管理性、透明性が実現されており、オンプレミスのワークロード要件をクラウドに容易に移行できます。 このモデルでは、ワークロードの必要性に基づいて、コンピューティング、メモリ、ストレージをスケーリングできます。 また、仮想コア モデルは [SQL Server 用の Azure ハイブリッド使用特典](../virtual-machines/windows/hybrid-use-benefit-licensing.md)による最大 30% の割引の対象となります。

仮想コアは、ハードウェアの世代の選択が可能な論理 CPU を表します。
- Gen 4 論理 CPU は、Intel E5-2673 v3 (Haswell) 2.4 GHz のプロセッサを基盤とします。
- Gen 5 論理 CPU は、Intel E5-2673 v4 (Broadwell) 2.3 GHz のプロセッサを基盤とします。

次の表は、コンピューティング、メモリ、ストレージ、I/O リソースの最適な構成を選択する方法を理解するために役立ちます。

||Gen 4|Gen 5|
|----|------|-----|
|ハードウェア|Intel E5-2673 v3 (Haswell) 2.4 GHz プロセッサ、接続されている SSD 仮想コア = 1 PP (物理コア)|Intel E5-2673 v4 (Broadwell) 2.3 GHz プロセッサ、高速 eNVM SSD、仮想コア=1 LP (ハイパー スレッド)|
|コンピューティング サイズ|8、16、24 の仮想コア|8、16、24、32、40、64、80 の仮想コア|
|メモリ|仮想コアあたり 7 GB|仮想コアあたり 5.5 GB|
||||

## <a name="managed-instance-service-tiers"></a>マネージド インスタンスのサービス階層

マネージド インスタンスは 2 つのサービス階層で利用できます。
- **General Purpose**: 一般的なパフォーマンスと IO 待ち時間要件のアプリケーション用に設計されています。
- **Business Critical (プレビュー)**: 短い IO 待ち時間要件で、基盤となるメンテナンス操作がワークロードに与える影響が最小限のアプリケーション用に設計されています。

どちらのサービス階層も 99.99% の可用性を保証し、ストレージ サイズとコンピューティング能力を個別に選択することができます。 Azure SQL Database の高可用性アーキテクチャの詳細については、「[高可用性と Azure SQL Database](sql-database-high-availability.md)」を参照してください。

> [!IMPORTANT]
> 汎用から Business Critical に、またはその逆にサービス レベルを変更することは、パブリック プレビューではサポートされていません。 別のサービス階層のインスタンスにデータベースを移行する場合は、新しいインスタンスを作成し、元のインスタンスからポイントインタイム リストアでデータベースを復元し、必要なくなった場合に元のインスタンスを削除します。 

### <a name="general-purpose-service-tier"></a>汎用のサービス階層

次のリストで、汎用サービス階層の主な特徴を説明します。 

- 標準的なパフォーマンス要件を持つ大多数のビジネス アプリケーション向けの設計 
- 高パフォーマンスの Azure Premium Storage (8 TB) 
- インスタンスあたり 100 個のデータベース 

次のリストで、汎用サービス階層の主な特徴を説明します。

|Feature | 説明|
|---|---|
| vCores* の数 | 8、16、24 (Gen 4)<br>8、16、24、32、40、64、80 (Gen 5)|
| SQL Server のバージョン/ビルド | SQL Server Database Engine (最新の安定版) |
| 最小ストレージ サイズ | 32 GB |
| 最大ストレージ サイズ | 8 TB |
| データベースあたりの最大ストレージ容量 | インスタンスごとの最大ストレージ サイズによって決まります |
| 期待されるストレージ IOPS | データ ファイルあたり 500 から 7500 IOPS (データ ファイルによって異なる)。 [Premium Storage](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes) に関するセクションを参照してください。 |
| データベースごとのデータ ファイル数 (ROWS) | 複数 | 
| データベースごとのログ ファイル数 (LOG) | 1 | 
| 管理される自動バックアップ | [はい] |
| HA | Azure Storage と [Azure Service Fabric](../service-fabric/service-fabric-overview.md) に格納されたデータ |
| インスタンスおよびデータベースに対する組み込みの監視とメトリック | [はい] |
| 自動的に行われるソフトウェア修正プログラムの適用 | [はい] |
| VNet - Azure Resource Manager デプロイ | [はい] |
| VNet - クラシック デプロイ モデル | いいえ  |
| ポータルのサポート | [はい]|
|||

\* 仮想コアは、ハードウェアの世代の選択が可能な論理 CPU を表します。 Gen 4 論理 CPU は、Intel E5-2673 v3 (Haswell) 2.4 GHz プロセッサをベースにしています。Gen 5 論理 CPU は、Intel E5-2673 v4 (Broadwell) 2.3 GHz プロセッサをベースにしています。 

詳細については、Azure SQL Database の [Standard/General Purpose の可用性とアーキテクチャ](sql-database-high-availability.md#standardgeneral-purpose-availability)に関するページを参照してください。

### <a name="business-critical-service-tier-preview"></a>Business Critical サービス レベル (プレビュー)

Business Critical サービス レベルは、IO 要件の高いアプリケーション向けに構築されます。 分離された複数の Always On レプリカを使用して、最高の耐障害性が提供されます。 

次のリストで、Business Critical サービス レベルの主な特徴を説明します。 
-   最高のパフォーマンスと HA の要件を持つビジネス アプリケーション向けに設計されています。 
-   超高速の SSD ストレージ (Gen 4 で最大 1 TB、Gen 5 で最大 4 TB) が付属します
-   インスタンスあたり最大 100 個のデータベースをサポートしています 
- レポートやその他の読み取り専用ワークロードに使用することができる、組み込みの読み取り専用追加インスタンス
- 高パフォーマンス要件のワークロードに使用することができる[インメモリ OLTP](sql-database-in-memory.md)  

|Feature | 説明|
|---|---|
| vCores* の数 | 8、16、24、32 (Gen 4)<br>8、16、24、32、40、64、80 (Gen 5)|
| SQL Server のバージョン/ビルド | SQL Server (最新版) |
| その他の機能 | [インメモリ OLTP](sql-database-in-memory.md)<br> 1 つの追加の読み取り専用レプリカ ([読み取りスケールアウト](sql-database-read-scale-out.md))
| 最小ストレージ サイズ | 32 GB |
| 最大ストレージ サイズ | Gen 4: 1 TB (すべての仮想コア サイズ)<br> Gen 5:<ul><li>8、16 仮想コアの場合は 1 TB</li><li>24 仮想コアの場合は 2 TB</li><li>32、40、64、80 仮想コアの場合は 4 TB</ul>|
| データベースあたりの最大ストレージ容量 | インスタンスごとの最大ストレージ サイズによって決まります |
| データベースごとのデータ ファイル数 (ROWS) | 複数 | 
| データベースごとのログ ファイル数 (LOG) | 1 | 
| 管理される自動バックアップ | [はい] |
| HA | ローカル SSD に格納され、[Always On 可用性グループ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)と [Azure Service Fabric](../service-fabric/service-fabric-overview.md) を使用するデータ |
| インスタンスおよびデータベースに対する組み込みの監視とメトリック | [はい] |
| 自動的に行われるソフトウェア修正プログラムの適用 | [はい] |
| VNet - Azure Resource Manager デプロイ | [はい] |
| VNet - クラシック デプロイ モデル | いいえ  |
| ポータルのサポート | [はい]|
|||

詳細については、Azure SQL Database の [Premium/Business Critical の可用性とアーキテクチャ](sql-database-high-availability.md#premiumbusiness-critical-availability)に関するページを参照してください。

> [!IMPORTANT]
> **Business Critical** サービス レベルはプレビュー段階です。

## <a name="advanced-security-and-compliance"></a>高度なセキュリティとコンプライアンス 

Azure SQL Database Managed Instance は、Azure クラウドと SQL Server Database Engine によって提供される高度な機能を組み合わせています。 

### <a name="managed-instance-security-isolation-in-azure-cloud"></a>Azure クラウドでの Managed Instance のセキュリティ分離 

マネージド インスタンスでは、Azure のクラウド内での他のテナントからのセキュリティ分離が追加されています。 セキュリティ分離: 

- Azure Express Route または VPN Gateway を使った、オンプレミス環境への[ネイティブ仮想ネットの実装](sql-database-managed-instance-vnet-configuration.md)と接続 
- SQL エンドポイントはプライベート IP アドレスでのみ公開されるため、Azure またはハイブリッド ネットワークから安全に接続できます。
- 専用の基になるインフラストラクチャ (コンピューティング、ストレージ) を備えたシングル テナント

次の図では、アプリケーションのさまざまな接続オプションについて概説します。 

![高可用性](./media/sql-database-managed-instance/application-deployment-topologies.png)  

サブネット レベルでの VNet 統合およびネットワーク ポリシーの適用の詳細については、「[Azure SQL Database Managed Instance の VNet を構成する](sql-database-managed-instance-vnet-configuration.md)」と「[Azure SQL Database Managed Instance にアプリケーションを接続する](sql-database-managed-instance-connect-app.md)」を参照してください。 

> [!IMPORTANT]
> 複数のマネージド インスタンスを同じサブネットに配置すると追加のメリットが得られるので、セキュリティ要件で許可されている場合は常にそのように配置します。 同じサブネット内に複数のインスタンスを配置すると、ネットワーク インフラストラクチャのメンテナンスが大幅に簡素化され、インスタンスのプロビジョニング時間が短縮されます。これは、長いプロビジョニング時間が、サブネット内の最初のマネージド インスタンスをデプロイするコストと関連性があるためです。

### <a name="azure-sql-database-security-features"></a>Azure SQL Database のセキュリティ機能

Azure SQL Database は、データを保護するために使用できる一連の高度なセキュリティ機能を提供します。

- [Managed Instance の監査](sql-database-managed-instance-auditing.md)では、データベース イベントを追跡し、Azure Storage アカウント内の監査ログ ファイルにイベントを書き込みます。 監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。 
- 移動中のデータの暗号化 - Managed Instance では、トランスポート層セキュリティを使用して、移動中のデータの暗号化を実現することにより、データをセキュリティで保護します。 トランスポート層セキュリティに加えて、SQL Database Managed Instance では [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) を使用して、転送中、保存中、およびクエリの処理中に機密データを保護します。 Always Encrypted は、重要なデータの盗難を含む侵害に対する、これまでにないデータ セキュリティを提供する業界初の機能です。 たとえば、クレジット カード番号は、Always Encrypted によって常に暗号化されてデータベースに格納されます。暗号化はクエリ処理中も行われます。復号化は、データを処理する必要がある承認されたスタッフまたはアプリケーションが使用するときに実行することができます。 
- [脅威の検出](sql-database-managed-instance-threat-detection.md)では、サービスに組み込まれたセキュリティ インテリジェンスの追加レイヤーを提供することにより、[Managed Instance の監査](sql-database-managed-instance-auditing.md)を補完します。このレイヤーでは、データベースにアクセスしたりデータベースを悪用したりしようとする、異常で有害な可能性がある動作を検出します。 不審なアクティビティ、潜在的な脆弱性、SQL インジェクション攻撃や、異常なデータベース アクセス パターンについて、アラートが送信されます。 脅威の検出のアラートは、[Azure Security Center](https://azure.microsoft.com/services/security-center/) で見ることができます。不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨される対処方法が表示されます。  
- [動的データ マスク](/sql/relational-databases/security/dynamic-data-masking)では、特権のないユーザーに対して機微なデータをマスクすることでデータの公開を制限します。 動的データ マスクでは、公開する機微なデータの量を指定することで、機微なデータに対する未承認のアクセスを防ぐことができ、アプリケーション レイヤーへの影響は最小限に抑えられます。 これはポリシー ベースのセキュリティ機能であり、指定されたデータベース フィールドに対するクエリの結果セットに含まれるデリケートなデータが表示されないようにします。データベース内のデータは変更されません。 
- [行レベルのセキュリティ](/sql/relational-databases/security/row-level-security)を使用して、クエリを実行しているユーザーの特性 (グループのメンバーシップや実行コンテキストなど) に基づいて、データベース テーブル内の行へのアクセスを制御できます。 行レベルのセキュリティ (RLS) により、アプリケーションでのセキュリティの設計やコーディングが簡略化されます。 RLS を使用すると、データ行のアクセスに対して制限を実装できます。 たとえば、ワーカーが自分の部署に関連するデータ行にのみアクセスできるようにしたり、データ アクセスを関連するデータにのみ制限したりできます。 
- [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) は、Azure SQL Database Managed Instance データ ファイルを暗号化します。これは、保存データの暗号化として知られています。 TDE は、データとログ ファイルの I/O 暗号化と複合化をリアルタイムで実行します。 暗号化は、復旧中に、可用性のためのデータベース ブート レコードに格納されるデータベース暗号化キー (DEK) を使用します。 透過的なデータ暗号化でマネージド インスタンス内のすべてのデータベースを保護することができます。 TDE は、記憶域メディアの盗難を防ぐために多くのコンプライアンス基準で必要とされている、SQL の実証済みの保存データの暗号化テクノロジです。 パブリック プレビュー中は、自動キー管理モデルがサポートされます (PaaS プラットフォームで実行されます)。 

Azure Database Migration Service (DMS) またはネイティブの復元を使用して、SQL Managed Instance への暗号化されたデータベースの移行がサポートされています。 ネイティブの復元を使用して暗号化されたデータベースを移行する場合は、オンプレミスの SQL Server または SQL Server VM からマネージド インスタンスへの、既存の TDE 証明書の移行が必要です。 移行オプションの詳細については、「[Azure SQL Database Managed Instance への SQL Server インスタンスの移行](sql-database-managed-instance-migrate.md)」を参照してください。

## <a name="azure-active-directory-integration"></a>Azure Active Directory の統合

Azure SQL Database Managed Instance は、従来の SQL サーバー データベース エンジン ログインと、Azure Active Directory (AAD) に統合されたログインをサポートしています。 AAD ログインは、オンプレミス環境で使用している Windows データベース ログインの Azure クラウド版です。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory との統合と多要素認証 

Managed Instance では、[Azure Active Directory 統合](sql-database-aad-authentication.md)によって、データベース ユーザーとその他の Microsoft サービスの ID を一元的に管理することができます。 この機能は、アクセス許可の管理を簡略化し、セキュリティを強化します。 Azure Active Directory は、[多要素認証](sql-database-ssms-mfa-authentication-configure.md) (MFA) をサポートしています。これにより、シングル サインオン プロセスをサポートすると同時に、データとアプリケーションのセキュリティが強化されます。 

### <a name="authentication"></a>Authentication 
Managed Instance 認証とは、データベースへの接続時にユーザーが自分の ID を証明する方法のことです。 SQL Database は、2 種類の認証をサポートしています。  

- SQL 認証。ユーザー名とパスワードを使用します。
- Azure Active Directory 認証。Azure Active Directory で管理されている ID を使用します。管理および統合されたドメインの場合にサポートされます。 

### <a name="authorization"></a>Authorization

承認とは、Azure SQL Database 内でユーザーにどのような操作が許可されるかを示すものであり、ユーザー アカウントのデータベース ロールのメンバーシップとオブジェクト レベルのアクセス許可によって制御されます。 マネージド インスタンスには、SQL Server 2017 と同じ承認機能があります。 

## <a name="database-migration"></a>データベース移行 

マネージド インスタンスのターゲットは、オンプレミスまたは IaaS データベース実装からのデータベースの一括移行を使用するユーザー シナリオです。 マネージド インスタンスでは、次に示すような複数のデータベース移行オプションをサポートします。 

### <a name="backup-and-restore"></a>バックアップと復元  

移行方法では、Azure BLOB ストレージへの SQL バックアップが活用されます。 Azure ストレージ BLOB に格納されたバックアップは、[T-SQL RESTORE コマンド](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current)を使用して、Managed Instance に直接復元できます。 
  - Wide World Importers - Standard データベース のバックアップ ファイルを復元する方法を説明したクイック スタートについては、[バックアップ ファイルをマネージド インスタンスに復元する](sql-database-managed-instance-get-started-restore.md)方法に関するページを参照してください。 このクイック スタートでは、バックアップ ファイルを Azure ブログ ストレージにアップロードし、Secure Access Signature (SAS) キーを使用してセキュリティで保護する必要があることが説明されています。
  - URL からの復元については、「[URL からのネイティブ復元](sql-database-managed-instance-migrate.md#native-restore-from-url)」を参照してください。

> [!IMPORTANT]
> Managed Instance からのバックアップは、別のマネージド インスタンスにのみ復元できます。 オンプレミスの SQL Server または Azure SQL Database 論理サーバーの単一またはプールされたデータベースに復元することはできません。

### <a name="data-migration-service"></a>データ移行サービス

Azure Database Migration Service は、複数のデータベース ソースから Azure データ プラットフォームへのシームレスな移行を最小限のダウンタイムで実現できるように設計された、フル マネージドのサービスです。 このサービスは、既存のサード パーティ製データベースと SQL Server データベースを Azure に移行するために必要なタスクを効率化します。 パブリック プレビュー段階のデプロイ オプションには、Azure SQL Database、マネージド インスタンス、および Azure VM 内の SQL Server があります。 [DMS を使用してオンプレミスのデータベースをマネージド インスタンスに移行する方法](https://aka.ms/migratetoMIusingDMS)に関するページを参照してください。

## <a name="sql-features-supported"></a>サポートされている SQL 機能 

マネージド インスタンスではサービスの一般提供までに、最新のオンプレミスの SQL Server との、100% 近くのセキュリティ、外部からのアクセス互換性を段階的に実現することを目的としています。 機能と比較の一覧については、[SQL Database 機能の比較](sql-database-features.md)に関するページを参照してください。Managed Instance と SQL Server における T-SQL の相違点の一覧については、[Managed Instance と SQL Server における T-SQL の相違点](sql-database-managed-instance-transact-sql-information.md)に関するページを参照してください。
 
マネージド インスタンスでは、SQL 2008 データベースに対する下位互換性をサポートします。 SQL 2005 データベース サーバーからの直接の移行がサポートされており、移行される SQL 2005 データベースの互換性レベルは SQL 2008 に更新されます。 
 
次の図では、マネージド インスタンスでのセキュリティ、外部からのアクセス互換性について概説します。  

![移行](./media/sql-database-managed-instance/migration.png) 

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>SQL Server オンプレミスとマネージド インスタンスとの主な相違点 

マネージド インスタンスはクラウド内で常に最新の状態が維持されることから利点が得られます。これは、オンプレミスの SQL Server の一部の機能が使用されなくなるか、削除されるか、代替機能が用意される可能性があることを意味します。 特定の機能の動作がやや異なること、または完全には制御できない環境でサービスが実行されていないことをツールで認識することが必要な特定のケースがあります。 

- 高可用性が組み込まれ、事前に構成されています。 Always On 高可用性機能は、SQL IaaS 実装で使用する場合と同じ方法で公開されません。 
- 自動バックアップおよびポイントイン タイム リストア。 顧客は自動バックアップ チェーンに影響を及ぼさない `copy-only` バックアップを開始できます。 
- マネージド インスタンスでは完全な物理パスを指定することができません。したがって、RESTORE DB は WITH MOVE をサポートしない、CREATE DB は物理パスを許可しない、BULK INSERT は Azure BLOB とのみ連携する、など、該当するすべてのシナリオを個別にサポートする必要があります。 
- マネージド インスタンスでは、Windows 認証に代わるクラウドでの別の手段として、[Azure AD 認証](sql-database-aad-authentication.md)をサポートします。 
- マネージド インスタンスでは、インメモリ OLTP オブジェクトを含むデータベース用の XTP ファイル グループおよびファイルを自動的に管理します。
- Managed Instance は SQL Server Integration Services (SSIS) をサポートし、SSIS パッケージを格納する SSIS カタログ (SSISDB) をホストできますが、パッケージは Azure Data Factory (ADF) 上のマネージド Azure-SSIS Integration Runtime (IR) で実行されます。[ADF での Azure-SSIS IR の作成](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)に関するページをご覧ください。 SQL Database と Managed Instance の SSIS 機能を比較するには、[SQL Database 論理サーバーと Managed Instance の比較](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-logical-server-and-sql-database-managed-instance)に関する記事を参照してください。

### <a name="managed-instance-administration-features"></a>マネージド インスタンスの管理機能  

マネージド インスタンスを使用することで、システム管理者はビジネスにとって最も重要な問題に集中することができます。 システム管理者/DBA のアクティビティの多くは必須ではないかシンプルなものです。 該当するアクティビティとして、OS/RDBMS のインストールおよび修正プログラムの適用、動的なインスタンスのサイズ変更および構成、バックアップ、[データベース レプリケーション](replication-with-sql-database-managed-instance.md) (システム データベースを含む)、高可用性の構成、正常性およびパフォーマンス監視データ ストリームの構成などが挙げられます。 

> [!IMPORTANT]
> サポートされている機能、部分的にサポートされている機能、サポートされていない機能の一覧については、[SQL Database の機能](sql-database-features.md)に関するページを参照してください。 マネージド インスタンスと SQL Server における T-SQL の相違点の一覧については、[Managed Instance と SQL Server における T-SQL の相違点](sql-database-managed-instance-transact-sql-information.md)に関するページを参照してください。

### <a name="how-to-programmatically-identify-a-managed-instance"></a>マネージド インスタンスをプログラムで識別する方法

次の表には、Transact SQL を介してアクセスできるプロパティをいくつか示します。これらのプロパティを使用することで、目的のアプリケーションがマネージド インスタンスで動作していることを検出し、重要なプロパティを取得することができます。

|プロパティ|値|Comment (コメント)|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation|この値は SQL Database の値と同じです。|
|`SERVERPROPERTY ('Edition')`|SQL Azure|この値は SQL Database の値と同じです。|
|`SERVERPROPERTY('EngineEdition')`|8|このプロパティはマネージド インスタンスを一意に識別します。|
|`@@SERVERNAME`、`SERVERPROPERTY ('ServerName')`|次の形式による完全なインスタンス DNS 名:<instanceName>.<dnsPrefix>.database.windows.net。ここで、<instanceName> は顧客が指定する名前です。<dnsPrefix> は名前の中で自動生成される部分であり、グローバルな DNS 名の一意性を保証します (例: "wcus17662feb9ce98")|例: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>次の手順

- 最初の Managed Instance を作成する方法については、[クイック スタート ガイド](sql-database-managed-instance-get-started.md)を参照してください。
- 機能比較一覧については、[SQL 共通機能](sql-database-features.md)に関する記事をご覧ください。
- VNet の構成の詳細については、[Managed Instance VNet の構成](sql-database-managed-instance-vnet-configuration.md)に関するページを参照してください。
- マネージド インスタンスを作成し、バックアップ ファイルからデータベースを復元するためのクイック スタートについては、[マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)に関するページを参照してください。
- Azure Database Migration Service (DMS) を使用して移行する方法のチュートリアルについては、[DMS を使用したマネージド インスタンスの移行](../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。
- 料金情報については、[Azure SQL Database Managed Instance の価格](https://azure.microsoft.com/pricing/details/sql-database/managed/)に関するページを参照してください。
