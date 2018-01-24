---
title: "最小限のダウンタイムでの Azure Database for PostgreSQL への移行 | Microsoft Docs"
description: "この記事では、PostgreSQL データベースをダンプ ファイルに抽出し、Azure Database for PostgreSQL で pg_dump によって作成されたアーカイブ ファイルから PostgreSQL データベースを復元し、Attunity Replicate for Microsoft Migrations を使用してソース データベースからターゲット データベースへの初期読み込みと継続的なデータ同期を設定することで、最小限のダウンタイムでの移行を実行する方法を説明します。"
services: postgresql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: 8c98c58042e7f1d1726eaad6ee03d1531b6c910e
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>最小限のダウンタイムでの Azure Database for PostgreSQL への移行
Attunity とマイクロソフトの共同提供サービスである Attunity Replicate for Microsoft Migrations を使用して、既存の PostgreSQL データベースを Azure Database for PostgreSQL に移行できます。このサービスは Azure Database Migration Service に付属しており、マイクロソフトのお客様には追加料金なしでご利用いただけます。 Attunity Replicate for Microsoft Migrations ではデータベース移行時のダウンタイムも最小限に抑えられ、移行プロセス中もソース データベースは動作を続けます。

Attunity Replicate は、さまざまなソースとターゲット間のデータ同期を有効にするデータ レプリケーション ツールで、スキーマ作成スクリプトと各データベース テーブルに関連するデータを伝達します。 Attunity Replicate は、他のアーティファクト (SP、トリガー、関数など) を伝達したり、このようなアーティファクトにホストされている PL/SQL コードなどを T-SQL に変換したりすることはありません。

> [!NOTE]
> Attunity Replicate では幅広い移行シナリオのセットがサポートされますが、Attunity Replicate for Microsoft Migrations はソース/ターゲットのペアの特定のサブセットのサポートに重点を置いています。

最小限のダウンタイムでの移行の実行プロセスの概要を次に示します。

1. -n パラメーターを指定した [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) コマンドを使用してから、[pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) コマンドを使用して、Azure Database for PostgreSQL データベースに **PostgreSQL ソース スキーマを移行**します。

2. Attunity Replicate for Microsoft Migrations を使用して、**ソース データベースからターゲット データベースへの初期読み込みと継続的なデータ同期を設定**します。 これにより、アプリケーションを Azure 上のターゲット PostgreSQL データベースに切り替える準備をするときに、ソース データベースを読み取り専用として設定する必要のある時間が最小限に抑えられます。

Attunity Replicate for Microsoft Migrations サービスについて詳しくは、次のリソースをご覧ください。
 - Attunity Replicate for Microsoft Migrations の [Web ページ](https://aka.ms/attunity-replicate)。
 - Attunity Replicate for Microsoft Migrations の[ダウンロード](http://discover.attunity.com/download-replicate-microsoft-lp6657.html)。
 - クイック スタート ガイド、チュートリアル、サポートがある Attunity Replicate [コミュニティ](https://microsoft.attunity.com/)。
 - Attunity を使用して PostgreSQL から Azure Database for PostgreSQL に移行する手順については、「[Database Migration Guide](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql)」(データベース移行ガイド) をご覧ください。