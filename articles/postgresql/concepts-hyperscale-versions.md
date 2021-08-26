---
title: サポートされているバージョン – Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL で使用できる PostgreSQL バージョン - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 299005d8d7c961335decbce46bab8c558d2589a1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734977"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL でサポートされているデータベースのバージョン – Hyperscale (Citus)

## <a name="postgresql-versions"></a>PostgreSQL のバージョン

Hyperscale (Citus) サーバー グループで実行されている PostgreSQL のバージョンは、作成時にカスタマイズできます。 Hyperscale (Citus) では現在、次のメジャー バージョンがサポートされています。

### <a name="postgresql-version-13"></a>PostgreSQL バージョン 13

現在のマイナー リリースは 13.3 です。 このマイナー リリースの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/13/static/release-13-2.html)を参照してください。

### <a name="postgresql-version-12"></a>PostgreSQL バージョン 12

現在のマイナー リリースは 12.7 です。 このマイナー リリースの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/12/static/release-12-6.html)を参照してください。

### <a name="postgresql-version-11"></a>PostgreSQL バージョン 11

現在のマイナー リリースは 11.12 です。 このマイナー リリースの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/11/static/release-11-11.html)を参照してください。

### <a name="postgresql-version-10-and-older"></a>PostgreSQL バージョン 10 以前

Azure Database for PostgreSQL - Hyperscale (Citus) では、PostgreSQL バージョン 10 以前はサポートされていません。

## <a name="citus-and-other-extension-versions"></a>Citus およびその他拡張機能のバージョン

サーバー グループで実行されている PostgreSQL のバージョンによっては、異なる[バージョンの Postgres 拡張機能](concepts-hyperscale-extensions.md)もインストールされます。  特に、Postgres 13 には Citus 10 が付属しており、以前の Postgres バージョンには Citus 9.5 が付属しています。

## <a name="next-steps"></a>次のステップ

* どの[拡張機能](concepts-hyperscale-extensions.md)がどのバージョンにインストールされているかを確認してください。
* [Hyperscale (Citus) サーバー グループを作成する方法](quickstart-create-hyperscale-portal.md)について確認します。
