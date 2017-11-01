---
title: "移行後の管理 - Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database への移行後のデータベースの管理方法について説明します。"
services: sql-database
documentationcenter: 
author: joesackmsft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 10/14/2016
ms.author: Joe.Sack
ms.suite: SQL
ms.prod_service: sql-database
ms.component: management
ms.openlocfilehash: 95e364c289aac394e1b3824533288b45725022a8
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2017
---
# <a name="how-should-i-manage-my-azure-sql-database-after-migration"></a>移行後はどのように Azure SQL Database を管理すればよいですか?

*Azure SQL Database への投資の管理についてのよくあるご質問*

最近 SQL Server データベースを Microsoft Azure SQL Database へ移行したか、もう間もなく移行する予定だとします。 移行した後は何をすればよいのでしょうか? SQL Database は*サービスとしてのプラットフォーム*ですから、Microsoft がお客様の代わりにいくつかの領域を処理します。 しかし、セキュリティ、ビジネス継続性、データベースのメンテナンス、パフォーマンス チューニング、監視などの重要な領域に関するお客様の会社の業務はどのように変わるのでしょうか? 

この記事の目的は、SQL Database への投資の管理に移行する上で必要なリソースおよびガイダンスを簡潔にまとめることです。 この記事では重要な領域として、ビジネス継続性、セキュリティ、データベースのメンテナンスと監視、パフォーマンス、データ移動を扱います。 SQL Server と SQL Database で異なる主要な領域について説明するとともに、メリットを最大化しリスクを最小化する上で役立つ業務上のベスト プラクティスを示します。 

## <a name="manage-business-continuity-after-migration"></a>移行後のビジネス継続性の管理

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>SQL Database でバックアップを作成し管理するにはどうすればよいですか? 
SQL Database ではデータベースが自動でバックアップされ、保持期間内の任意の時点で復元できます。 保持期間は、Standard データベースおよび Premium データベースでは 35 日間、Basic データベースでは 7 日間です。 さらに、長期保存機能を使用すると長期 (最大 10 年) にわたりバックアップ ファイルを保持することができ、こうしたバックアップから任意の時点で復元できます。 また、障害または局所的な大災害が発生した場合にリージョンを問わず geo リストアを行うことができるように、データベース バックアップが geo レプリケーションされます。 [ビジネス継続性の概要](sql-database-business-continuity.md)に関するページを参照してください。

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-a-regional-catastrophe"></a>データセンターレベルの障害または局所的大災害が発生した場合にビジネス継続性を確保するにはどうすればよいですか? 

障害または局所的な大災害が発生した場合にリージョンを問わず geo リストアを行うことができるように、データベース バックアップが geo レプリケーションされます。 [ビジネス継続性の概要](sql-database-business-continuity.md)に関するページを参照してください。 さらに、SQL Database には、別のリージョンでアクティブに geo レプリケーションされたセカンダリ データベースを保持する機能も備わっています。 自動フェールオーバー グループでこれらのデータベースを構成することで、障害が起きた場合にセカンダリ データベースへの自動フェールオーバーを行うことができます。 自動フェールオーバー グループを構成していない場合、アプリケーションでは障害を絶えず監視し、セカンダリ データベースへのフェールオーバーを開始する必要があります。 
### <a name="sql-server-provided-me-readable-secondary-replicas-can-i-access-the-secondaries-on-sql-database"></a>SQL Server には読み取り可能なセカンダリ レプリカがありますが、SQL Database ではこれらのセカンダリ レプリカにアクセスできますか? 

はい。"アクティブ geo レプリケーション" 機能を使用して読み取り可能なセカンダリ レプリカを作成できます。 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premise-to-sql-database"></a>オンプレミスのディザスター リカバリー プランをどのように SQL Database に変更すればよいですか? 
SQL Server の実装では、フェールオーバー クラスタリング、データベース ミラーリング、レプリケーション、ログ配布、ごくシンプルな BACPAC バックアップなどの機能を使用して、バックアップを能動的に管理する必要がありました。 しかし、SQL Database ではバックアップは Microsoft により完全に管理されるため、お客様が行うことができるのは、Azure Portal で数回クリックして (または PowerShell で数個のコマンドを入力して) バックアップ プランとディザスター リカバリー プランを構成し、使用することだけです。 ‌
### <a name="in-the-event-of-disaster-how-do-i-recover-my-databases"></a>障害の発生時にデータベースを復旧するにはどうすればよいですか? 
SQL Database では、データベースを過去 35 日間のいずれかの時点に自動で復元できます。 これは、データが失われた場合やアプリケーション関連の障害が発生した場合のオプションです。 

