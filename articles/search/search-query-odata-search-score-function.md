---
title: OData の search.score 関数リファレンス
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search クエリで search.score 関数を使用するための構文とリファレンスのドキュメント。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 2439d4f03184f8dbb85b229b3908dff95013b4bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113143"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Azure Cognitive Search における OData の `search.score` 関数

[ **$orderby** パラメーター](search-query-odata-orderby.md)なしで Azure Cognitive Search にクエリを送信した場合、返される結果は関連性スコア別に降順で並べ替えられます。 **$orderby** を使用する場合でも、既定では、関連性スコアを使用して優先順位が決められます。 ただし、関連性スコアを最初の並べ替え条件として使用し、その他のいくつかの条件を判断基準として使用するのが便利な場合もあります。 `search.score` 関数を使用すれば、それを行うことができます。

## <a name="syntax"></a>構文

`search.score`$orderby**の** の構文は `search.score()` です。 関数 `search.score` には、いかなるパラメーターも取得されません。 これは、`asc`$orderby`desc` パラメーター内の他の句と同様に、**または** の並べ替え順序指定子と一緒に使用できます。 これは並べ替え条件のリスト内の任意の場所に配置できます。

## <a name="example"></a>例

`search.score` と `rating` に基づいて降順でホテルを並べ替え、その後、所与の座標からの距離別に昇順で並べ替え、2 つのホテルで評価が同じ場合、距離的に近い方が先に表示されるようにします。

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>次のステップ  

- [Azure Cognitive Search の OData 式言語の概要](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)
- [ドキュメントの検索 &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
