---
title: Azure Database for PostgreSQL サーバー上で TOAST テーブル ストレージ戦略を使用してクエリ時間を最適化する
description: この記事では、Azure Database for PostgreSQL サーバー上で TOAST テーブル ストレージ戦略を使用してクエリ時間を最適化する方法について説明します。
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: dee8aaaef4b1998a7234a88d07ad5efbc79d050b
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628637"
---
# <a name="optimizing-query-time-with-toast-table-storage-strategy"></a>TOAST テーブル ストレージ戦略を使用してクエリ時間を最適化する 
この記事では、TOAST テーブル ストレージ戦略を使用してクエリ時間を最適化する方法について説明します。

## <a name="toast-table-storage-strategies"></a>TOAST テーブル ストレージ戦略
TOAST 可能な列をディスクに格納するための戦略には、圧縮と行外の格納のさまざまな組み合わせを表す 4 つの異なる戦略があります。 データ型のレベルと列のレベルで戦略を設定できます。
- **Plain** では、圧縮も行外の格納も阻止されます。さらに、varlena 型に対するシングル バイト ヘッダーも使用できません。 **Plain** は、TOAST できないデータ型の列に対して使用できる唯一の可能な戦略です。
- **Extended** では、圧縮と行外の格納が許可されます。 **Extended** は、TOAST 可能なデータ型の大半で、既定で使用されます。 圧縮がまず行われ、それでも行が大きすぎる場合は行外に格納されます。
- **External** では、行外の格納は許可されますが、圧縮は許可されません。 **External** を使用すると、text 行と bytea 列全体に対する部分文字列操作が圧縮されていない行外の値から必要な部分のみをフェッチするように最適化されるため、それらの操作が高速化されます (格納領域が増加するという欠点があります)。
- **Main** では、圧縮は許可されますが、行外の格納は許可されません。 これらの列については、1 ページに収まるように行を縮小する方法がない場合の最後の手段としてのみ、行外の格納が行われます。

## <a name="using-toast-table-storage-strategies"></a>TOAST テーブル ストレージ戦略の使用
クエリで TOAST 可能なデータ型にアクセスする場合は、既定の **Extended** オプションではなく **Main** を使用してクエリ時間を短縮することを検討します。 **Main** では、行外の格納は除外されません。 一方、クエリで TOAST 可能なデータ型にアクセスしない場合は、**Extended** オプションのままにするほうが有用である可能性があります。 メイン テーブルの行の大部分が共有バッファ キャッシュに収まるため、パフォーマンスが向上します。

ワイドなテーブルと文字数が多いスキーマを使用するワークロードがある場合は、PostgreSQL TOAST テーブルの使用を検討します。 サンプルの顧客テーブルには 350 列を超える列があり、いくつかの列では文字数が 255 文字に及んでいました。 TOAST 戦略を **Main** に変換した後、そのベンチマーク クエリ時間は、4,203 秒から 467 秒に短縮され、パフォーマンスが 89 パーセント向上しました。

## <a name="next-steps"></a>次の手順
上記の特性を考慮してワークロードを見直します。 

次の PostgreSQL のドキュメントを確認します。[Chapter 68, Database physical storage](https://www.postgresql.org/docs/current/storage-toast.html) (第 68 章 データベースの物理ストレージ) 