地域的な災害が発生した場合、geo レプリケーションされたセカンダリ データベースを構成していれば、別のリージョンにある geo セカンダリ データベースから復旧できます。 別のリージョンの geo セカンダリ データベースに手動でフェールオーバーすることで、アプリケーションへのリアルタイム アクセスを確保できます。 または、自動フェールオーバー グループを構成している場合、障害発生時に上記の geo セカンダリ データベースへのフェールオーバーが自動で行われます。 geo レプリケーションされたセカンダリ データベースを構成していない場合でも、自動レプリケーション済みのバックアップ ファイル (組み込みの機能であり構成は不要です) からデータベースを復旧できます。ただし、復旧時間はやや長く (RTO は 12 時間)、最大で 1 時間分のデータが失われます。 

### <a name="are-the-failovers-to-secondary-transparent-how-does-my-application-handle-database-failovers"></a>セカンダリへのフェールオーバーは透過的ですか? アプリケーションではデータベース フェールオーバーはどのように処理されますか? 
自動フェールオーバー グループを構成済みの場合、セカンダリへのフェールオーバーは透過的に行われます。 ただし、このグループを構成していない場合、アプリケーションにはプライマリの可用性を監視するロジックを組み込み、セカンダリへのフェールオーバーを手動で行う必要があります。 
 
## <a name="manage-security-after-migration"></a>移行後のセキュリティの管理

### <a name="how-can-i-restrict-access-to-my-sql-database"></a>SQL Database へのアクセスを制限するにはどうすればよいですか? 
 
SQL Database への接続アクセスをロック ダウンする方法はいくつかあります。 
1. インターネット経由のトラフィックを制限します。ExpressRoute では、データがインターネット経由で送信されないように、Azure ネットワークへの専用ファイバーが用意されています。 また、ExpressRoute を使用してリージョン間接続を構成することもできます。 次のリンクで、ExpressRoute についてさらに詳しく説明しています。 
 - [ExpressRoute の概要](../expressroute/expressroute-introduction.md)
 - [前提条件](../expressroute/expressroute-prerequisites.md) 
 - [ワークフロー](../expressroute/expressroute-workflows.md) 
 
