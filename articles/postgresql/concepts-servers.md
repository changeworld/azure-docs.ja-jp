---
title: "Azure Database for PostgreSQL のサーバーの概念 | Microsoft Docs"
description: "このトピックでは、Azure Database for PostgreSQL サーバーを操作するための考慮事項とガイドラインを示します。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e14712b8fd68d6364f44c116448a9a8f33622a91
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---
# <a name="azure-database-for-postgresql-servers"></a>Azure Database for PostgreSQL サーバー

このトピックでは、Azure Database for PostgreSQL サーバーを操作するための考慮事項とガイドラインを示します。

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーとは

Azure Database for PostgreSQL サーバーは、複数のデータベースの中央管理ポイントです。 これは、オンプレミスで一般的な PostgreSQL サーバー コンストラクトと同じです。 具体的には、PostgreSQL サービスが管理され、パフォーマンスが保証されるほか、アクセスと機能がサーバー レベルで公開されます。

Azure Database for PostgreSQL サーバーの特徴を次に示します。

- Azure サブスクリプション内で作成されます。
- データベースの親リソースです。
- データベースに名前空間を提供します。
- 強力な有効期間のセマンティクスが含まれるコンテナーです。サーバーを削除すると、包含データベースが削除されます。
- リージョンにリソースを併置します。
- サーバーおよびデータベース アクセスの接続エンドポイントを提供します (.postgresql.database.azure.com)。
- データベースに適用される管理ポリシーのスコープ (ログイン、ファイアウォール、ユーザー、ロール、構成など) を提供します。
- 複数のバージョンで使用できます。 詳細については、[サポートされる PostgreSQL データベース バージョン](concepts-supported-versions.md)に関するページをご覧ください。
- ユーザーが拡張できます。 詳細については、[PostgreSQL の拡張機能](concepts-extensions.md)に関するページをご覧ください。

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーへの接続および認証方法

次の要素が、データベースへの安全なアクセスを確保するうえで役に立ちます。

|||
| :-- | :-- |
| **認証と権限承認** | Azure Database for PostgreSQL サーバーは、ネイティブ PostgreSQL 認証をサポートしています。 サーバーにはサーバーの管理者ログインで接続し、認証できます。<br />詳細については、[Azure Database for PostgreSQL でのユーザーとロールの管理](/azure/sql-database/sql-database-manage-logins)に関するページをご覧ください。 |
| **プロトコル** | サービスは、PostgreSQL で使用されるメッセージ ベースのプロトコルをサポートしています。 |
| **TCP/IP** | プロトコルは、TCP/IP および UNIX ドメイン ソケット経由でサポートされます。 |
| **ファイアウォール** | データを保護するため、ファイアウォール規則は、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーまたはそのデータベースへのすべてのアクセスを遮断します。 「[Azure Database for PostgreSQL サーバーのファイアウォール規則](concepts-firewall-rules.md)」を参照してください。 |
|||

## <a name="how-do-i-manage-a-server"></a>サーバーの管理方法

Azure Database for PostgreSQL サーバーを管理するには、Azure Portal または Azure CLI を使用します。

## <a name="next-steps"></a>次のステップ

- サービスの概要については、[Azure Database for PostgreSQL の概要](overview.md)に関するページをご覧ください
- **サービス レベル**に基づく特定のリソース クォータと制限については、[サービス レベル](concepts-service-tiers.md)に関するページをご覧ください
- サービスへの接続については、「[Azure Database for PostgreSQL の接続ライブラリ](concepts-connection-libraries.md)」を参照してください。

