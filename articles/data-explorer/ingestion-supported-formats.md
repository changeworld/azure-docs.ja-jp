---
title: インジェスト用に Azure Data Explorer でサポートされているデータ形式。
description: インジェスト用に Azure Data Explorer でサポートされているさまざまなデータおよび圧縮の形式について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 16ecdf7ac3f0062637e4bbea86d26e2560f38222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235293"
---
# <a name="data-formats-supported-by-azure-data-explorer-for-ingestion"></a>インジェスト用に Azure Data Explorer でサポートされているデータ形式

データ インジェストは、データをテーブルに追加して Azure Data Explorer でのクエリに使用できるようにするプロセスです。 ingest-from-query 以外のすべてのインジェスト方法では、データは、サポートされているいずれかの形式になっている必要があります。 次の表は、データ インジェスト用に Azure Data Explorer でサポートされている形式とその説明を示しています。

|Format   |拡張機能   |説明|
|---------|------------|-----------|
|Avro     |`.avro`     |[Avro コンテナー ファイル](https://avro.apache.org/docs/current/)。 次のコードがサポートされています: `null`、`deflate` (`snappy` は現在サポートされていません)。|
|CSV      |`.csv`      |コンマ区切り値 (`,`) を含むテキスト ファイル。 [RFC 4180: _コンマ区切り値 (CSV) ファイルの共通形式と MIME の種類_](https://www.ietf.org/rfc/rfc4180.txt)に関するページを参照してください。|
|JSON     |`.json`     |`\n` または `\r\n` で区切られた JSON オブジェクトを含むテキスト ファイル。 「[JSON Lines (JSONL)](http://jsonlines.org/)」を参照してください。|
|MultiJSON|`.multijson`|プロパティ バッグの JSON 配列 (それぞれレコードを表す)、または空白、`\n` または`\r\n` で区切られた任意の数のプロパティ バッグを含むテキスト ファイル。 各プロパティ バッグは、複数の行にまたがることができます データが非プロパティ バッグの場合を除き、この形式は `JSON` よりも優先されます。|
|ORC      |`.orc`      |[ORC ファイル](https://en.wikipedia.org/wiki/Apache_ORC)。|
|Parquet  |`.parquet`  |[Parquet ファイル](https://en.wikipedia.org/wiki/Apache_Parquet)。|
|PSV      |`.psv`      |パイプ区切り値を含むテキスト ファイル (<code>&#124;</code>)。|
|RAW      |`.raw`      |コンテンツ全体が 1 つの文字列値であるテキスト ファイル。|
|SCsv     |`.scsv`     |セミコロン区切り値 (`;`) を含むテキスト ファイル。|
|SOHsv    |`.sohsv`    |SOH で区切られた値を含むテキスト ファイル。 (SOH は ASCII コードポイント 1 です。この形式は、HDInsight の Hive によって使用されます)。|
|TSV      |`.tsv`      |タブ区切り値 (`\t`) を含むテキスト ファイル。|
|TSVE     |`.tsv`      |タブ区切り値 (`\t`) を含むテキスト ファイル。 エスケープにはバックスラッシュ文字 (`\`) が使用されます。|
|TXT      |`.txt`      |`\n` で区切られた行を含むテキスト ファイル。 空の行はスキップされます。|

## <a name="supported-data-compression-formats"></a>サポートされているデータ圧縮形式

次のいずれかの圧縮アルゴリズムを使用して BLOB とファイルを圧縮できます。

|圧縮|拡張機能|
|-----------|---------|
|GZip       |.gz      |
|Zip        |.zip     |

BLOB またはファイルの名前に拡張子を追加して、圧縮を示します。

次に例を示します。
* `MyData.csv.zip` は、BLOB、または CSV として書式設定されたファイルが ZIP (アーカイブまたは単一ファイル) で圧縮されていることを示します
* `MyData.csv.gz` は、BLOB、または CSV として書式設定されたファイルが GZip で圧縮されていることを示します

形式拡張子がなく圧縮のみが含まれている BLOB またはファイルの名前 (例: ) もサポートされています。 この場合、ファイル形式は推測されないため、インジェスト プロパティとして指定する必要があります。

> [!NOTE]
> 一部の圧縮形式では、元のファイル拡張子が圧縮ストリームの一部として追跡されます。 この拡張子は通常、ファイル形式の判別では無視されます。 (圧縮された) BLOB またはファイルの名前からファイル形式を判別できない場合は、`format` インジェスト プロパティを使用して指定する必要があります。

## <a name="next-steps"></a>次のステップ

* [データ インジェスト](/azure/data-explorer/ingest-data-overview)の詳細を確認する
* [Azure Data Explorer データ インジェスト プロパティ](ingestion-properties.md)の詳細を確認する
