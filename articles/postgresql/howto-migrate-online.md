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
ms.openlocfilehash: efbd4f227880875c11e2c43c84716dfc49c5717d
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>最小限のダウンタイムでの Azure Database for PostgreSQL への移行
Attunity Replicate for Microsoft Migrations を使用して、既存の PostgreSQL データベースを Azure Database for PostgreSQL に移行できます。 Attunity Replicate は、Attunity と Microsoft の共同製品です。 これは、Azure Database Migration Service と共に、Microsoft のお客様には追加コストなしで含まれています。 

Attunity Replicate は、データベース移行中のダウンタイムを最小限に抑えるのに役立ち、プロセス全体を通してソース データベースを動作状態に維持します。

Attunity Replicate は、さまざまなソースとターゲットの間のデータ同期を可能にするデータ レプリケーション ツールです。 これは、各データベース テーブルに関連付けられたスキーマ作成スクリプトおよびデータを伝播します。 Attunity Replicate が他のアーティファクト (SP、トリガー、関数など) を伝播したり、たとえば、このようなアーティファクトでホストされている PL/SQL コードを T-SQL に変換したりすることはありません。

> [!NOTE]
> Attunity Replicate は幅広い一連の移行シナリオをサポートしていますが、ソースとターゲットのペアの特定のサブセットのサポートに重点を置いています。

最小限のダウンタイムでの移行を実行するためのプロセスの概要を次に示します。

* -n パラメーターを指定した [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) コマンドを使用してから、[pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) コマンドを使用して、Azure Database for PostgreSQL データベースに **PostgreSQL ソース スキーマを移行**します。

* Attunity Replicate for Microsoft Migrations を使用して、**ソース データベースからターゲット データベースへの初期読み込みと継続的なデータ同期を設定**します。 これにより、アプリケーションを Azure 上のターゲット PostgreSQL データベースに切り替える準備をするときに、ソース データベースを読み取り専用として設定する必要のある時間が最小限に抑えられます。

Attunity Replicate for Microsoft Migrations 製品の詳細については、次のリソースを参照してください。
 - [Attunity Replicate for Microsoft Migrations](https://aka.ms/attunity-replicate) の Web ページに移動します。
 - [Attunity Replicate for Microsoft Migrations](http://discover.attunity.com/download-replicate-microsoft-lp6657.html) をダウンロードします。
 - クイック スタート ガイド、チュートリアル、およびサポートについては、[Attunity Replicate コミュニティ](https://aka.ms/attunity-community/)にアクセスしてください。
 - Attunity Replicate を使用して PostgreSQL から Azure Database for PostgreSQL に移行する方法に関するステップ バイ ステップ ガイダンスについては、「[データベース移行ガイド](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql)」を参照してください。