2. SQL Database に接続するリソースを選択します。 

   既定では、SQL Database は [Allow all Azure services]\(すべての Azure サービスを許可\) に設定されています。この場合、Azure 内のすべての VM がデータベースへの接続を試行する可能性があります。  この場合でも、すべてのログインについて認証が必要です。 データベースで一部の Azure IP にアクセスできないようにする場合は、[Allow all Azure services]\(すべての Azure サービスを許可\) を無効にして、[VNET サービス エンドポイント](sql-database-vnet-service-endpoint-rule-overview.md)を使用して、データベースへの受信アクセスを、特定の Azure VNET サブネット内にある Azure リソースからのもののみに制限します。 

   ![VNET サービス エンドポイント](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

   別の選択肢としては、VM に[予約済み IP](../virtual-network/virtual-networks-reserved-public-ip.md) をプロビジョニングし、サーバーのファイアウォール設定でこれらの VM IP アドレスをホワイトリストに登録します。 (Azure Portal での例として、以下のスクリーンショットを参照してください。)予約済み IP アドレスを割り当てると、IP アドレスを変更した場合にファイアウォールの規則を更新する手間が省けます。 

3. ポート 1433 を Azure 外部に公開しないようにする

   [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps) を使用して、Azure で SSMS を実行します。 この場合、ポート 1433 に対する発信接続を開く必要はありません。IP は静的であるため、DB を開放できるのは RemoteApp に対してのみです。多要素認証 (MFA) とマルチユーザーがサポートされます。 

### <a name="what-authentication-methods-are-offered-in-sql-database"></a>SQL Database ではどのような認証方法が提供されますか?

SQL Database および SQL Data Warehouse で提供される主要な認証方法は、Azure Active Directory 認証と SQL 認証です。 Azure Active Directory (AD) は一元的な ID およびアクセス管理サービスであり、SQL は Azure AD に統合されている多数の Azure サービスの 1 つです。 一元的管理の サービスのメリットは、使用する Azure サービス全体でユーザーの資格情報が共有されるため、認証が簡単になることです。 このサービスを使用すると、SQL Database および SQL Data Warehouse で、Azure AD ドメイン内において多要素認証とゲスト ユーザー アカウントを提供できます。 

既に Active Directory をオンプレミスで使用している場合、そのディレクトリを Azure Active Directory とフェデレーションして Azure へ拡張できます。 


|||
|---|---|
| お客様の状態|Azure SQL Database/Azure SQL Data Warehouse|
| Azure で Azure Active Directory (AD) を使用しない|[SQL 認証](sql-database-security-overview.md)を使用します。|
| オンプレミスの SQL Server で AD を使用している|[AD と Azure AD をフェデレーションして](../active-directory/connect/active-directory-aadconnect.md)、Azure AD 認証を使用します。 これにより、シングル サインオンを使用できます。|
| 多要素認証 (MFA) を適用する必要がある|[Microsoft 条件付きアクセス](sql-database-conditional-access.md)で MFA をポリシーとして必須にして、[MFA サポート付きの Azure AD ユニバーサル認証](sql-database-ssms-mfa-authentication.md)を使用します。|
| Microsoft アカウント (live.com、outlook.com) または他のドメイン (gmail.com など) のゲスト アカウントを持っている|SQL Database/Data Warehouse で、[Azure AD B2B コラボレーション](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)を利用する [Azure AD ユニバーサル認証](sql-database-ssms-mfa-authentication.md)を使用します。|
| フェデレーション ドメインから Azure AD の資格情報を使用して Windows にログオンしている|[Azure AD 統合認証](sql-database-aad-authentication-configure.md)を使用します。|
| Azure にフェデレーションされていないドメインから資格情報を使用して Windows にログオンしている|[Azure AD パスワード認証](sql-database-aad-authentication-configure.md)を使用します。|
| Azure SQL Database または SQL Data Warehouse への接続が必要な中間層サービスを使用している|[Azure AD トークン認証](sql-database-aad-authentication-configure.md)を使用します。
|||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-databases-from-the-application-side"></a>データベース内の機微なデータへのアプリケーション側からのアクセスを制限するにはどうすればよいですか? 

SQL Database には、機微なデータを未承認ユーザーが表示できないようにするいくつかのオプションがあります。 

- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) はクライアント側暗号化の 1 種であり、データベースの機密列を暗号化します (このため、これらの列は、データベース管理者および未承認ユーザーに対しては暗号化テキストになります)。 Always Encrypted のキーはクライアント側に格納されるため、機密列の暗号化を解除できるのは承認済みクライアントのみに限られます。 Always Encrypted では現在等値比較がサポートされているため、DBA では引き続き、SQL コマンドの一環として暗号化された列に対してクエリを行うことができます。 Always Encrypted は、[Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)、Windows 証明書ストア、ローカル ハードウェアのセキュリティ モジュールなどの多様なキー ストア オプションと組み合わせて使用できます。
- [動的データ マスク](sql-database-dynamic-data-masking-get-started.md)は、アプリケーション レイヤー上の権限のないユーザーに対して機微なデータをマスクすることでこれらのデータの公開を制限するデータ マスク機能です。 マスク ルールを定義してマスク パターン (例: 国 ID の最後の 4 桁のみを表示し残りは X と表示する) を作成するとともに、マスク ルールから除外するユーザーを指定できます。
- [行レベルのセキュリティ](/sql/relational-databases/security/row-level-security)では、クエリを実行するユーザー (グループ メンバーシップまたは実行コンテキスト) に基づいて、データベース テーブル内の行へのアクセスを制限できます。 このアクセス制限は、アプリのロジックを簡素化するため、アプリケーション層ではなくデータベース層で行われます。 

### <a name="what-encryption-options-do-i-have-in-sql-database-and-what-actors-does-the-encryption-protect-from"></a>SQL Database にはどのような暗号化オプションがありますか? また、暗号化で保護されるアクターはどのようなものですか?
SQL Database で利用できる主要な暗号化テクノロジは 3 つあります。 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) (上記の質問で説明しています): 未承認のクライアントから物理ディスクまで、テーブル内の機密列をエンド ツー エンドで暗号化します。 暗号化キーはクライアントに格納されるため、サーバー管理者およびデータ管理者は機微なデータを確認することはできません。 
- [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) (TDE): 保存時の暗号化であり、データ ファイル、ログ ファイル、および関連するバックアップをデータベース レベルで暗号化し、物理メディアの盗難から保護します。 TDE は、新しく作成されたすべてのデータベースで、既定で有効化されます。
 
  以下の表に、暗号化テクノロジの選択肢の概要を示します。

   ![暗号化の概要](./media/sql-database-manage-after-migration/overview-encryption.png)

