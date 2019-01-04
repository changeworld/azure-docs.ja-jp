---
title: Azure SQL Database を構成する方法 | Microsoft Docs
description: Azure SQL Database を構成して管理する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: b4dd21324591075d7625a82fbbb661c4a8e84b1d
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53439539"
---
# <a name="how-to-use-azure-sql-database"></a>Azure SQL Database を使用する方法

このセクションでは、お客様の Azure SQL データベースを管理して構成するのに役立つさまざまなガイド、スクリプト、説明をご紹介します。 また、[Single Database](sql-database-howto-single-database.md) と [Managed Instance](sql-database-howto-managed-instance.md) については個別の攻略ガイドもあります。

## <a name="load-data"></a>データを読み込む

- [Azure 内での単一データベースのコピー](https://docs.microsoft.com/azure/sql-database/sql-database-copy)
- [BACPAC からの DB のインポート](https://docs.microsoft.com/azure/sql-database/sql-database-import)
- [BACPAC への DB のエクスポート](https://docs.microsoft.com/azure/sql-database/sql-database-export)
- [BCP を使用したデータの読み込み](https://docs.microsoft.com/azure/sql-database/sql-database-load-from-csv-with-bcp)
- [ADF を使用したデータの読み込み](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>データ同期

- [SQL データ同期](https://docs.microsoft.com/azure/sql-database/sql-database-sync-data)
- [データ同期エージェント](https://docs.microsoft.com/azure/sql-database/sql-database-data-sync-agent)
- [スキーマ変更のレプリケート](https://docs.microsoft.com/azure/sql-database/sql-database-update-sync-schema)
- [OMS で監視する](https://docs.microsoft.com/azure/sql-database/sql-database-sync-monitor-oms)
- [データ同期のベスト プラクティス](https://docs.microsoft.com/azure/sql-database/sql-database-best-practices-data-sync)
- [データ同期のトラブルシューティング](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-data-sync)

## <a name="monitoring-and-tuning"></a>監視とチューニング

-  [手動チューニング](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
- [DMV を使用したパフォーマンスの監視](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
- [クエリ ストアを使用したパフォーマンスの監視](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)
- [Intelligent Insights を使用したパフォーマンスのトラブルシューティング](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance)
- [Intelligent Insights 診断ログの使用](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-use-diagnostics-log)
- [インメモリ OLTP スペースの監視](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring)

### <a name="extended-events"></a>拡張イベント

- [拡張イベント](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-db-diff-from-svr)
- [イベント ファイルへの拡張イベントの格納](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-event-file)
- [リング バッファーへの拡張イベントの格納](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-ring-buffer)

## <a name="configure-features"></a>機能の構成

- [Azure AD 認証の構成](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)
- [条件付きアクセスの構成](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- [多要素 AAD 認証](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication)
- [多要素認証の構成](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication-configure)
- [一時的なアイテム保持ポリシーの構成](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables-retention-policy)
- [BYOK を使用した TDE の構成](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-configure)
- [TDE BYOK キーの回転](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-key-rotation)
- [TDE 保護機能の削除](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)
- [インメモリ OLTP の構成](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-migration)
- [Azure Automation の構成](https://docs.microsoft.com/azure/sql-database/sql-database-manage-automation)

## <a name="develop-applications"></a>アプリケーションを開発する

- [接続](https://docs.microsoft.com/azure/sql-database/sql-database-libraries)
- [Spark コネクタの使用](https://docs.microsoft.com/azure/sql-database/sql-database-spark-connector)
- [アプリの認証](https://docs.microsoft.com/azure/sql-database/sql-database-client-id-keys)
- [エラー メッセージ](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)
- [バッチ処理を使用したパフォーマンスの向上](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)
- [接続のガイダンス](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)
- [DNS エイリアス](https://docs.microsoft.com/azure/sql-database/dns-alias-overview)
- [DNS エイリアスの PowerShell の設定](https://docs.microsoft.com/azure/sql-database/dns-alias-powershell)
- [ポート - ADO.NET](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12)
- [C と C++](https://docs.microsoft.com/azure/sql-database/sql-database-develop-cplusplus-simple)
- [Excel](https://docs.microsoft.com/azure/sql-database/sql-database-connect-excel)

## <a name="design-applications"></a>アプリケーションの設計

- [ディザスター リカバリーの設計](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery)
- [エラスティック プールの設計](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool)
- [アプリのアップグレードの設計](https://docs.microsoft.com/azure/sql-database/sql-database-manage-application-rolling-upgrade)

### <a name="design-multi-tenant-saas-applications"></a>マルチテナント SaaS アプリケーションの設計

- [SaaS の設計パターン](https://docs.microsoft.com/azure/sql-database/saas-tenancy-app-design-patterns)
- [SaaS Video Indexer](https://docs.microsoft.com/azure/sql-database/saas-tenancy-video-index-wingtip-brk3120-20171011)
- [SaaS アプリのセキュリティ](https://docs.microsoft.com/azure/sql-database/saas-tenancy-elastic-tools-multi-tenant-row-level-security)

## <a name="next-steps"></a>次の手順
- [Managed Instance の攻略ガイド](sql-database-howto-managed-instance.md)について詳しく確認する。
- [Single Database の攻略ガイド](sql-database-howto-single-database.md)について詳しく確認する。
