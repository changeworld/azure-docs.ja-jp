---
title: Azure Cosmos DB のインデックスの種類
description: Azure Cosmos DB でのインデックスの種類の概要
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: f3c5d7bc1907e94ff2e590fe77cc531ac4b01f4c
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628558"
---
# <a name="index-types-in-azure-cosmos-db"></a>Azure Cosmos DB のインデックスの種類

パスのインデックス作成ポリシーを構成するときは、複数のオプションがあります。 すべてのパスに対して 1 つ以上のインデックス作成定義を指定することができます。

- **データ型**: String、Number、Point、Polygon、LineString (各パスのデータ型ごとにエントリを 1 つだけ含めることができます)。

- **インデックスの種類**: ハッシュ (等値クエリ)、範囲 (等値、範囲、ORDER BY クエリ)、または空間 (空間クエリ)。

- **有効桁数**: ハッシュ インデックスの場合、文字列と数値の両方で 1 から 8 の範囲であり、既定値は 3 です。 範囲インデックスの場合、最大有効桁数は -1 です。 文字列または数値の場合、1 から 100 (最大有効桁数) の範囲で変更できます。

## <a name="index-kind"></a>インデックスの種類

Azure Cosmos DB では、String、Number、またはその両方のデータ型に対して構成できるすべてのパスに対して、ハッシュ インデックスと範囲インデックスをサポートします。

- **ハッシュ インデックス**では、効率的な等値クエリと JOIN クエリをサポートします。 ほとんどのユース ケースでは、ハッシュ インデックスに既定値 3 バイトより高い有効桁数は不要です。 データ型には、String または Number を指定できます。

- **範囲インデックス**では、効率的な等値クエリ、範囲クエリ (>、<、>=、<=、!= を使用)、および ORDER BY クエリをサポートします。 既定では ORDER BY クエリには、インデックスの最大有効桁数 (-1) も必要です。 データ型には、String または Number を指定できます。

- **空間インデックス** では、効率的な空間クエリ (within と distance) をサポートします。 データ型には、Point、Polygon、または LineString を指定できます。 Azure Cosmos DB では、Point、Polygon、または LineString データ型に対して指定できる、すべてのパスに対する空間インデックスの種類もサポートしています。 指定されたパスの値は、{"type": "Point", "coordinates": [0.0, 10.0]} のような有効な GeoJSON フラグメントである必要があります。 Azure Cosmos DB は、Point、Polygon、LineString データ型の自動インデックス作成をサポートしています。

ハッシュ、範囲、および空間の各インデックスを使用して処理できるクエリの例を次に示します。

| **インデックスの種類** | **説明/ユース ケース** |
| ---------- | ---------------- |
| Hash  | Hash を /prop/? (または/) に使用して、以下のクエリを効率的に処理することができます。<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>Hash を /props/[]/?  (または / または /props/) に使用して、以下のクエリを効率的に処理することができます。<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5  |
| Range  | Range を /prop/? (または/) に使用して、以下のクエリを効率的に処理することができます。<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop   |
| Spatial     | Range を /prop/? (または/) に使用して、以下のクエリを効率的に処理することができます。<br><br>SELECT FROM collection c<br><br>WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... }) -- ポイントに対するインデックス作成が有効になっている場合<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Point", ... }, c.prop) -- ポリゴンに対するインデックス作成が有効になっている場合     |

## <a name="default-behavior-of-index-kinds"></a>各インデックスの種類の既定の動作

- クエリを処理するにはスキャンが必要であることを通知する、任意の有効桁数が指定された範囲インデックス が存在しない場合、既定では、>= のような範囲演算子を使用したクエリにはエラーが返されます。

- "x-ms-documentdb-enable-scan" ヘッダーを REST API で使用するか、.NET SDK で "EnableScanInQuery" 要求オプションを使用すれば、範囲インデックスがなくても、範囲クエリを実行することができます。 Azure Cosmos DB でインデックスを使用してフィルター処理できるその他のフィルターがクエリにない場合、エラーは返されません。

- 既定では、空間インデックスがない場合、またはインデックスからサービスを提供できる他のフィルターがない場合、空間クエリにはエラーが返されます。 このようなクエリは、x-ms-documentdb-enable-scan または EnableScanInQuery を使用して、スキャンとして実行できます。

## <a name="index-precision"></a>インデックスの有効桁数

- インデックスの有効桁数を使用して、インデックスのストレージ オーバーヘッドとクエリのパフォーマンスをトレードオフできます。 数値の場合、既定の有効桁数の構成 -1 (最大) を使用することをお勧めします。 JSON では数値は 8 バイトであるため、これは、8 バイトの構成と同じです。 1 から 7 などの小さい値の有効桁数を選択すると、特定の範囲内の値が同じインデックス エントリにマップされることになります。 そのため、インデックスのストレージ領域は減りますが、クエリの実行で処理する項目が増える可能性があります。 結果として、より多くのスループット/RU が消費されます。

- インデックスの有効桁数は、文字列の範囲を使用するとより実際に役立ちます。 文字列は任意の長さにできるため、インデックスの有効桁数を選択することで、文字列の範囲クエリのパフォーマンスに影響する可能性があります。 また、必要なインデックスのストレージ領域の量に影響する可能性もあります。 文字列の範囲インデックスでは、有効桁数を 1 から 100 の間、または-1 (最大) に構成できます。 文字列のプロパティに ORDER BY クエリを実行する場合は、対応するパスに有効桁数 -1 を指定する必要があります。

- 空間インデックスでは、常にすべての型 (Point、LineString、Polygon) 用の既定のインデックスの有効桁数を使用します。 空間インデックスの場合、既定のインデックスの有効桁数はオーバーライドはできません。

クエリが ORDER BY を使用しているのに、最大有効桁数でのクエリ パスに対して範囲インデックスが存在しない場合、Azure Cosmos DB はエラーを返します。

## <a name="next-steps"></a>次の手順

Azure Cosmos DB のインデックス作成の詳細については、次の記事を参照してください。

- [インデックス作成の概要](index-overview.md)
- [インデックス作成ポリシー](indexing-policies.md)
- [インデックスのパス](index-paths.md)

