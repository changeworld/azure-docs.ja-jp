---
title: サポートされているバージョン – Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL で使用できる PostgreSQL バージョン - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: d8a584b6ba752e8f9220defa575f519828ba07e6
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023845"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL でサポートされているデータベースのバージョン – Hyperscale (Citus)

## <a name="postgresql-versions"></a>PostgreSQL のバージョン

> [!IMPORTANT]
> Hyperscale (Citus) でカスタマイズ可能な PostgreSQL バージョンは現在プレビュー中です。  このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
>
> その他の新機能については、[Hyperscale (Citus) のプレビュー機能](hyperscale-preview-features.md)に関するページで全一覧をご覧いただけます。

Hyperscale (Citus) サーバー グループで実行されている PostgreSQL のバージョンは、作成時にカスタマイズできます。 11 以外のバージョンを選択することは、現在プレビュー機能です。

Hyperscale (Citus) では現在、次のメジャー バージョンがサポートされています。

### <a name="postgresql-version-13-preview"></a>PostgreSQL バージョン 13 (プレビュー)

現在のマイナー リリースは 13.2 です。 このマイナー リリースの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/13/static/release-13-2.html)を参照してください。

### <a name="postgresql-version-12-preview"></a>PostgreSQL バージョン 12 (プレビュー)

現在のマイナー リリースは 12.6 です。 このマイナー リリースの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/12/static/release-12-6.html)を参照してください。

### <a name="postgresql-version-11"></a>PostgreSQL バージョン 11

現在のマイナー リリースは 11.11 です。 このマイナー リリースの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/11/static/release-11-11.html)を参照してください。

### <a name="postgresql-version-10-and-older"></a>PostgreSQL バージョン 10 以前

Azure Database for PostgreSQL - Hyperscale (Citus) では、PostgreSQL バージョン 10 以前はサポートされていません。

## <a name="citus-and-other-extension-versions"></a>Citus およびその他拡張機能のバージョン

サーバー グループで実行されている PostgreSQL のバージョンによっては、異なる[バージョンの Postgres 拡張機能](concepts-hyperscale-extensions.md)もインストールされます。  特に、Postgres 13 には Citus 10 が付属しており、以前の Postgres バージョンには Citus 9.5 が付属しています。

## <a name="next-steps"></a>次のステップ

* どの[拡張機能](concepts-hyperscale-extensions.md)がどのバージョンにインストールされているかを確認してください。
* [Hyperscale (Citus) サーバー グループを作成する方法](quickstart-create-hyperscale-portal.md)について確認します。
