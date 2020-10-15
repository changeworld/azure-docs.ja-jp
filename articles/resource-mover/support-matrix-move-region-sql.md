---
title: Azure Resource Mover を使用して Azure SQL リソースをリージョン間で移動するためのサポート。
description: Azure Resource Mover を使用して Azure SQL リソースをリージョン間で移動するためのサポートを確認します。
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 4925f6ffd2383c21f8ff9b0e3196d44fc15bb657
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "89652652"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Azure SQL リソースをリージョン間で移動するためのサポート

この記事では、Azure Resource Mover を使用して Azure SQL リソースを Azure リージョン間で移動するためのサポートと前提条件について説明します。

## <a name="requirements"></a>必要条件

要件の概要については、次の表で説明しています。

**機能** | **サポートされるかどうか** | **詳細**
--- | --- | ---
**Azure SQL Database Hyperscale** | サポートされていません | Resource Mover を使用して Azure SQL Hyperscale サービス レベルでデータベースを移動することはできません。
**ゾーン冗長性** | サポートされています |  サポートされている移動オプション:<br/><br/> - ゾーン冗長性をサポートしているリージョン間。<br/><br/> - ゾーン冗長性をサポートしていないリージョン間。<br/><br/> - ゾーン冗長性をサポートしているリージョンからゾーン冗長性をサポートしていないリージョン間。<br/><br/> - ゾーン冗長性をサポートしていないリージョンからゾーン冗長性をサポートしているリージョン間。 
**データ同期** | ハブ/同期データベース:サポートされていません<br/><br/> 同期メンバー:サポートされています。 | 同期メンバーが移動される場合は、新しいターゲット データベースへのデータ同期を設定する必要があります。
**既存の geo レプリケーション** | サポートされています | 既存の geo レプリカは、ターゲット リージョンの新しいプライマリに再マップされます。<br/><br/> 移動後にシード処理を初期化する必要があります。 [詳細情報](/azure/sql-database/sql-database-active-geo-replication-portal)
**Bring Your Own Key (BYOK) による Transparent Data Encryption (TDE)** | サポートされています | キー コンテナーのリージョン間の移動については、[こちら](../key-vault/general/move-region.md)を参照してください。
**サービス マネージド キーを使用する TDE** | サポートされています。 |  キー コンテナーのリージョン間の移動については、[こちら](../key-vault/general/move-region.md)を参照してください。
**動的データ マスク ルール** | サポートされています。 | ルールは、移動の一部としてターゲット リージョンに自動的にコピーされます。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started-portal)。
**Advanced Data Security** | サポートされていません。 | 対処法:ターゲット リージョンで SQL Server レベルで設定します。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)。
**ファイアウォール規則** | サポートされていません。 | 対処法:ターゲット リージョンで SQL Server 用のファイアウォール規則を設定します。 データベースレベルのファイアウォール規則は、ソース サーバーからターゲット サーバーにコピーされます。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-server-level-firewall-rule)。
**監査ポリシー** | サポートされていません。 | 移動後に、ポリシーは既定値にリセットされます。 再設定方法については、[こちら](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)を参照してください。
**バックアップ保有期間** | サポートされています。 | ソース データベースのバックアップ保有ポリシーは、ターゲット データベースに引き継がれます。 移動後の設定の変更方法については、[こちら](/azure/sql-database/sql-database-long-term-backup-retention-configure)を参照してください。
**自動チューニング** | サポートされていません。 | 対処法:移動後に、自動チューニング設定を行います。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-automatic-tuning-enable)。
**データベースのアラート** | サポートされていません。 | 対処法:移動後に、アラートを設定します。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal)。
**Azure SQL Server 拡張データベース** | サポートされていません | Resource Mover を使用して SQL Server 拡張データベースを移動することはできません。
**Azure Synapse Analytics** | サポートされていません | Resource Mover を使用して Azure Synapse Analytics (旧称 Azure SQL Data Warehouse) を移動することはできません。
## <a name="next-steps"></a>次の手順

Resource Mover を使用して [Azure SQL リソース](tutorial-move-region-sql.md)を別のリージョンに移動してみる