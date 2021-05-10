---
title: 列指向テーブル ストレージのプレビュー - Hyperscale (Citus) - Azure Database for PostgreSQL
description: 列指向ストレージを使用したデータの圧縮 (プレビュー)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c60c398d49a802dbe051ca37c4aea2092ab5144b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023857"
---
# <a name="columnar-table-storage-preview"></a>列指向テーブル ストレージ (プレビュー)

> [!IMPORTANT]
> Hyperscale (Citus) の列指向テーブル ストレージは、現在プレビュー中です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
>
> その他の新機能については、[Hyperscale (Citus) のプレビュー機能](hyperscale-preview-features.md)に関するページで全一覧をご覧いただけます。

Azure Database for PostgreSQL - Hyperscale (Citus) では、分析およびデータ ウェアハウスのワークロード用に追加専用の列指向テーブル ストレージがサポートされています。 (行ではなく) 列がディスク上に連続して格納されている場合、データはより圧縮可能になり、クエリは列のサブセットをより迅速に要求できます。

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

```postgresql
CREATE TABLE contestant_row AS
    SELECT * FROM contestant;

SELECT pg_total_relation_size('contestant_row') as row_size,
       pg_total_relation_size('contestant') as columnar_size;
```
```
 row_size | columnar_size
----------+---------------
    16384 |         24576
```

この小さなテーブルの場合、列指向ストレージは実際にはより多くの領域を使用しますが、データのサイズが大きくなるにつれて、圧縮が優先されます。

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

この機能には、重要な制限が数多くあります。 詳細については、[Hyperscale (Citus) の制限と制限事項](concepts-hyperscale-limits.md#columnar-storage)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* Citus [時系列チュートリアル](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage) (外部リンク) にある列指向ストレージの例を参照してください。
