---
title: Azure Database for PostgreSQL のサポートされているバージョン
description: サポートされている Azure Database for PostgreSQL バージョンについて説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 16064d662c5b101e30f8d2fbb64b39db0848f49f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702318"
---
# <a name="supported-postgresql-database-versions"></a>サポートされている PostgreSQL Database バージョン
マイクロソフトでは、Azure Database for PostgreSQL サービスでの PostgreSQL エンジンの n-2 バージョンのサポートを予定しています。 バージョンは、Azure の現在のメジャー バージョン (n) とその前の 2 つのメジャー バージョン (-2) です。

Azure Database for PostgreSQL では現在、次のバージョンがサポートされています。

## <a name="postgresql-version-107"></a>PostgreSQL バージョン 10.7
このマイナー バージョンの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/10/static/release-10-7.html)を参照してください。

## <a name="postgresql-version-9612"></a>PostgreSQL バージョン 9.6.12
このマイナー バージョンの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html)を参照してください。

## <a name="postgresql-version-9516"></a>PostgreSQL バージョン 9.5.16
このマイナー バージョンの機能強化と修正については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html)を参照してください。

## <a name="managing-updates-and-upgrades"></a>更新プログラムとアップグレードの管理
Azure Database for PostgreSQL では、マイナー バージョンの修正プログラム適用については自動管理されます。 現在、メジャー バージョンのアップグレードはサポートされていません。 たとえば、PostgreSQL 9.5 から PostgreSQL 9.6 へのアップグレードはサポートされません。 次のメジャー バージョンにアップグレードする場合は、新しいエンジンのバージョンで作成されたサーバーにデータベースの[ダンプと復元](./howto-migrate-using-dump-and-restore.md)を作成します。

## <a name="next-steps"></a>次の手順
さまざまな PostgreSQL 拡張機能のサポートについては、[PostgreSQL 拡張機能](concepts-extensions.md)に関するページをご覧ください。
