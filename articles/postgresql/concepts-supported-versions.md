---
title: Azure Database for PostgreSQL のサポートされているバージョン
description: サポートされている Azure Database for PostgreSQL バージョンについて説明します。
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/07/2018
ms.openlocfilehash: f337679279f4a3eb6d01808f15a59fee4c8f3cd1
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630589"
---
# <a name="supported-postgresql-database-versions"></a>サポートされている PostgreSQL Database バージョン
Microsoft は、Azure Database for PostgreSQL サービスで、n-2 バージョン (つまり、現在リリースされているメジャー バージョン (n) と以前の 2 つのメジャー バージョン (-2)) の PostgreSQL エンジンをサポートすることを目指しています。

Azure Database for PostgreSQL では現在、次のバージョンがサポートされています。

## <a name="postgresql-version-104"></a>PostgreSQL バージョン 10.4
このマイナー バージョンの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/10/static/release-10-4.html)を参照してください。

## <a name="postgresql-version-969"></a>PostgreSQL バージョン 9.6.9
このマイナー バージョンの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/9.6/static/release-9-6-9.html)を参照してください。

## <a name="postgresql-version-9513"></a>PostgreSQL バージョン 9.5.13
このマイナー バージョンの機能強化と修正については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/9.5/static/release-9-5-13.html)を参照してください。

## <a name="managing-updates-and-upgrades"></a>更新プログラムとアップグレードの管理
Azure Database for PostgreSQL では、マイナー バージョンの更新プログラムの適用については自動管理されます。 現在、メジャー バージョンのアップグレードはサポートされていません。 たとえば、PostgreSQL 9.5 から PostgreSQL 9.6 へのアップグレードはサポートされません。 次のメジャー バージョンにアップグレードする場合は、新しいエンジンのバージョンで作成されたサーバーに[ダンプを復元](./howto-migrate-using-dump-and-restore.md)します。

## <a name="next-steps"></a>次の手順
さまざまな PostgreSQL 拡張機能のサポートについては、[PostgreSQL 拡張機能](concepts-extensions.md)に関するページをご覧ください。
