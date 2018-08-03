---
title: Bing 分析情報の例 | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Bing.com で表示される画像分析情報の例を示します。
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/17/2018
ms.author: scottwhi
ms.openlocfilehash: 927a02b400c7a7b250b315ad5668feb198fd590a
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005691"
---
# <a name="examples-of-bing-insights-usage"></a>Bing 分析情報の使用例

このセクションでは、Bing.com で表示される分析情報の例を示します。

## <a name="pagesincluding-insight-example"></a>PagesIncluding 分析情報の例

その画像を含む Web ページが Bing でどのように表示されるかを次に示します。 この例では、最初の Web ページへのリンクを表示し、この画像を含む他の Web ページの一覧をユーザーが展開したり折りたたんだりできるようにしています。

![画像を含むページの展開表示](./media/pages-including.PNG)


## <a name="shoppingsources-insight-example"></a>ShoppingSources 分析情報の例

画像に表示されている製品のショッピング ソースが Bing でどのように表示されるかを次に示します。

![ショッピング ソース](./media/shopping-sources.PNG)


## <a name="visualsearch-insight-example"></a>VisualSearch 分析情報の例

見た目が似ている画像が Bing でどのように表示されるかを次に示します (例の **[関連画像]** を参照)。

![見た目が似ている画像](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Recipes 分析情報の例

画像に表示されている料理のレシピが Bing でどのように表示されるかを次に示します。 この例により、使用可能なレシピがあることがユーザーにわかります。

![レシピと画像を含むページ](./media/recipes-pages-including.PNG)

 また、ユーザーが一覧を展開すると、レシピへのリンクが表示されます。

![画像を含むレシピ ページの展開表示](./media/expanded-recipes-pages-including.PNG)


## <a name="relatedsearches-insight-example"></a>RelatedSearches 分析情報の例

他のユーザーによる画像の関連検索が Bing でどのように表示されるかを次に示します。 ユーザーが画像をクリックすると、関連するクエリの Bing.com/images の検索結果ページに移動します。

![画像の関連検索](./media/bordered-related-searches.PNG)


## <a name="entity-insight-example"></a>Entity 分析情報の例

画像に表示されるエンティティ (人、場所、物など) に関する情報が Bing でどのように表示されるかを次に示します。 ユーザーがエンティティのリンクをクリックすると、そのエンティティに関する Bing.com の検索結果ページに移動します。

![画像に表示されるエンティティ](./media/entity.PNG)


## <a name="displaying-other-insights-that-the-user-might-explore"></a>ユーザーが検索する可能性がある他の分析情報の表示

画像に関してユーザーが検索する可能性がある他の情報が Bing でどのように表示されるかを次に示します。

![画像に関する他の分析情報の検索](./media/apple-pie-more-tags.PNG)


## <a name="bounding-boxes-and-hot-spots"></a>境界ボックスとホット スポット

既定以外のタグには、画像内でタグが適用される対象領域を特定する境界ボックスが含まれています。 境界ボックスによって画像全体が特定される場合以外は、境界ボックスを使用して画像のホット スポットを作成します。 ユーザーがホット スポットをクリックすると、ホット スポット (四角形) に含まれるコンテンツに関連する情報を取得できます。 たとえば、画像が最新ファッションに関するものである場合、結果にはその画像に表示されているアクセサリ (バッグ、ジュエリー、スカーフなど) のタグ (および境界ボックス) が含まれる可能性があります。次の例は、画像に表示されているサングラスを特定するホット スポットの四角形です。

![境界ボックスとホット スポット](./media/click-to-search.PNG)



## <a name="next-steps"></a>次の手順

これらの例に使われている JSON を確認するには、[既定の分析情報](default-insights-tag.md)に関するページと [JSON 応答](overview.md#the-response)に関するページを参照してください。

実際に要求を送信してみるには、クイック スタートを参照してください。[C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)。