### <a name="how-should-i-manage-encryption-keys-in-the-cloud"></a>暗号化キーはクラウドでどのように管理すればよいですか? 
Always Encrypted (クライアント側暗号化) および Transparent Data Encryption (保存時暗号化) ではともに、キー管理オプションが用意されています。 暗号化キーは、社内規制要件およびコンプライアンス要件の両方を満たす頻度で定期的にローテーションさせることをお勧めします。

- **Always Encrypted**: Always Encrypted は [2 キー階層](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted)になっており、機微なデータの列は AES 256 列暗号化キー (CEK) で暗号化され、さらに列マスター キー (CMK) で暗号化されます。 Always Encrypted で提供されるクライアント ドライバーには、CMK の長さに関する制限はありません。

  CEK の暗号化値はデータベースに格納され、CMK は Windows 証明書ストア、Azure Key Vault、ハードウェア セキュリティ モジュールなどの信頼できるキー ストアに格納されます。 
  
  [CEK と CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) はともにローテーションさせることができます。 CEK のローテーションは、暗号化された列を含むテーブルのサイズによっては時間がかかる場合があります。 このため、CEK のローテーションについては慎重に計画してください。 一方、CMK のローテーションはデータベースのパフォーマンスに影響しないため、個別のロールを使用して行うことができます。

  次の図に、Always Encrypted の列マスター キーのキー ストア オプションを示します。 

   ![Always Encrypted の CMK ストア プロバイダー](./media/sql-database-manage-after-migration/always-encrypted.png)

- **Transparent Data Encryption (TDE)**: TDE は 2 キー階層になっており、各ユーザー データベースのデータは対称的でデータベース固有の AES-256 データベース暗号化キー (DEK) で暗号化され、さらにサーバー固有の非対称の RSA 2048 マスター キーで暗号化されます。 

  既定では、Transparent Data Encryption のマスター キーは利便性のために SQL Database サービスにより管理されます。 組織でマスター キーを管理する必要がある場合、[Azure Key Vault](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql) をキー ストアとして使用できます。 

  Azure Key Vault を使用する場合、組織でキーのプロビジョニング、ローテーション、および権限管理を制御することになります。 TDE マスター キーは DEK を再暗号化しただけのものであるため、[このキーのローテーションおよび種類の切り替え](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation)は短時間で行うことができます。 

  セキュリティとデータ管理で役割が分けられている組織の場合、セキュリティ管理者が TDE マスター キーの主要な素材を Azure Key Vault にプロビジョニングし、サーバーでの保存時暗号化用に使用する Azure Key Vault キー ID をデータベース管理者に提供できます。 

## <a name="monitoring-and-compliance-after-migration"></a>移行後の監視およびコンプライアンス

### <a name="how-do-i-monitor-database-activities-in-sql-database"></a>SQL Database ではデータベース アクティビティをどのように監視すればよいですか?
SQL Database には、データベースのセキュリティ イベントやその他のイベントを追跡するいくつかの監視機能が組み込まれています。
- [SQL 監査](sql-database-auditing.md): ご自身の Azure Storage アカウントのデータベース イベントの監査ログを収集できます。
- [SQL 脅威検出](sql-database-threat-detection.md): データベース内のデータに対し、悪意をもってアクセス、侵害、悪用しようとしている可能性のある不審なアクティビティを検出できます。 SQL Database の脅威検出機能では、潜在的な脆弱性と SQL インジェクション攻撃、および異常なデータベース アクセス パターン (通常とは異なる場所または未知のプリンシパルからのアクセスなど) を検出するアルゴリズムを複数セット実行します。 データベースで脅威が検出された場合、セキュリティ責任者またはその他の指定された管理者に電子メール通知が送られます。 各通知には、不審なアクティビティの詳細情報と、脅威に対して推奨されるさらなる調査方法や軽減策が記載されています。 
- [SQL 脆弱性評価](sql-vulnerability-assessment.md): データベース スキャンおよびレポート サービスであり、大規模なデータベースのセキュリティ状態を監視して、セキュリティ上のリスクおよびユーザーが定義したセキュリティ ベースラインからのずれを特定します。 各スキャン後には、ユーザーに合わせた実行可能な手順の一覧と修復スクリプト、およびコンプライアンスに準拠するために使用可能な評価レポートが提供されます。 
- [SQL-OMS セキュリティ同期アプリケーション](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration): Microsoft Operations Management Suite (OMS) のパブリック API を使用して SQL 監査ログを OMS にプッシュし、以下のようなログ分析やカスタム検出アラート定義の機能を活用します。 
 - データベース アクティビティに関する明確でわかりやすいレポートが表示される SQL Database 監査タイル/ダッシュボード 
 - データベース アクティビティを分析し、セキュリティ違反の疑いがある不一致や異常について調査する SQL Log Analytics
 - 電子メール、webhook、Azure Automation Runbook アラートをトリガーする観察対象イベント (パスワードの変更、勤務時間後、特定の SQL コマンドなど) に対する高度なアラート固有ルール
