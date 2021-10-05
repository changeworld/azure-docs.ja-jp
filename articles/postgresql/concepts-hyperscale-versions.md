---
title: サポートされているバージョン – Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL で使用できる PostgreSQL バージョン - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 8dd0929d56bac0b2f825d976ba07bd0956c0d063
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755206"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL でサポートされているデータベースのバージョン – Hyperscale (Citus)

## <a name="postgresql-versions"></a>PostgreSQL のバージョン

Hyperscale (Citus) サーバー グループで実行されている PostgreSQL のバージョンは、作成時にカスタマイズできます。 Hyperscale (Citus) では現在、次のメジャー バージョンがサポートされています。

### <a name="postgresql-version-13"></a>PostgreSQL バージョン 13

現在のマイナー リリースは 13.4 です。 このマイナー リリースの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/13/release-13-4.html)を参照してください。

### <a name="postgresql-version-12"></a>PostgreSQL バージョン 12

現在のマイナー リリースは 12.8 です。 このマイナー リリースの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/12/release-12-8.html)を参照してください。

### <a name="postgresql-version-11"></a>PostgreSQL バージョン 11

現在のマイナー リリースは 11.13 です。 このマイナー リリースの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/11/release-11-13.html)を参照してください。

### <a name="postgresql-version-10-and-older"></a>PostgreSQL バージョン 10 以前

Azure Database for PostgreSQL - Hyperscale (Citus) では、PostgreSQL バージョン 10 以前はサポートされていません。

## <a name="citus-and-other-extension-versions"></a>Citus およびその他拡張機能のバージョン

サーバー グループで実行されている PostgreSQL のバージョンによっては、異なる[バージョンの Postgres 拡張機能](concepts-hyperscale-extensions.md)もインストールされます。  特に、Postgres 13 には Citus 10 が付属しており、以前の Postgres バージョンには Citus 9.5 が付属しています。

## <a name="next-steps"></a>次のステップ

* どの[拡張機能](concepts-hyperscale-extensions.md)がどのバージョンにインストールされているかを確認してください。
* [Hyperscale (Citus) サーバー グループを作成する方法](quickstart-create-hyperscale-portal.md)について確認します。
