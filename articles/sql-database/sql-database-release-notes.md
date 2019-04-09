---
title: Azure SQL Database リリース ノート | Microsoft Docs
description: Azure SQL Database サービスの新機能と機能強化、および Azure SQL Database のドキュメントの改善について説明します
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: carlrab
ms.openlocfilehash: 6600a578ba9c73c8a2c71466fd0b008f19058b80
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57861293"
---
# <a name="sql-database-release-notes"></a>SQL Database リリース ノート

この記事では、SQL Database サービスの新機能と機能強化、および SQL Database のドキュメントの改善について説明します。 SQL Database サービスの機能強化については、[SQL Database サービスの更新情報](https://azure.microsoft.com/updates/?product=sql-database)も参照してください。 その他の Azure サービスの機能強化については、[サービスの更新情報](https://azure.microsoft.com/updates)を参照してください。

## <a name="march-2019"></a>2019 年 3 月

### <a name="service-improvements"></a>サービスの機能強化

| サービスの機能強化 | 詳細 |
| --- | --- |
| 近日対応予定 ||
| &nbsp; |

### <a name="documentation-improvements"></a>ドキュメントの改善

| ドキュメントの改善 | 詳細 |
| --- | --- |
| 単一データベースに関するログの制限が追加されました|詳細については、[単一データベースに対する仮想コアのリソース制限](sql-database-vcore-resource-limits-single-databases.md)に関する記事をご覧ください。|
| エラスティック プールとプールされたデータベースに関するログの制限が追加されました|詳細については、[エラスティック プールに対する仮想コアのリソース制限](sql-database-vcore-resource-limits-elastic-pools.md)に関する記事をご覧ください。|
| トランザクション ログ速度ガバナンスが追加されました| [トランザクション ログ速度ガバナンス](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)に関する新しいコンテンツが追加されました。|
| 単一データベースとエラスティック プールで az.sql モジュールを使用する PowerShell サンプルが更新されました | 詳細については、[単一データベースとエラスティック プール用の PowerShell サンプル](sql-database-powershell-samples.md#single-database-and-elastic-pools)を参照してください。|
| &nbsp; |

## <a name="february-2019"></a>2019 年 2 月

### <a name="service-improvements"></a>サービスの機能強化

| サービスの機能強化 | 詳細 |
| --- | --- |
|再開可能なオンライン インデックスの作成機能が一般提供になりました| 詳細については、[インデックスの作成](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)に関する記事をご覧ください。|
|ルート テーブルでのマネージド インスタンスのサポートが改善されました| 詳細については、「[ネットワークの要件](sql-database-managed-instance-connectivity-architecture.md#network-requirements)」を参照してください。|
|マネージド インスタンスでデータベース名の変更がサポートされるようになりました | 詳細については、[ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) および [sp_rename](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql) 構文を参照してください。|
|SQL Database を Stream Analytics 用の参照データのソースとして使用できるようになりました。 | 詳細については、[Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) に関するページを参照してください。|
|Data Migration Assistance に、マネージド インスタンスのサポートが追加されました。 |詳細については、「[DMA の新機能](https://docs.microsoft.com/sql/dma/dma-whatsnew)」を参照してください。|
|SQL Server Migration Assistant に、マネージド インスタンスに対するターゲット対応性評価のサポートが追加されました。 | 詳細については、「[SQL Server Migration Assistant](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant)」を参照してください。
|データ移行サービスで、Amazon RDS からマネージド インスタンスへの移行がサポートされるようになりました | 詳細については、[チュートリアル: DMS を使用して RDS SQL Server を Azure SQL Database または Azure SQL Database マネージド インスタンスにオンラインで移行する](../dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md)」を参照してください。|
| &nbsp; |

### <a name="documentation-improvements"></a>ドキュメントの改善

| ドキュメントの改善 | 詳細 |
| --- | --- |
|マネージド インスタンスのデプロイ オプションに関する説明を追加しました|単一データベース、エラスティック プール、マネージド インスタンスのデプロイ オプションに適用できることを明確にするため、多くの記事を更新しました。 |
|DTU ベースの購入モデルの tempdb のサイズを更新しました | 詳細については、「[SQL Database の Tempdb データベース](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)」を参照してください。|
|BACPAC ファイルによるマネージド インスタンスのインポートとエクスポートのサポートが更新されました| 詳細については、[BACPAC からのインポート](sql-database-import.md)および [BACPAC へのエクスポート](sql-database-export.md)に関する記事をご覧ください。 |
| &nbsp; |


## <a name="january-2019"></a>2019 年 1 月

### <a name="service-improvements"></a>サービスの機能強化

| サービスの機能強化 | 詳細 |
| --- | --- |
| コンピューティング リソースに関する細分性のオプションが追加されました | [単一データベース](sql-database-vcore-resource-limits-single-databases.md)および[エラスティック プール](sql-database-vcore-resource-limits-elastic-pools.md)の General Purpose および Business Critical サービス レベルに、細分性の細かいコンピューティング オプションが追加されました。|
| Azure portal でのマネージド インスタンスの監査レコードの表示 | Azure portal で[マネージド インスタンスの監査レコード](sql-database-managed-instance-auditing.md)を表示できるようになりました。|
| 高度な脅威検出機能の名前が Advanced Data Security に変更されました | 単一データベース、エラスティック プール、マネージド インスタンスの高度な脅威検出機能の名前が [Advanced Data Security](sql-advanced-threat-protection.md) に変更されました。 |
| &nbsp; |

### <a name="documentation-improvements"></a>ドキュメントの改善

| ドキュメントの改善 | 詳細 |
| --- | --- |
| マネージド インスタンスとトランザクション レプリケーション | [トランザクション レプリケーションとマネージド インスタンス](replication-with-sql-database-managed-instance.md)の併用に関する記事が追加されました |
| Azure AD とマネージド インスタンスのチュートリアルが追加されました | この [Azure AD とマネージド インスタンス](sql-database-managed-instance-aad-security-tutorial.md)のチュートリアルでは、Azure AD のログインを使用してマネージド インスタンスのセキュリティを構成してテストする必要があることを示します。 |
| Transact-SQL スクリプトを使用したジョブ自動化に関するコンテンツが更新されました | 単一データベース、エラスティック プール、マネージド インスタンスの [Transact-SQL スクリプトによるジョブの自動化](sql-database-job-automation-overview.md)の使用に関するコンテンツが更新および明確化されました |
| マネージド インスタンスのセキュリティに関するコンテンツが更新されました | [マネージド インスタンスのセキュリティ モデル](sql-database-security-overview.md)に関するコンテンツが更新および明確化され、単一データベースとエラスティック プールのセキュリティ モデルと比較されています |
| すべてのクイック スタートとチュートリアルが更新されました | [ドキュメント](https://docs.microsoft.com/azure/sql-database)内のすべてのクイック スタートとチュートリアルが Azure portal の変更に合わせて更新されました |
| クイック スタートの概要ガイドが追加されました | [単一データベース](sql-database-quickstart-guide.md)と[マネージド インスタンス](sql-database-managed-instance-quickstart-guide.md)のクイック スタートの概要ガイドが追加されました |
| SQL Database 用語集が追加されました | この[用語集](sql-database-glossary-terms.md)には、SQL Database の用語の最終的な一覧と、本文中で用語を説明している主な概念ページへのリンクが含まれています。 |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>コンテンツの改善に協力する

Azure SQL のドキュメントはオープン ソースです。 公開された環境での作業には、さまざまな利点があります。

- オープン ソース リポジトリでは、どのドキュメントが最も必要かに関するフィードバックを公に募集する計画です。
- オープンソース リポジトリは公開された環境でレビューされ、最初のリリースで最も有用なコンテンツが公開されます。
- オープンソース リポジトリは公開された環境で更新され、コンテンツの継続的な改善が簡単にできるようになります。

Azure SQL Database のドキュメント コンテンツに協力するには、「[Microsoft Docs 共同作成者ガイド概要](https://docs.microsoft.com/contribute/)」を参照してください。 [docs.microsoft.com](https://docs.microsoft.com/) のユーザー エクスペリエンスが [GitHub](https://github.com/) のワークフローに直接統合され、さらに簡単になりました。 まず、[表示中のドキュメントを編集](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents)してみましょう。 [新しいトピックのレビュー](https://docs.microsoft.com/contribute/#review-open-prs)や[品質の Issue の作成](https://docs.microsoft.com/contribute/#create-quality-issues)によって協力することもできます。
