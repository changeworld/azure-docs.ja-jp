---
title: Azure Database for PostgreSQL のサポートされているバージョン - Single Server
description: Azure Database for PostgreSQL のサポートされているバージョン - Single Server について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2a2b8b71e07e5dac74d73d3a81c150ac5d980ea2
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935348"
---
# <a name="supported-postgresql-database-versions"></a>サポートされている PostgreSQL Database バージョン
Microsoft では、Azure Database for PostgreSQL - Single Server での PostgreSQL エンジンの n-2 バージョンのサポートを予定しています。 バージョンは、Azure の現在のメジャー バージョン (n) とその前の 2 つのメジャー バージョン (-2) です。

Azure Database for PostgreSQL では現在、次のメジャー バージョンがサポートされています。

## <a name="postgresql-version-11"></a>PostgreSQL バージョン 11
現行のマイナー バージョンは 11.5 です。 このマイナー バージョンの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/11/static/release-11-5.html)を参照してください。

## <a name="postgresql-version-10"></a>PostgreSQL バージョン 10
現行のマイナー バージョンは 10.10 です。 このマイナー バージョンの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/10/static/release-10-10.html)を参照してください。

## <a name="postgresql-version-96"></a>PostgreSQL バージョン 9.6
現行のマイナー バージョンは 9.6.15 です。 このマイナー バージョンの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html)を参照してください。

## <a name="postgresql-version-95"></a>PostgreSQL バージョン 9.5
現行のマイナー バージョンは 9.5.19 です。 このマイナー バージョンの機能強化と修正については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html)を参照してください。

## <a name="managing-upgrades"></a>アップグレードの管理
Azure Database for PostgreSQL では、マイナー バージョンのアップグレードは自動管理されます。 

メジャー バージョンの自動アップグレードはサポートされていません。 たとえば、PostgreSQL 9.5 から PostgreSQL 9.6 への自動アップグレードはありません。 次のメジャー バージョンにアップグレードするには、新しいエンジンのバージョンで作成されたサーバーに[データベースのダンプと復元](./howto-migrate-using-dump-and-restore.md)を作成します。

### <a name="version-syntax"></a>バージョンの構文
PostgreSQL バージョン 10 より前は、[PostgreSQL のバージョン管理ポリシー](https://www.postgresql.org/support/versioning/)では、1 番目_または_ 2 番目の番号が増えることが_メジャー バージョン_のアップグレードと見なされていました。 たとえば、9.5 から 9.6 への変更は、_メジャー_ バージョンのアップグレードと見なされていました。 バージョン 10 以降は、1 番目の番号の変更のみがメジャー バージョンのアップグレードと見なされます。 たとえば、10.0 から 10.1 への変更は、_マイナー_ バージョンのアップグレードになります。 バージョン 10 から 11 への変更が、_メジャー_ バージョンのアップグレードになります。

## <a name="next-steps"></a>次の手順
サポートされている PostgreSQL 拡張機能の詳細については、[拡張機能に関するドキュメント](concepts-extensions.md)を参照してください。
