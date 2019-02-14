---
title: Azure SQL Database (プレビュー) での Machine Learning Services と R に関する主な違いの概要
description: このトピックでは、Azure SQL Database Machine Learning Services と R および SQL Server Machine Learning Services の主な違いについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 01/31/2019
ms.openlocfilehash: 4350fb0e75f140e120ba6cd2f074ffa1816a8fce
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237486"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-and-sql-server"></a>Azure SQL Database と SQL Server の Machine Learning Services の主な違い

Azure SQL Database における Machine Learning Services と R の機能は、[SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning) と似ています。 これらの主な違いをいくつか以下に示します。

## <a name="language-support"></a>言語のサポート

SQL Server では、[拡張性フレームワーク](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)を介して、R と Python がサポートされます。 SQL Database では、両方の言語がサポートされません。 主な違いは次のとおりです。

- SQL Database でサポートされている言語は R のみです。 現時点では、Python はサポートされていません。
- R のバージョンは 3.4.4 です。
- `sp_configure` で `external scripts enabled` を構成する必要はありません。 [サインアップ](sql-database-machine-learning-services-overview.md#signup)したら、SQL データベースに対して機械学習が有効になります。

## <a name="package-management"></a>パッケージの管理

R パッケージの管理とインストールの動作は、SQL Database と SQL Server で異なります。 これらの違いは次のとおりです。

- R パッケージは、[sqlmlutils](https://github.com/Microsoft/sqlmlutils) または [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) を使用してインストールされます。
- パッケージでアウトバウンド ネットワークを呼び出すことはできません。 この制限は、SQL Server での [Machine Learning Services の既定のファイアウォール規則](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration)に似ていますが、SQL Database で変更することはできません。
- 外部ランタイム (Java など) に依存するか、インストールまたは使用のための OS API へのアクセスが必要なパッケージはサポートされません。

## <a name="resource-governance"></a>リソース ガバナンス

[Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) と外部リソース プールを使用して、R リソースを制限することはできません。 R リソースは SQL Database リソースの割合であり、選択するサービス レベルによって異なります。 詳細については、「[Azure SQL Database purchasing models](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)」 (Azure SQL Database の購入モデル) を参照してください。

## <a name="security-isolation"></a>セキュリティ分離

Azure SQL Database では、SQLPAL (SQL Platform Abstraction Layer) によって外部プロセスの分離が提供されます。 この分離では、R スクリプトを実行するためのセキュリティの追加レイヤーが提供されます。

## <a name="next-steps"></a>次の手順

- 一般的な情報について、[SQL Server Machine Learning サービス](https://docs.microsoft.com/sql/advanced-analytics)のドキュメントを参照します
- Azure SQL Database で Machine Learning Services と R を使用する方法について、[クイック スタート ガイド](sql-database-connect-query-r.md)を参照します。
- [SQL Server R 言語のチュートリアル](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)で詳細を確認します