- [Azure Security Center](../security-center/security-center-intro.md): Azure、オンプレミス、他のクラウドで実行されているワークロード全体でのセキュリティ管理を一元化します。 監査や Transparent Data Encryption などの SQL Database の主要な保護機能がすべてのリソースに対して構成されているかどうかを確認し、独自の要件に応じてポリシーを作成できます。 

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>SQL Database はすべての規制要件に適合していますか? また、SQL Database は組織のコンプライアンスにどのように役立ちますか? 
Azure SQL Database は、さまざまな規制に準拠しています。 適合している法規制の最新の一覧については、[Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings) にアクセスして組織に重要なコンプライアンスをドリルダウンし、Azure SQL Database がコンプライアンスに準拠している Azure サービスに含まれているかどうかを確認してください。 Azure SQL Database はコンプライアンスに準拠しているサービスとして認証を受けていますが、これは組織のサービスのコンプライアンスを支援するものであり、自動的にコンプライアンスを保証するものではないことに注意してください。 

## <a name="database-maintenance-and-monitoring-after-migration"></a>移行後のデータベースのメンテナンスおよび監視

### <a name="how-do-i-monitor-growth-in-data-size-and-resource-utilization"></a>データ サイズおよびリソース使用率の増加を監視するにはどうすればよいですか?

- Azure Portal の [監視] グラフで、データベース サイズとリソース使用率に関する監視メトリックを確認できます。 

  ![監視グラフ](./media/sql-database-manage-after-migration/monitoring-chart.png)

- Azure Portal に用意されている "Query Performance Insight" を使用して、より深い詳細を確認しクエリの詳細にドリルダウンすることができます。 これを行うには、データベースで "クエリ ストア" をアクティブにする必要があります。

  ![[Query Performance Insight]](./media/sql-database-manage-after-migration/query-performance-insight.png)

- または、動的管理ビュー (DMV) を使用して ([sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) および [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) を使用)、これらのメトリックを確認することもできます。 

### <a name="how-often-do-i-need-to-run-consistency-checks-like-dbcccheckdb"></a>DBCC_CHECKDB などの整合性チェックはどれくらいの頻度で実行する必要がありますか?
DBCC_CHECKDB では、データベース内のすべてのオブジェクトの論理的および物理的な整合性を確認します。 これらのチェックは Azure 上で Microsoft により管理されるため、お客様が実行する必要はありません。 詳細については、「[Data Integrity in Azure SQL Database (Azure SQL Database のデータ整合性)](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)」を参照してください。

## <a name="monitor-performance-and-resource-utilization-after-migration"></a>移行後のパフォーマンスおよびリソース使用率の監視

### <a name="how-do-i-monitor-performance-and-resource-utilization-in-azure-sql-database"></a>Azure SQL Database のパフォーマンスとリソース使用率を監視するにはどうすればよいですか?
Azure SQL Database のパフォーマンスとリソース使用率は、次の方法で監視できます。

- **Azure Portal**: Azure Portal でデータベースを選択し [概要] ペインのグラフをクリックすると、単一のデータベースの使用率が表示されます。 このグラフは、CPU 使用率、DTU の割合、データ IO の割合、セッションの割合、データベース サイズの割合など、複数のメトリックを表示するように変更できます。 
  ![リソース使用率](./media/sql-database-manage-after-migration/resource-utilization.png)

  このグラフでは、リソース別のアラートを構成することもできます。 これらのアラートを使用することで、リソースの状態に電子メールで対応したり、HTTPS/HTTP エンドポイントに書き込んだり、アクションを実行することができます。 詳細な手順については、「[Azure SQL Database におけるデータベース パフォーマンスの監視](sql-database-single-database-monitor.md)」を参照してください。

