---
title: 列指向テーブル ストレージ - Hyperscale (Citus) - Azure Database for PostgreSQL
description: 列指向ストレージを使用したデータの圧縮
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 557fe7d2242b67f8fa53541aef25cfc44090e48b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745160"
---
# <a name="columnar-table-storage"></a>列指向テーブル ストレージ

Azure Database for PostgreSQL - Hyperscale (Citus) では、分析およびデータ ウェアハウスのワークロード用に追加専用の列指向テーブル ストレージがサポートされています。 (行ではなく) 列がディスク上に連続して格納されている場合、データはより圧縮可能になり、クエリは列のサブセットをより迅速に要求できます。

## <a name="usage"></a>使用

列指向ストレージを使用する場合は、テーブル作成時に `USING columnar` を指定します。

```postgresql
CREATE TABLE contestant (
    handle TEXT,
    birthdate DATE,
    rating INT,
    percentile FLOAT,
    country CHAR(3),
    achievements TEXT[]
) USING columnar;
```

Hyperscale (Citus) によって、挿入時に行が "ストライプ" の列型ストレージに変換されます。 各ストライプには、1 つのトランザクションに相当するデータ、または 150000 行のいずれか少ない方のデータが保持されます。  (列指向テーブルのストライプ サイズとその他のパラメーターは、[alter_columnar_table_set](reference-hyperscale-functions.md#alter_columnar_table_set) 関数を使用して変更できます。)

たとえば、次のステートメントでは、すべての値が 1 つのトランザクションに挿入されているため、5 行すべてが同じストライプに配置されます。

```postgresql
-- insert these values into a single columnar stripe

INSERT INTO contestant VALUES
  ('a','1990-01-10',2090,97.1,'XA','{a}'),
  ('b','1990-11-01',2203,98.1,'XA','{a,b}'),
  ('c','1988-11-01',2907,99.4,'XB','{w,y}'),
  ('d','1985-05-05',2314,98.3,'XB','{}'),
  ('e','1995-05-05',2236,98.2,'XC','{a}');
```

Hyperscale (Citus) は列指向データをストライプごとに個別に圧縮するため、可能な場合は、大きなストライプを作成することをお勧めします。 `VACUUM VERBOSE` を使用すると、圧縮率、ストライプの数、ストライプあたりの平均行数など、列指向テーブルに関する事実を確認できます。

```postgresql
VACUUM VERBOSE contestant;
```
```
INFO:  statistics for "contestant":
storage id: 10000000000
total file size: 24576, total data size: 248
compression rate: 1.31x
total row count: 5, stripe count: 1, average rows per stripe: 5
chunk count: 6, containing data for dropped columns: 0, zstd compressed: 6
```

出力には、Hyperscale (Citus) によって zstd 圧縮アルゴリズムが使用され、1.31 倍のデータ圧縮が行われたことが示されています。 圧縮率は、a) メモリにステージされたときの挿入データのサイズと、b) 最終ストライプで圧縮されたデータのサイズを比較します。

この測定方法が原因で、圧縮率はテーブルの行と列指向ストレージのサイズ差と一致しない場合があります。 その差を真に見極めるには、同じデータを含む行と列指向テーブルを作成し、次のように比較することです。

## <a name="measuring-compression"></a>圧縮の測定

圧縮の節約をベンチマークするために、より多くのデータを使用して新しい例を作成しましょう。

```postgresql
-- first a wide table using row storage
CREATE TABLE perf_row(
  c00 int8, c01 int8, c02 int8, c03 int8, c04 int8, c05 int8, c06 int8, c07 int8, c08 int8, c09 int8,
  c10 int8, c11 int8, c12 int8, c13 int8, c14 int8, c15 int8, c16 int8, c17 int8, c18 int8, c19 int8,
  c20 int8, c21 int8, c22 int8, c23 int8, c24 int8, c25 int8, c26 int8, c27 int8, c28 int8, c29 int8,
  c30 int8, c31 int8, c32 int8, c33 int8, c34 int8, c35 int8, c36 int8, c37 int8, c38 int8, c39 int8,
  c40 int8, c41 int8, c42 int8, c43 int8, c44 int8, c45 int8, c46 int8, c47 int8, c48 int8, c49 int8,
  c50 int8, c51 int8, c52 int8, c53 int8, c54 int8, c55 int8, c56 int8, c57 int8, c58 int8, c59 int8,
  c60 int8, c61 int8, c62 int8, c63 int8, c64 int8, c65 int8, c66 int8, c67 int8, c68 int8, c69 int8,
  c70 int8, c71 int8, c72 int8, c73 int8, c74 int8, c75 int8, c76 int8, c77 int8, c78 int8, c79 int8,
  c80 int8, c81 int8, c82 int8, c83 int8, c84 int8, c85 int8, c86 int8, c87 int8, c88 int8, c89 int8,
  c90 int8, c91 int8, c92 int8, c93 int8, c94 int8, c95 int8, c96 int8, c97 int8, c98 int8, c99 int8
);

-- next a table with identical columns using columnar storage
CREATE TABLE perf_columnar(LIKE perf_row) USING COLUMNAR;
```

両方のテーブルに同じ大きなデータ セットを入力します。

