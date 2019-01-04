---
title: Azure Database for PostgreSQL サーバーでの一括挿入を最適化する
description: この記事では、Azure Database for PostgreSQL サーバーでの一括挿入を最適化する方法について説明します。
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 9d2bfcddc649e4fff68bdba49df0945e88067036
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545238"
---
# <a name="optimizing-bulk-inserts-and-use-of-transient-data-on-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーでの一括挿入の最適化と一時データの使用 
この記事では、Azure Database for PostgreSQL サーバーでの一括挿入の最適化と一時データの使用を行う方法について説明します。

## <a name="using-unlogged-tables"></a>ログ記録されないテーブルの使用
一時データが関係するか、大きなデータセットを一括で挿入するワークロード操作を行うお客様は、ログ記録されないテーブルの使用を検討してください。

ログ記録されないテーブルは、一括挿入を最適化するために効果的に使用できる PostgreSQL の機能です。 PostgreSQL では先書きログ (WAL) が使用され、原子性と永続性という 2 つのACID プロパティが既定で提供されます。 ログ記録されないテーブルへの挿入とは、PostgreSQL でトランザクション ログへの書き込みなしで挿入が実行されることを意味し、それ自体が I/O 操作であるため、通常のテーブルに比べ、これらのテーブルでは処理が大幅に高速化されます。

ログ記録されないテーブルを作成するためのオプションを次に示します。
- 次の構文を使用して、新しいログ記録されないテーブルを作成します。`CREATE UNLOGGED TABLE <tableName>`
- 次の構文を使用して、既存のログ記録されるテーブルをログ記録されないテーブルに変換します。`ALTER <tableName> SET UNLOGGED`  これは、次の構文を使用して元に戻すことができます。`ALTER <tableName> SET LOGGED`

## <a name="unlogged-table-tradeoff"></a>ログ記録されないテーブルのトレードオフ
ログ記録されないテーブルは、クラッシュ セーフではありません。 ログ記録されないテーブルはクラッシュ後に自動的に切り捨てられるか、不完全なシャットダウンの対象になります。 ログ記録されないテーブルの内容は、スタンバイ サーバーにレプリケートされることもありません。 ログ記録されないテーブルに対して作成されたインデックスも、ログに自動的に記録されることはありません。  挿入操作が完了したら、テーブルをログ記録されるテーブルに変換して、挿入を永続的にすることができます。

ただし、一部のお客様のワークロードでは、ログ記録されないテーブルの使用時に約 15 ～ 20% のパフォーマンスが向上しています。

## <a name="next-steps"></a>次の手順
一時データと大規模な一括挿入の使用について、ワークロードを見直します。  

次の PostgreSQL のドキュメントを確認します。[Create Table SQL Commands](https://www.postgresql.org/docs/current/static/sql-createtable.html) (Create Table SQL コマンド)