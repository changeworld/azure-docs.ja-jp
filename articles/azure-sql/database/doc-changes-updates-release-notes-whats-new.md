---
title: 新機能
titleSuffix: Azure SQL Database
description: Azure SQL Database の新機能とドキュメントの改善について説明します。
services: sql-database
author: MashaMSFT
ms.author: mathoma
ms.service: sql-database
ms.subservice: service-overview
ms.custom: sqldbrb=2, references_regions, ignite-fall-2021
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/21/2021
ms.openlocfilehash: e57adde4df10a942baff7b7032706a991d46050c
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556175"
---
# <a name="whats-new-in-azure-sql-database"></a>Azure SQL Database の新機能
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事は、[Azure SQL Database](https://azure.microsoft.com/products/azure-sql/database/) の最新リリースで導入された新機能と機能強化に関連するドキュメントの変更点をまとめたものです。 Azure SQL Database の詳細については、[概要](sql-database-paas-overview.md)に関する記事を参照してください。 

Azure SQL Managed Instance については、「[新機能](../managed-instance/doc-changes-updates-release-notes-whats-new.md)」を参照してください。

> [!div class="nextstepaction"]
> [Azure SQL を改善するためのアンケート](https://aka.ms/AzureSQLSurveyNov2021)



## <a name="preview"></a>プレビュー 

次の表に、現在プレビュー段階にある Azure SQL Database の機能を示します。 

| 特徴量 | 詳細 |
| ---| --- |
| [変更データのキャプチャ](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 変更データ キャプチャ (CDC) を使用すると、データベース上で発生したすべての変更を追跡できます。 この機能は、かなり前から SQL Server で使用できましたが、Azure SQL Database での使用は現在プレビュー段階です。 |
| [エラスティック ジョブ](elastic-jobs-overview.md) | エラスティック ジョブ機能は、PaaS オファリングとして利用できる Azure SQL Database の SQL Server エージェントの代替機能です。  |
| [エラスティック クエリ](elastic-query-overview.md) | エラスティック クエリ機能を使用すると、Azure SQL Database で複数のデータベースにまたがるクエリを実行できます。 |
| [エラスティック トランザクション](elastic-transactions-overview.md) | エラスティック トランザクションを使用すると、Azure SQL Database のクラウド データベース間で分散されたトランザクションを実行できます。 |
| [台帳](ledger-overview.md) | Azure SQL Database の台帳機能を使用すると、監査人や他のビジネス関係者などの他者に対して、データが改ざんされていないことを暗号を使用した方法で証明することができます。 | 
| [メンテナンス期間](maintenance-window.md)| メンテナンス期間機能を使用して、Azure SQL Database のメンテナンス スケジュールを構成できます。 |
| [Azure portal のクエリ エディター](connect-query-portal.md) | ポータルのクエリ エディターを使用すると、[Azure portal](https://portal.azure.com) から Azure SQL Database に対してクエリを直接実行できます。|
| [クエリ ストアのヒント](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-current&preserve-view=true) | OPTION 句によるクエリの実行を最適化するには、クエリ ヒントを使用します。 |
| [SQL Analytics](../../azure-monitor/insights/azure-sql.md)|Azure SQL Analytics は、1 つのビューですべての Azure SQL Database のパフォーマンスを、複数のサブスクリプションにわたって大規模に監視するための先進のクラウド監視ソリューションです。 Azure SQL Analytics で組み込みのインテリジェンスを使用して重要なパフォーマンス メトリックを収集し、視覚化することによって、パフォーマンスのトラブルシューティングを行うことができます。|
| [SQL Insights](../../azure-monitor/insights/sql-insights-overview.md) |  SQL Insights は、Azure SQL ファミリのあらゆる製品を監視するための包括的なソリューションです。 SQL Insights では、動的管理ビューを使用して、正常性の監視、問題の診断、パフォーマンスの調整に必要なデータを公開します。|
| [汎用レベル向けのゾーン冗長構成](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | このゾーン冗長構成機能を選択すると、[Azure Availability Zones](../../availability-zones/az-overview.md#availability-zones) が利用され、Azure リージョン内の複数の物理的な場所にデータベースがレプリケートされます。 [ゾーン冗長](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)を選択すると、アプリケーション ロジックを変更することなく、データセンターの壊滅的な障害を含む、大規模な障害から汎用データベースやエラスティック プールを回復できるようになります。 現在、この機能は汎用レベルでのみ使用できます。 | 
|||

## <a name="general-availability-ga"></a>一般公開 (GA)

次の表に、過去 12 か月以内にプレビューから一般提供 (GA) に移行した Azure SQL Database の機能を示します。 

| 特徴量 | GA の月 | 詳細 |
| ---| --- |--- |
| [Azure Active Directory のみの認証](authentication-azure-ad-only-authentication.md) | 2021 年 11 月 | Azure Active Directory からの認証のみを許可するように Azure SQL Database を構成することができます。 | 
| [AAD サービス プリンシパル](authentication-aad-service-principal.md) |  2021 年 9 月 | Azure Active Directory (Azure AD) は、Azure AD アプリケーション (サービス プリンシパル) の代わりに Azure SQL Database でのユーザー作成をサポートします。| 
| [管理操作の監査](../database/auditing-overview.md#auditing-of-microsoft-support-operations) |  2021 年 3 月 | Azure SQL 監査機能を使用すると、サポート リクエスト中に SQL 資産にアクセスする必要がある場合、Microsoft サポート エンジニアが実行した操作を監査して、従業員の透明性を高めることができます。 | 
|||| 


## <a name="documentation-changes"></a>ドキュメントの変更点

Azure SQL Database ドキュメントに対する重要な変更点について説明します。

### <a name="november-2021"></a>2021 年 11 月

| [変更点] | 詳細 |
| --- | --- |
| **Azure AD のみの認証** | Azure SQL Database の認証を Azure Active Directory ユーザーのみに制限する機能が一般提供されました。 詳細については、[Azure AD のみの認証](../database/authentication-azure-ad-only-authentication.md)に関する記事を参照してください。 | 
| | | 


### <a name="october-2021"></a>2021 年 10 月

| [変更点] | 詳細 |
| --- | --- |
|**新機能の分割** | 以前、一緒になっていた、**新機能** に関する記事は、製品ごとに「[SQL Database の新機能](doc-changes-updates-release-notes-whats-new.md)」と「[SQL Managed Instance の新機能](../managed-instance/doc-changes-updates-release-notes-whats-new.md)」に分割されています。これにより、現在プレビュー段階の機能、一般提供されている機能、重要なドキュメントの変更点を簡単に確認できます。 さらに、[SQL Managed Instance での既知の問題](../managed-instance/doc-changes-updates-known-issues.md)に関するコンテンツが、独自のページに移動されました。  | 

### <a name="september-2021"></a>2021 年 9 月

| [変更点] | 詳細 |
| --- | --- |
| **可用性ゾーンのメンテナンス期間のサポート** | Azure SQL Database が可用性ゾーンにデプロイされている場合、[メンテナンス期間機能](maintenance-window.md)を使用できるようになりました。 現在、この機能はプレビュー段階にあります。  | 
|||


### <a name="july-2021"></a>2021 年 7 月

| [変更点] | 詳細 |
| --- | --- |
| **Azure AD のみの認証** | Azure SQL Database の認証を Azure Active Directory のユーザーのみに限定することができるようになりました。 現在、この機能はプレビュー段階にあります。 詳細については、[Azure AD のみの認証](authentication-azure-ad-only-authentication.md)に関する記事を参照してください。 | 
|||

### <a name="june-2021"></a>2021 年 6 月

| [変更点] | 詳細 |
| --- | --- |
| **クエリ ストアのヒント** | クエリ ヒントを使用して、OPTION 句によるクエリの実行を最適化できるようになりました。 現在、この機能はプレビュー段階にあります。 詳細については、「[クエリ ストアのヒント](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-current&preserve-view=true)」を参照してください。 | 
|||

### <a name="may-2021"></a>2021 年 5 月

| [変更点] | 詳細 |
| --- | --- |
| **変更データのキャプチャ** | Azure SQL Database での変更データ キャプチャ (CDC) の使用は現在プレビュー段階です。 詳細については、「[変更データ キャプチャ](/sql/relational-databases/track-changes/about-change-data-capture-sql-server)」を参照してください。 | 
| **SQL Database 台帳** | SQL Database 台帳はプレビュー段階であり、監査人や他のビジネス関係者などの他者に対して、データが改ざんされていないことを暗号を使用した方法で証明できる機能が導入されました。 詳細については、[台帳](ledger-overview.md)に関する記事を参照してください。 | 
|||

### <a name="march-2021"></a>2021 年 3 月

| [変更点] | 詳細 |
| --- | --- |
 | **メンテナンス期間** | メンテナンス期間機能を使用して、Azure SQL Database のメンテナンス スケジュールを構成できます。これは現在プレビューの段階です。 詳細については、「[メンテナンス期間](maintenance-window.md)」を参照してください。|
| **SQL Insights** | SQL Insights は、Azure SQL ファミリのあらゆる製品を監視するための包括的なソリューションです。 SQL Insights では、動的管理ビューを使用して、正常性の監視、問題の診断、パフォーマンスの調整に必要なデータを公開します。 詳細については、[SQL Insights](../../azure-monitor/insights/sql-insights-overview.md) に関する記事を参照してください。 | 
||| 

## <a name="contribute-to-content"></a>コンテンツの改善への協力

Azure SQL のドキュメントに投稿するには、[Docs 共同作成者ガイド](/contribute/)に関する記事をご覧ください。
