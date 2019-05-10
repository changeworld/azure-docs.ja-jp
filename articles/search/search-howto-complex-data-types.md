---
title: 複合データ型をモデル化する方法 - Azure Search
description: 階層または入れ子になったデータ構造は、ComplexType とCollections データ型を使用して、Azure Search インデックスでモデル化できます。
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec2018
ms.openlocfilehash: 00606ed5cbcd8681748241e9404c6e6e5aa95021
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147308"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Azure Search で複合データ型をモデル化する方法

Azure Search インデックスの設定に使用する外部データセットの中には、下部構造が階層または入れ子となっているものがあります。 たとえば、単一の顧客に複数の住所と電話番号が含まれるケース、単一の SKU に複数の色とサイズが含まれるケース、1 冊の書籍に複数の著者が存在するケースなどが挙げられます。 モデル化の際に使う用語では、このような構造が "*複合データ型*"、"*コンパウンド データ型*"、"*コンポジット データ型*"、"*集合体データ型*" と呼ばれることがあります。 Azure Search の用語では、複合型とは、それ自体が単純または複合のいずれかである子 (サブフィールド) を含むフィールドです。 これは、プログラミング言語の構造化データ型と似ています。 複合フィールドは、ドキュメント内の単一オブジェクトを表す単一フィールド、またはオブジェクトの配列を表すコレクションのいずれかの可能性があります。

Azure Search は、複合型とコレクションをネイティブでサポートしています。 これらの型を一緒に使用すると、Azure Search インデックス内の入れ子になったほとんどすべての JSON 構造をモデル化できます。 Azure Search API の以前のバージョンでは、フラット化された行セットのみをインポートできました。 最新のバージョンでは、インデックスはソース データにより密接に調和できるようになりました。 つまり、ソース データに複合型がある場合、インデックスも複合型を持つことができます。

Azure portal の**データのインポート** ウィザードで読み込むことができる [Hotels データ セット](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)から始めることをお勧めします。 ウィザードでは、ソース内の複合型が検出され、検出された構造に基づいてインデックス スキーマが提案されます。

> [!Note]
> 複合型のサポートは、`api-version=2019-05-06` で一般提供されています。 
>
> 検索ソリューションがコレクション内でフラット化されたデータセットの以前の回避策に基づいている場合は、インデックスを変更して、最新の API バージョンでサポートされている複合型を含めることをお勧めします。 API バージョンのアップグレードの詳細については、[最新の REST API バージョンへのアップグレード](search-api-migration.md)または[最新の .NET SDK バージョンへのアップグレード](search-dotnet-sdk-migration-version-9.md)に関する記事を参照してください。

## <a name="example-of-a-complex-structure"></a>複合構造の例

次の JSON ドキュメントは、単純フィールドと複合フィールドで構成されています。 `Address` や `Rooms` などの複合フィールドには、サブフィールドがあります。 `Address` はドキュメント内の単一オブジェクトなので、サブフィールドには単一の値のセットがあります。 対照的に、`Rooms` のサブフィールドには、コレクション内の各オブジェクトに 1 つずつ、複数の値のセットがあります。

```json
{
    "HotelId": "1",
    "HotelName": "Secret Point Motel",
    "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
    "Address": {
        "StreetAddress": "677 5th Ave",
        "City": "New York",
        "StateProvince": "NY"
    },
    "Rooms": [
        {
            "Description": "Budget Room, 1 Queen Bed (Cityside)",
            "Type": "Budget Room",
            "BaseRate": 96.99,
        },
        {
            "Description": "Deluxe Room, 2 Double Beds (City View)",
            "Type": "Deluxe Room",
            "BaseRate": 150.99,
        },
    ]
}
```

## <a name="creating-complex-fields"></a>複合フィールドの作成

他のインデックス定義と同様に、ポータル、[REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)、または [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) を使用して、複合型を含むスキーマを作成できます。 

次の例は、単純フィールド、コレクション、および複合型を含む JSON インデックス スキーマを示しています。 最上位レベルのフィールドと同様に、複合型内の各サブフィールドには型があり、場合によっては属性がある点に注意してください。 スキーマは、上記のデータ例に対応しています。 `Address` はコレクションではない複合フィールドです (1 つのホテルには 1 つの住所があります)。 `Rooms` は複合コレクション フィールドです (1 つのホテルには多くの部屋があります)。

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with Knowledge Store](knowledge-store-howto.md).
-->

```json
{
    "name": "hotels",
    "fields": [
        {   "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true    },
        {   "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        {   "name": "Address", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        },
        {
            "name": "Rooms",
            "type": "Collection(Edm.ComplexType)",
            "fields": [{
                    "name": "Description",
                    "type": "Edm.String",
                    "searchable": true,
                    "analyzer": "en.lucene"
                },
                {
                    "name": "Type",
                    "type": "Edm.String",
                    "searchable": true
                },
                {
                    "name": "BaseRate",
                    "type": "Edm.Double",
                    "filterable": true,
                    "facetable": true
                },
            ]
        }
    ]
}
```
## <a name="updating-complex-fields"></a>複合フィールドの更新

一般にフィールドに適用されるすべての[再インデックス作成規則](search-howto-reindex.md)は、複合フィールドにも適用されます。 ここでいくつかの主な規則を言い換えると、フィールドの追加にはインデックスの再構築が必要ありませんが、ほとんどの修正には必要です。

### <a name="structural-updates-to-the-definition"></a>定義に対する構造的な更新

