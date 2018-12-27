---
title: Bing のサムネイルのサイズ変更とトリミング - Bing Image Search API
description: Bing Image Search API からの応答に含まれるサムネイルのサイズを変更し、トリミングする方法について説明します。
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.assetid: F4FFAE91-A003-4F7C-8E60-83A142485E28
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: de82cc5554af91294dda3826dfb394cc94dbf3d0
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2018
ms.locfileid: "46296229"
---
# <a name="resizing-and-cropping-thumbnail-images"></a>サムネイル画像のサイズ変更とトリミング

検索クエリを処理すると、Bing は[応答](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#bing-image-search-response-format)の中ですべてのイメージのサムネイル情報を生成します。 この情報は、返されたサムネイルのすべてまたはサブセットを表示するために使用できます。 サブセットを表示する場合、残りのイメージを表示するためのオプションを提供します。


<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

[contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) を使用すると、サムネイルがクリックされた場合に、フルサイズの画像をユーザーに表示することができます。 必ずその帰属を示すようにしてください。

`shoppingSourcesCount` または `recipeSourcesCount` がゼロより大きい場合は、対応するサムネイルにバッジ (ショッピング カートなど) を追加し、画像内のアイテムについてのショッピングまたはレシピが存在することを示します。

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

画像に関する分析情報 (その画像がある Web ページ、画像内で認識された人物など) を取得するには、[imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken) を使用します。 詳細については、[画像の分析情報](image-insights.md)に関するページを参照してください。

## <a name="resizing-and-cropping-thumbnails"></a>サムネイルのサイズ変更とトリミング

ユーザーのカーソルがサムネイルをポイントした場合などに、サムネイルをサイズ変更したり、展開したりすることもできます。
> [!NOTE]
> 画像を展開する場合は、必ずその帰属を示すようにしてください。 たとえば、[hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) からホストを展開して、それを画像の下に表示するようにします。

[!INCLUDE [cognitive-services-bing-resize-crop-thumbnails](../../../includes/cognitive-services-bing-resize-crop-thumbnails.md)]
