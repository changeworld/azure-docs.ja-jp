---
title: "Azure Database for PostgreSQL のサーバーの概念 | Microsoft Docs"
description: "このトピックでは、Azure Database for PostgreSQL サーバーを構成および管理するための考慮事項とガイドラインを示します。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: a1008936c053316630360403be688e4eedc8b2c0
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2017
---
# <a name="azure-database-for-postgresql-servers"></a>Azure Database for PostgreSQL サーバー
この記事では、Azure Database for PostgreSQL サーバーを操作するための考慮事項とガイドラインを示します。

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
- 複数のバージョンで使用できます。 詳しくは、「[サポートされている PostgreSQL Database バージョン](concepts-supported-versions.md)」をご覧ください。
- ユーザーが拡張できます。 詳細については、[PostgreSQL の拡張機能](concepts-extensions.md)に関するページをご覧ください。

Azure Database for PostgreSQL サーバー内では、1 つ以上のデータベースを作成できます。 サーバーごとに 1 つのデータベースを作成してすべてのリソースを利用するか、複数のデータベースを作成してリソースを共有することができます。 価格は、価格レベル、コンピューティング ユニット、ストレージ (GB) の構成に基づき、サーバーごとに構造化されています。 詳細については、「[価格レベル](./concepts-service-tiers.md)」をご覧ください。

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーへの接続および認証方法
次の要素が、データベースへの安全なアクセスを確保するうえで役に立ちます。

| :-- | :-- | | **認証と承認** | Azure Database for PostgreSQL サーバーは、ネイティブ PostgreSQL 認証をサポートしています。 サーバーにはサーバーの管理者ログインで接続し、認証できます。 | | **プロトコル** | サービスは、PostgreSQL で使用されるメッセージ ベースのプロトコルをサポートしています。 | | **TCP/IP** | プロトコルは、TCP/IP および UNIX ドメイン ソケット経由でサポートされます。 | | **ファイアウォール** | データを保護するため、ファイアウォール規則は、どのコンピューターに権限を持たせるかを指定するまで、使用中のサーバーおよびそのデータベースへのすべてのアクセスを遮断します。 「[Azure Database for PostgreSQL サーバーのファイアウォール規則](concepts-firewall-rules.md)」を参照してください。 |

## <a name="how-do-i-manage-a-server"></a>サーバーの管理方法
Azure Database for PostgreSQL サーバーを管理するには、[Azure Portal](https://portal.azure.com) または [Azure CLI](/cli/azure/postgres) を使用します。

## <a name="server-parameters"></a>サーバー パラメーター
PostgreSQL サーバー パラメーターは、サーバーの構成を決定します。 Azure Database for PostgreSQL では、Azure Portal または Azure CLI を介してパラメーターの一覧を表示および編集できます。 

Postgres の管理対象サービスとして、Azure Database for PostgreSQL 内の構成可能なパラメーターはローカル Postgres インスタンス内のパラメーターのサブセットです (Postgres パラメーターについて詳しくは、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/9.6/static/runtime-config.html)をご覧ください)。 Azure Database for PostgreSQL サーバーは、作成時に各パラメーターに対して既定値で有効化されます。 変更を反映するためにサーバーの再起動やスーパー ユーザーのアクセス権が必要なパラメーターは、ユーザーが構成することはできません。


## <a name="next-steps"></a>次のステップ
- サービスの概要については、[Azure Database for PostgreSQL の概要](overview.md)に関するページをご覧ください。
- **サービス レベル**に基づく特定のリソース クォータと制限については、[サービス レベル](concepts-service-tiers.md)に関するページをご覧ください。
- サービスへの接続については、「[Azure Database for PostgreSQL の接続ライブラリ](concepts-connection-libraries.md)」を参照してください。
- [Azure Portal](howto-configure-server-parameters-using-portal.md) または [Azure CLI](howto-configure-server-parameters-using-cli.md) でサーバー パラメーターを表示および編集します。