- **ビュー**: リソース使用率の統計について、[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 動的管理ビューでクエリを実行すると過去 1 時間における履歴を確認でき、[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) システム カタログ ビューでクエリを実行すると過去 14 日間の履歴を確認できます。 

- **Query Performance Insight**: [Query Performance Insight](sql-database-query-performance.md) では、指定したデータベースについて、リソース使用率が上位のクエリおよび実行時間が長いクエリの履歴を確認できます。 この機能を使用するには、データベースで[クエリ ストア](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)を有効化しアクティブにする必要があります。

- **Log Analytics の Azure SQL Analytics (プレビュー)**: [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md) では、主要な Azure SQL パフォーマンス メトリックを収集して視覚化することができます。ワークスペースごとに Azure SQL Database は 15 万個、SQL エラスティック プールは 5 千個サポートされています。 これにより、監視を行い、通知を受け取ることができます。 Azure SQL Database およびエラスティック プールのメトリックは、複数の Azure サブスクリプションとエラスティック プールにわたって監視でき、これらのメトリックを利用してアプリケーション スタックの各層における問題を特定できます。 

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>適切なサービス階層とパフォーマンス レベルを使用していることを確認するにはどうすればよいですか?
[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) および [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) の各動的管理ビューを監視して、CPU、I/O、メモリ使用率を確認してください。 または、SQL Database の [Query Performance Insight](sql-database-query-performance.md) を使用して、リソース使用率を確認できます。 実行時に利用可能なリソースが一貫して大量に使用されている場合は、既存のサービス階層内で上位のパフォーマンス レベルに変更するか、上位のサービス階層に移動することを検討してください。 反対に、利用可能なリソースが一貫してほとんど使用されていない場合は、下位のパフォーマンス レベルまたはサービス階層に変更することを検討してください。

### <a name="i-am-seeing-performance-issues-how-does-my-azure-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>パフォーマンスに問題があります。 Azure SQL Database と SQL Server のトラブルシューティング方法の違いはどのようなものですか?
パフォーマンスに関するトラブルシューティングの方法は Azure SQL Database でもほとんどの面で同じですが、いくつかの違いがあります。 たとえば、全体的なパフォーマンスの低下が生じている場合は、[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) および [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) の各動的管理ビューを監視して、CPU、I/O、メモリ使用率を確認します。 ワークロードのニーズに応じて、パフォーマンス レベルやサービス階層の変更が必要になる場合があります。
パフォーマンスのチューニングに関する問題の包括的な推奨事項については、「[Azure SQL Database のパフォーマンスのチューニング](sql-database-performance-guidance.md)」を参照してください。 

### <a name="do-i-need-to-maintain-indexes-and-statistics"></a>インデックスと統計情報を管理する必要はありますか?
Azure SQL Database では、サービスの一環としてインデックスおよび統計情報を自動で管理することはしていません。 インデックスおよび統計情報の管理のスケジュール設定は、お客様が行う必要があります。 「Azure Automation Methods (Azure Automation のメソッド)」の記事では、Azure SQL Database に対してメンテナンス ジョブのスケジュールを設定する複数のオプションについて説明されています。

## <a name="data-movement-after-migration"></a>移行後のデータの移動

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-azure-sql-database"></a>Azure SQL Database のデータを BACPAC ファイルとしてエクスポートおよびインポートするにはどうすればよいですか? 

- **エクスポート**: Azure SQL Database は、Azure Portal で BACPAC ファイルとしてエクスポートできます。

  ![BACPAC としてのエクスポート](./media/sql-database-export/database-export.png)

- **インポート**: Azure Portal を使用して、BACPAC ファイルをデータベースにインポートできます。

  ![BACPAC のインポート](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-azure-sql-database-sql-server-2016--2012"></a>Azure SQL Database と SQL Server 2016/2012 間でデータを同期するにはどうすればよいですか?
[データ同期](sql-database-sync-data.md)機能を使用して、複数のオンプレミス SQL Server データベースと Azure SQL Database 間でデータを双方向に同期することができます。 ただし、この同期はトリガーに基づくため、最終的な整合性 (データ損失なし) は保証されますが、トランザクションの整合性は保証されません。 

## <a name="next-steps"></a>次のステップ
[Azure SQL Database](sql-database-technical-overview.md) について確認してください。
