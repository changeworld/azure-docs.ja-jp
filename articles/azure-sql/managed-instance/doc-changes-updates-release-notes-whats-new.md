---
title: 新機能
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance の新機能とドキュメントの改善について説明します。
services: sql-database
author: MashaMSFT
ms.author: mathoma
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: references_regions
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/21/2021
ms.openlocfilehash: 59147dd6037e5eb2f4565ec135d04f3a1c242cc3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130247931"
---
# <a name="whats-new-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance の新機能
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、[Azure SQL Managed Instance](https://azure.microsoft.com/updates/?product=sql-database&query=sql%20managed%20instance) の最新リリースで導入された新機能と機能強化に関連するドキュメントの変更をまとめます。 Azure SQL Managed Instance の詳細については、[概要](sql-managed-instance-paas-overview.md)に関するページを参照してください。


Azure SQL Database については、[新機能](../database/doc-changes-updates-release-notes-whats-new.md)に関するページを参照してください。


## <a name="preview"></a>プレビュー

次の表に、現在プレビュー段階にある Azure SQL Managed Instance の機能を示します。


| 特徴量 | 詳細 |
| ---| --- |
| [SQL Managed Instance General Purpose の 16 TB サポート](resource-limits.md)| SQL Managed Instance General Purpose で最大 16 TB の領域の割り当てをサポート |
| [Azure SQL 用 Azure Active Directory のみの認証](../database/authentication-azure-ad-only-authentication.md) |  Azure SQL Managed Instance の認証を Azure Active Directory のユーザーのみに限定することができるようになりました。 |
| [エラスティック トランザクション](../database/elastic-transactions-overview.md) | エラスティック トランザクションを使用すると、Azure SQL Database と Azure SQL Managed Instance 内のクラウド データベース全体で分散トランザクションを実行できます。 |
| [インスタンス プール](instance-pools-overview.md) | 比較的小規模な SQL Server インスタンスをクラウドに移行するための、便利かつ費用対効果に優れた方法です。 |
| [Log Replay Service を使用した移行](log-replay-service-migrate.md) | Log Replay Service を使用して SQL Server から SQL Managed Instance にデータベースを移行する。 |
| [メンテナンス期間](../database/maintenance-window.md)| メンテナンス期間機能を使用して、Azure SQL Managed Instance のメンテナンス スケジュールを構成できます。 |
| [長期のバックアップ リテンション期間](long-term-backup-retention-configure.md) | Azure SQL Managed Instance で最大 10 年間の長期のバックアップ リテンション期間をサポート。 |
| [Service Broker のクロスインスタンス メッセージ交換](/sql/database-engine/configure-windows/sql-server-service-broker) | Azure SQL Managed Instance で Service Broker を使用したクロスインスタンス メッセージ交換をサポートします。 |
| [SQL Insights](../../azure-monitor/insights/sql-insights-overview.md) | SQL Insights は、Azure SQL ファミリのあらゆる製品を監視するための包括的なソリューションです。 SQL Insights では、動的管理ビューを使用して、正常性の監視、問題の診断、パフォーマンスの調整に必要なデータを公開します。 |
| [トランザクション レプリケーション](replication-transactional-overview.md) | テーブルの変更を、SQL Managed Instance、SQL Database、または SQL Server の他のデータベースにレプリケートします。 または、SQL Managed Instance や SQL Server の他のインスタンスで一部の行が変更されたときに、テーブルを更新します。 詳しくは、[Azure SQL Managed Instance にレプリケーションを構成する方法](replication-between-two-instances-configure-tutorial.md)に関する記事をご覧ください。 |
| [脅威の検出](threat-detection-configure.md) | 脅威の検出は、データベースに対して検出されたセキュリティの脅威を通知します。 |
| [クエリ ストアのヒント](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-mi-current&preserve-view=true) | クエリ ヒントでは、OPTION 句によってクエリの実行を最適化します。 |
|||

## <a name="general-availability-ga"></a>一般公開 (GA)

次の表に、過去 12 か月以内にプレビューから一般提供 (GA) に移行した Azure SQL Managed Instance の機能を示します。 

| 特徴量 | GA の月 | 詳細 |
| ---| --- |--- |
| [Machine Learning Service](machine-learning-services-overview.md) | 2021 年 3 月 | Machine Learning Services は Azure Python Managed Instance の機能であり、データベース内の機械学習を提供し、Python スクリプトと R スクリプトの両方をサポートしています。 その機能には、高パフォーマンスの予測分析と機械学習のための Microsoft Python および R のパッケージが含まれています。 |
| [動的データ マスクの詳細なアクセス許可](../database/dynamic-data-masking-overview.md)| 2021 年 3 月 | 動的データ マスクでは、公開するデリケートなデータの量を指定することで、デリケートなデータに対する未承認のアクセスを防ぎ、アプリケーション レイヤーへの影響は最小限に抑えられます。 これはポリシー ベースのセキュリティ機能です。これにより、データベース内のデータはそのままで、指定されたデータベース フィールドに対するクエリの結果セットで機微なデータを非表示にすることができます。 動的にマスクされたデータに対して詳細なアクセス許可を割り当てることができるようになりました。 詳細については、「[動的なデータ マスク](../database/dynamic-data-masking-overview.md#permissions)」を参照してください。 |
| [管理操作の監査](../database/auditing-overview.md#auditing-of-microsoft-support-operations) |  2021 年 3 月 | Azure SQL 監査機能を使用すると、サポート リクエスト中に SQL 資産にアクセスする必要がある場合、Microsoft サポート エンジニアが実行した操作を監査して、従業員の透明性を高めることができます。 | 
||| 

## <a name="documentation-changes"></a>ドキュメントの変更点

Azure SQL Managed Instance ドキュメントに対する重要な変更点について説明します。

### <a name="october-2021"></a>2021 年 10 月

| [変更点] | 詳細 |
| --- | --- |
|**新機能の分割** | 以前、一緒になっていた、**新機能** に関する記事は、製品ごとに「[SQL Database の新機能](../database/doc-changes-updates-release-notes-whats-new.md)」と「[SQL Managed Instance の新機能](doc-changes-updates-release-notes-whats-new.md)」に分割されています。これにより、現在プレビュー段階の機能、一般提供されている機能、重要なドキュメントの変更点を簡単に識別できます。 さらに、[SQL Managed Instance での既知の問題](doc-changes-updates-known-issues.md)に関するコンテンツが、独自のページに移動されました。  | 


### <a name="june-2021"></a>2021 年 6 月

| [変更点] | 詳細 |
| --- | --- |
|**General Purpose の 16 TB サポート** | SQL Managed Instance General Purpose に最大 16 TB の領域割り当てのサポートが追加されました。 詳細については、[リソース制限](resource-limits.md)に関するページを参照してください。 このインスタンスのオファーは現在、プレビュー段階です。 | 
| **並列バックアップ** | 汎用レベルの SQL Managed Instance のバックアップを並行して行うことができるようになりました。これにより、バックアップが高速になります。 詳細については、[パフォーマンス向上のための並列バックアップ](https://techcommunity.microsoft.com/t5/azure-sql/parallel-backup-for-better-performance-in-sql-managed-instance/ba-p/2421762)に関するブログ エントリを参照してください。 |
| **Azure AD のみの認証** | Azure SQL Managed Instance の認証を Azure Active Directory のユーザーのみに限定することができるようになりました。 現在、この機能はプレビュー段階にあります。 詳細については、[Azure AD のみの認証](../database/authentication-azure-ad-only-authentication.md)に関するページを参照してください。 | 
| **Resource Health 監視** | Resource Health を使用して、Azure SQL Managed Instance の正常性状態を監視します。 詳細については、[Resource Health](../database/resource-health-to-troubleshoot-connectivity.md) に関するページを参照してください。 |
| **GA のデータ マスキングの詳細なアクセス許可** | Azure SQL Managed Instance の動的データマスクの詳細なアクセス許可が一般提供 (GA) されました。 詳細については、「[動的なデータ マスク](../database/dynamic-data-masking-overview.md#permissions)」を参照してください。 | 
|  | |


### <a name="april-2021"></a>2021 年 4 月

| [変更点] | 詳細 |
| --- | --- |
| **ユーザー定義ルート (UDR) テーブル** | Azure SQL Managed Instance のサービス支援サブネット構成で、ユーザー定義ルート (UDR) テーブル用のサービス タグが利用可能になりました。 詳細については、[接続のアーキテクチャ](connectivity-architecture-overview.md)に関するページを参照してください。 |
|  |  |


### <a name="march-2021"></a>2021 年 3 月

| [変更点] | 詳細 |
| --- | --- |
| **管理操作の監査** | SQL Managed Instance 操作を監査する機能が一般提供 (GA) されました。 | 
| **ログ再生サービス** | ログ再生サービスを使用して、SQL Server から Azure SQL Managed Instance にデータベースを移行できるようになりました。 詳細については、[ログ再生サービスを使用した移行](log-replay-service-migrate.md)に関するページを参照してください。 現在、この機能はプレビュー段階にあります。 | 
| **長期のバックアップ リテンション期間** | Azure SQL Managed Instance で最大 10 年間の長期のバックアップ リテンション期間をサポート。 詳細については、[長期のバックアップ リテンション期間](long-term-backup-retention-configure.md)に関するページを参照してください|
| **Machine Learning Services GA** | Azure SQL Managed Instance の Machine Learning Services が一般提供 (GA) されました。 詳細については、「[SQL Managed Instance の Machine Learning Services](machine-learning-services-overview.md)」を参照してください。| 
| **メンテナンス期間** | メンテナンス期間機能を使用して、Azure SQL Managed Instance のメンテナンス スケジュールを構成できます。これは現在プレビューの段階です。 詳細については、「[メンテナンス期間](../database/maintenance-window.md)」を参照してください。|
| **Service Broker メッセージ交換** | Azure SQL Managed Instance の Service Broker コンポーネントを使用すると、サービスにアタッチされているデータベース間で信頼性の高い安全なメッセージ交換をネイティブでサポートすることにより、独立した自己完結型サービスからアプリケーションを構成できます。 現在プレビューの段階です。 詳細については、「[Service Broker](/sql/database-engine/configure-windows/sql-server-service-broker)」を参照してください。
| **SQL Insights** | SQL Insights は、Azure SQL ファミリのあらゆる製品を監視するための包括的なソリューションです。 SQL Insights では、動的管理ビューを使用して、正常性の監視、問題の診断、パフォーマンスの調整に必要なデータを公開します。 詳細については、[SQL Insights](../../azure-monitor/insights/sql-insights-overview.md) に関するページを参照してください。 | 
||| 

### <a name="2020"></a>2020

次の変更は、2020 年に SQL Managed Instance とドキュメントに追加されました。 

| [変更点] | 詳細 |
| --- | --- |
| **サポート操作の監査** | Microsoft サポート操作の監査機能を使用すると、監査ログの保存先へのサポート リクエスト中にサーバーやデータベースにアクセスする必要がある場合、Microsoft サポート操作を監査することができます (プレビュー)。 詳細については、[サポート操作の監査](../database/auditing-overview.md#auditing-of-microsoft-support-operations)に関するページを参照してください。|
| **エラスティック トランザクション** | エラスティック トランザクションを使用すると、Azure SQL Database と Azure SQL Managed Instance にまたがる複数データベースの分散データベース トランザクションが可能になります。 エラスティック トランザクションが追加され、既存のアプリケーションをスムーズに移行することや、垂直または水平方向にパーティション分割されたデータベース アーキテクチャに従う最新のマルチテナント アプリケーションの開発ができるようになりました (プレビュー)。 詳細については、[分散トランザクション](../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)に関する記事を参照してください。 | 
| **構成可能なバックアップ ストレージの冗長性** | バックアップ ストレージの冗長性について、ローカル冗長ストレージ (LRS) オプションとゾーン冗長ストレージ (ZRS) オプションを構成できるようになりました。これにより、より柔軟な選択が可能になりました。 詳細については、「[バックアップ ストレージの冗長性を構成する](../database/automated-backups-overview.md?tabs=managed-instance#configure-backup-storage-redundancy)」を参照してください。|
| **TDE で暗号化されたバックアップのパフォーマンスの向上** | ポイントインタイム リストア (PITR) バックアップの保持期間を設定できるようになりました。また、透過的なデータ暗号化 (TDE) で暗号化されたバックアップの自動圧縮により、バックアップ ストレージ領域の使用効率が 30% 向上し、エンド ユーザーのコストを削減できます。 詳細については、[PITR の変更](../database/automated-backups-overview.md?tabs=managed-instance#change-the-short-term-retention-policy)に関するページを参照してください。 |
| **Azure AD 認証の機能強化** | Azure AD アプリケーションを使用してユーザー作成を自動化し、個別の Azure AD ゲスト ユーザーを作成します (プレビュー)。 詳細については、[Azure AD のディレクトリ閲覧者](../database/authentication-aad-directory-readers-role.md)に関するページを参照してください|
| **グローバル VNet ピアリング サポート** | グローバル仮想ネットワーク ピアリングのサポートが SQL Managed Instance に追加され、geo レプリケーションのエクスペリエンスが向上しました。 [マネージド インスタンス間での geo レプリケーションの構成](../database/auto-failover-group-overview.md?tabs=azure-powershell#enabling-geo-replication-between-managed-instances-and-their-vnets)に関するページを参照してください。 |
| **SSRS カタログ データベースのホスティング** | SQL Managed Instance で、サポートされているすべてのバージョンの SQL Server Reporting Services (SSRS) のカタログ データベースをホストできるようになりました。 | 
| **パフォーマンスの大幅な向上** | トランザクション ログ書き込みのスループット向上、ビジネス クリティカルなインスタンスのデータとログの IOPS 向上、TempDB のパフォーマンス向上など、SQL Managed Instance パフォーマンスの向上について紹介します。 詳細については、[パフォーマンスの向上](https://techcommunity.microsoft.com/t5/azure-sql/announcing-major-performance-improvements-for-azure-sql-database/ba-p/1701256)に関する技術コミュニティのブログを参照してください。 
| **強化された管理エクスペリエンス** | 新しい[操作 API](/rest/api/sql/2021-02-01-preview/managed-instance-operations) を使用して、実行時間の長いインスタンス操作の進行状況を確認できるようになりました。 詳細については、[管理操作](management-operations-overview.md?tabs=azure-portal)に関するページを参照してください。
| **機械学習のサポート** | R と Python 言語をサポートする Machine Learning Services に、Azure SQL Managed Instance でのプレビュー サポートが追加されました (プレビュー)。 詳細については、[SQL Managed Instance を使用した機械学習](machine-learning-services-overview.md)に関するページを参照してください。 | 
| **ユーザーが開始したフェールオーバー** | ユーザーによって開始されるフェールオーバーが一般提供され、PowerShell、CLI コマンド、API 呼び出しを使用して自動フェールオーバーを手動で開始する機能が提供されました。これにより、アプリケーションの回復性が向上します。 詳細については、[回復性のテスト](../database/high-availability-sla.md#testing-application-fault-resiliency)に関するページを参照してください。 
|  |  |



## <a name="known-issues"></a>既知の問題

既知の問題に関するコンテンツは、[SQL Managed Instance での既知の問題](doc-changes-updates-known-issues.md)専用の記事に移動されました。 


## <a name="contribute-to-content"></a>コンテンツの改善への協力

Azure SQL のドキュメントに投稿するには、[Docs 共同作成者ガイド](/contribute/)に関する記事をご覧ください。
