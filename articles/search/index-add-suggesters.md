---
title: Azure Search インデックスに suggester を追加する
description: Azure Search インデックスのフィールドのテキストで提案されたクエリが構成される、先行入力クエリ操作に対してフィールドを有効にします。
ms.date: 02/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: fd4b29134fd45ed2888fbc81ded413ecf7286959
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308654"
---
# <a name="add-suggesters-to-an-azure-search-index"></a>Azure Search インデックスに suggester を追加する

**suggester** は、"入力と並行して検索を行う" [検索候補](https://docs.microsoft.com/rest/api/searchservice/suggestions)機能および[オートコンプリート (プレビュー)](search-autocomplete-tutorial.md) 機能をサポートする Azure Search のコンストラクトです。 Suggestions API を呼び出す前に、インデックスで **suggester** を定義して、特定のフィールドでの検索候補を有効にする必要があります。

**suggester** にはいくつかのプロパティがありますが、基本的には [Suggestions API](https://docs.microsoft.com/rest/api/searchservice/suggestions) を有効にするフィールドのコレクションです。 たとえば、旅行アプリでは、目的地、都市、アトラクションなどで先行入力検索を有効にすることがあります。 その場合、3 つのフィールドすべてをフィールド コレクションに追加します。

インデックスごとに使用できる **suggester** リソースは 1 つだけです (具体的には、**suggesters** コレクション内の 1 つの **suggester**)。

## <a name="creating-a-suggester"></a>suggester の作成

いつでも **suggester** を作成できますが、インデックスへの影響はフィールドによって異なります。

+ 同じ更新の一部として suggester に新しいフィールドを追加する場合は、インデックスの再構築が必要ないため影響は最も小さくなります。
+ 一方、既存のフィールドを suggester に追加する場合は、フィールドの定義が変化するので、インデックスの完全な再構築が必要です。

**suggester** は、あいまいな用語やフレーズではなく、特定のドキュメントを提案するために使用した場合に、最も効果的に機能します。 最適な候補フィールドは、タイトル、名前、および項目を識別できる他の比較的短い語句です。 あまり効果的ではないのは、カテゴリやタグなどの反復的なフィールドまたは説明やコメントなどの非常に長いフィールドです。

suggester を作成した後、クエリのロジックに [Suggestions API](https://docs.microsoft.com/rest/api/searchservice/suggestions) を追加して機能を呼び出します。

**suggester** を定義するプロパティは次のとおりです。

|プロパティ|説明|
|--------------|-----------------|
|`name`|**suggester** の名前。 [検索候補 &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions) を呼び出すときに、**suggester** の名前を使用します。|
|`searchMode`|候補語句の検索に使用する戦略。 現在サポートされている唯一のモードは `analyzingInfixMatching` です。文の先頭または中間にあるフレーズの柔軟なマッチングを実行します。|
|`sourceFields`|検索候補の内容のソースである 1 つまたは複数のフィールドのリスト。 `Edm.String` 型および `Collection(Edm.String)` 型のフィールドだけを、検索候補のソースにできます。 カスタム言語アナライザーが設定されていないフィールドのみを使用できます。 |

## <a name="suggester-example"></a>suggester の例
**suggester** は、インデックス定義の一部です。 現在のバージョンで **suggesters** コレクションに存在できる **suggester** は、**fields** コレクションと **scoringProfiles** と共に 1 つだけです。

```
{
  "name": "hotels",
  "fields": [
    . . .
  ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ],
  "scoringProfiles": [
    . . .
  ]
}

```

## <a name="see-also"></a>関連項目
[インデックスを作成する &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
[インデックスを更新する &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/update-index)  
[検索候補 &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions)  
[インデックス操作 &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/index-operations)  
[Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/)  
[Azure Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)