```postgresql
INSERT INTO perf_row
  SELECT
    g % 00500, g % 01000, g % 01500, g % 02000, g % 02500, g % 03000, g % 03500, g % 04000, g % 04500, g % 05000,
    g % 05500, g % 06000, g % 06500, g % 07000, g % 07500, g % 08000, g % 08500, g % 09000, g % 09500, g % 10000,
    g % 10500, g % 11000, g % 11500, g % 12000, g % 12500, g % 13000, g % 13500, g % 14000, g % 14500, g % 15000,
    g % 15500, g % 16000, g % 16500, g % 17000, g % 17500, g % 18000, g % 18500, g % 19000, g % 19500, g % 20000,
    g % 20500, g % 21000, g % 21500, g % 22000, g % 22500, g % 23000, g % 23500, g % 24000, g % 24500, g % 25000,
    g % 25500, g % 26000, g % 26500, g % 27000, g % 27500, g % 28000, g % 28500, g % 29000, g % 29500, g % 30000,
    g % 30500, g % 31000, g % 31500, g % 32000, g % 32500, g % 33000, g % 33500, g % 34000, g % 34500, g % 35000,
    g % 35500, g % 36000, g % 36500, g % 37000, g % 37500, g % 38000, g % 38500, g % 39000, g % 39500, g % 40000,
    g % 40500, g % 41000, g % 41500, g % 42000, g % 42500, g % 43000, g % 43500, g % 44000, g % 44500, g % 45000,
    g % 45500, g % 46000, g % 46500, g % 47000, g % 47500, g % 48000, g % 48500, g % 49000, g % 49500, g % 50000
  FROM generate_series(1,50000000) g;

INSERT INTO perf_columnar
  SELECT
    g % 00500, g % 01000, g % 01500, g % 02000, g % 02500, g % 03000, g % 03500, g % 04000, g % 04500, g % 05000,
    g % 05500, g % 06000, g % 06500, g % 07000, g % 07500, g % 08000, g % 08500, g % 09000, g % 09500, g % 10000,
    g % 10500, g % 11000, g % 11500, g % 12000, g % 12500, g % 13000, g % 13500, g % 14000, g % 14500, g % 15000,
    g % 15500, g % 16000, g % 16500, g % 17000, g % 17500, g % 18000, g % 18500, g % 19000, g % 19500, g % 20000,
    g % 20500, g % 21000, g % 21500, g % 22000, g % 22500, g % 23000, g % 23500, g % 24000, g % 24500, g % 25000,
    g % 25500, g % 26000, g % 26500, g % 27000, g % 27500, g % 28000, g % 28500, g % 29000, g % 29500, g % 30000,
    g % 30500, g % 31000, g % 31500, g % 32000, g % 32500, g % 33000, g % 33500, g % 34000, g % 34500, g % 35000,
    g % 35500, g % 36000, g % 36500, g % 37000, g % 37500, g % 38000, g % 38500, g % 39000, g % 39500, g % 40000,
    g % 40500, g % 41000, g % 41500, g % 42000, g % 42500, g % 43000, g % 43500, g % 44000, g % 44500, g % 45000,
    g % 45500, g % 46000, g % 46500, g % 47000, g % 47500, g % 48000, g % 48500, g % 49000, g % 49500, g % 50000
  FROM generate_series(1,50000000) g;

VACUUM (FREEZE, ANALYZE) perf_row;
VACUUM (FREEZE, ANALYZE) perf_columnar;
```

このデータの場合、列テーブルで 8 倍を超える圧縮率を確認できます。

```postgresql
SELECT pg_total_relation_size('perf_row')::numeric/
       pg_total_relation_size('perf_columnar') AS compression_ratio;
 compression_ratio
--------------------
 8.0196135873627944
(1 row)
```

## <a name="example"></a>例

列指向ストレージは、テーブルのパーティション分割では適切に機能します。 例については、Citus Engine のコミュニティ ドキュメントの、[列指向型ストレージを使用したアーカイブ](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage)に関するページを参照してください。

## <a name="gotchas"></a>注意事項

* 列指向ストレージはストライプごとに圧縮します。 ストライプはトランザクションごとに作成されるため、トランザクションごとに 1 行を挿入すると、単一の行が独自のストライプに配置されます。 単一行のストライプにおける圧縮とパフォーマンスは、行テーブルよりも劣ります。 列指向テーブルには、常に一括で挿入します。
* 小さなストライプの束を間違えて列志向型にしてしまうと、行き詰ります。
  唯一の解決策は、新しい列指向テーブルを作成し、1 つのトランザクションで元のテーブルからデータをコピーすることです。
  ```postgresql
  BEGIN;
  CREATE TABLE foo_compacted (LIKE foo) USING columnar;
  INSERT INTO foo_compacted SELECT * FROM foo;
  DROP TABLE foo;
  ALTER TABLE foo_compacted RENAME TO foo;
  COMMIT;
  ```
* 基本的に圧縮できないデータが問題になる可能性がありますが、特定の列を選択する場合には列指向ストレージが役に立ちます。 他の列をメモリに読み込む必要はありません。
* 行と列のパーティションが混在するパーティション テーブルでは、更新対象を慎重に設定する必要があります。 行パーティションのみにヒットするようにフィルター処理を行います。
   * 操作が特定の行パーティション (たとえば、`UPDATE p2 SET i = i + 1`) を対象としている場合は成功します。指定された列パーティション (たとえば、`UPDATE p1 SET i = i + 1`) を対象としている場合は失敗します。
   * 操作がパーティション テーブルを対象とし、すべての列パーティション (たとえば、`UPDATE parent SET i = i + 1 WHERE timestamp = '2020-03-15'`) を除外する WHERE 句がある場合、操作は成功します。
   * 操作がパーティション テーブルを対象にしているが、パーティションのキー列に対してフィルター処理が行われていない場合、操作は失敗します。 列パーティションのみの行に一致する WHERE 句がある場合でも、それだけでは不十分です。パーティション キーもフィルター処理する必要があります。

## <a name="limitations"></a>制限事項

この機能には、重要な制限があります。 詳細については、[Hyperscale (Citus) の制限と制限事項](concepts-hyperscale-limits.md#columnar-storage)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* Citus [時系列チュートリアル](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage) (外部リンク) にある列指向ストレージの例を参照してください。