新しいサブフィールドは、インデックスを再構築することなく、いつでも複合フィールドに追加できます。 たとえば、インデックスに最上位レベルのフィールドを追加する場合と同様に、"ZipCode" を `Address` に、"Amenities" を `Rooms` に追加することができます。 既存のドキュメントでは、データを更新してそれらのフィールドに明示的にデータを入力するまで、新しいフィールドの値は null です。

最上位レベルのフィールドと同様に、複合型内の各サブフィールドには型があり、場合によっては属性がある点に注意してください

### <a name="data-updates"></a>データの更新

アップロード アクションを使用してインデックス内の既存のドキュメントを更新する処理は、複合フィールドと単純フィールドで同じように機能し、すべてのフィールドが置き換えられます。 ただし、マージ (または既存のドキュメントに適用された場合は mergeOrUpload) は、すべてのフィールドで同じようには機能しません。 具体的には、マージにはコレクション内の要素をマージする機能がありません。 これは、プリミティブ型のコレクションだけでなく複合コレクションにも当てはまります。 コレクションを更新するには、完全なコレクション値を取得し、変更を加え、新しいコレクションを Index API 要求に含める必要があります。


## <a name="searching-complex-fields"></a>複合フィールドの検索

自由形式の検索式は、複合型では期待どおりに機能します。 ドキュメント内の任意の場所にある検索可能なフィールドまたはサブフィールドが一致すると、そのドキュメント自体が一致します。 

複数の用語と演算子があり、[Lucene 構文](query-lucene-syntax.md)で可能なように、一部の用語にフィールド名を指定すると、より細かい表現のクエリにすることができます。 たとえば、このクエリは、"Portland" と "OR" という 2 つの用語を Address フィールドの 2 つのサブフィールドに対して照合を試みます。

```json
search=Address/City:Portland AND Address/State:OR
```

このようなクエリは、フルテキスト検索では無相関です (フィルターとは異なります。フィルターの場合、複合コレクションのサブフィールドに対するクエリは、SQL の相関サブクエリと同様に、any または all を使用して相関することができます)。 つまり、上記の Lucene クエリは、"Portland, Maine" (メイン州ポートランド) だけでなく、"Portland, Oregon" (オレゴン州ポートランド)、およびその他のオレゴンの都市を含むドキュメントを返します。 これは、個々の句がドキュメント全体の指定されたフィールドのすべての値に対して評価され、"現在のサブドキュメント" という概念がないためです。 

 

## <a name="selecting-complex-fields"></a>複合フィールドの選択

`$select` パラメーターは、検索結果に返すフィールドを選択するために使用されます。 このパラメーターを使用して複合フィールドの特定のサブフィールドを選択するには、スラッシュ (`/`) で区切った親フィールドとサブフィールドを含めます。

```json
$select=HotelName, Address/City, Rooms/BaseRate
```

検索結果に必要な場合は、インデックス内のフィールドを Retrievable とマークする必要があります。 `$select` ステートメントでは Retrievable とマークされたフィールドのみを使用できます。 


## <a name="filter-facet-and-sort-complex-fields"></a>複合フィールドのフィルター処理、ファセット、並べ替え

検索のフィルター処理とフィールド検索に使用されるものと同じ [OData パス構文](query-odata-filter-orderby-syntax.md)を、検索要求内のフィールドのファセット、並べ替え、および選択にも使用できます。 複合型の場合、sortable または facetable とマークできるサブフィールド管理する規則が適用されます。 

### <a name="faceting-sub-fields"></a>サブフィールドのファセット 

型が `Edm.GeographyPoint` または `Collection(Edm.GeographyPoint)` でない限り、すべてのサブフィールドは facetable とマークすることができます。 

ファセット ナビゲーション構造に対してドキュメント数が返される場合、その数は、複合コレクション (部屋) 内の入れ子になったドキュメントではなく、親ドキュメント (ホテル) に関連します。 たとえば、ホテルに 20 室の型 "suite" があるとします。 このファセット パラメーター `facet=Rooms/Type` を考えると、ファセット数は親ドキュメント (ホテル) に対する 1 であり、中間のサブドキュメント (部屋) にはなりません。 

### <a name="sorting-complex-fields"></a>複合フィールドの並べ替え

並べ替え操作は、サブドキュメント (Rooms) ではなく、ドキュメント (Hotels) に適用されます。 Rooms のように複合型のコレクションがある場合は、Rooms で並べ替えることがまったくできないことを認識することが重要です。 実際のところ、どのコレクションでも並べ替えることはできません。 

並べ替え操作は、単純フィールドとしても複合型のサブフィールドとしても、フィールドが単一値の場合に機能します。 たとえば、各ホテルの郵便番号は 1 つのみなので、`$orderby=Address/ZipCode` 複合型は並べ替えることができます。 

インデックス フィールド内の並べ替えに関する規則をもう一度説明すると、`$orderby` ステートメント内で使用するには、Filterable および Sortable とマークする必要があります。 

## <a name="next-steps"></a>次の手順

 **データのインポート** ウィザードで [Hotels データ セット](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)を試してください。 データにアクセスするには、readme に記載されている Cosmos DB 接続情報が必要です。 
 
 その情報を入手したら、ウィザードの最初の手順は新しい Azure Cosmos DB データ ソースを作成することです。 ウィザードの手順を進め、ターゲットのインデックス ページに移動すると、複合型のインデックスが表示されます。 このインデックスを作成して読み込み、クエリを実行して新しい構造を理解してください。

> [!div class="nextstepaction"]
> [クイックスタート: インポート、インデックス作成、クエリのポータル ウィザード](search-get-started-portal.md)