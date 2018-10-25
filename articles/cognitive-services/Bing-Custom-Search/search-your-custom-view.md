---
title: カスタム ビューの検索 - Bing Custom Search
titlesuffix: Azure Cognitive Services
description: Web のカスタム ビューを検索する方法について説明します。
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: maheshb
ms.openlocfilehash: 15c5b3c58c4f3617111707ed82d031b67b6ad4c1
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465137"
---
# <a name="call-your-custom-search"></a>カスタム検索の呼び出し

インスタンスの検索結果を取得するために Custom Search API に最初の呼び出しを行う前に、Cognitive Services サブスクリプション キーを取得する必要があります。 Custom Search API のキーを取得するには、「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)」を参照してください。


## <a name="try-it-out"></a>試してみる

カスタム検索エクスペリエンスを構成した後は、Custom Search ポータル内から構成をテストできます。 

1. [カスタム検索](https://customsearch.ai)にサインインします。
2. ご利用のインスタンスの一覧からカスタム検索のインスタンスをクリックします。
3. **[運用]** タブをクリックします。 
4. **[エンドポイント]** タブで、エンドポイント (たとえば、Web API) を選択します。 表示されるエンドポイントは、ご利用のサブスクリプションによって決まります (サブスクリプションのオプションについては、[価格](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)に関するページを参照してください)。 
5. パラメーター値を指定します。 

    次に示したのは、設定できる可能性があるパラメーターです (実際の一覧は、選択したエンドポイントによって異なります)。 これらのパラメーターの詳細については、[Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) のリファレンスを参照してください。

    - **クエリ**: 検索する検索用語。 Web、Image、Video、および Autosuggest エンドポイントに対してのみ使用できます。
    - **Custom Configuration ID\(カスタム構成 ID\)**: 選択した Custom Search インスタンスの構成 ID。 このフィールドは読み取り専用です。
    - **市場**: 結果の取得元の市場です。 Web、Image、Video、および Hosted UI エンドポイントに対してのみ使用できます。
    - **サブスクリプション キー**: テストに使用するサブスクリプション キー。 ドロップダウン リストからキーを選択するか、または手動で入力します。  
      
    **[追加パラメーター]** をクリックすると、次のパラメーターが表示されます。  
      
    - **セーフ サーチ**: 成人向けコンテンツの Web ページをフィルター処理するために使用されるフィルター。 Web、Image、Video、および Hosted UI エンドポイントに対して使用できます。
    - **ユーザー インターフェイス言語**: ユーザー インターフェイスの文字列に使用される言語です。 たとえば、Hosted UI でイメージおよびビデオを有効にした場合、**[イメージ]** タブおよび **[ビデオ]** タブでは指定した言語が使用されます。
    - **カウント**: 応答で返される検索結果の数。 Web、Image、および Video エンドポイントに対してのみ使用できます。
    - **オフセット**: 結果を返す前にスキップする検索の数です。 Web、Image、および Video エンドポイントに対してのみ使用できます。

6. すべての必須オプションを指定した後、**[呼び出し]** をクリックすると、右側のウィンドウに JSON 応答が表示されます。 

Hosted UI エンドポイントを選択した場合は、下側のウィンドウで検索エクスペリエンスをテストできます。

## <a name="next-steps"></a>次の手順

- [C# を使用してカスタム ビューを呼び出す](./call-endpoint-csharp.md)
- [Java を使用してカスタム ビューを呼び出す](./call-endpoint-java.md)
- [NodeJs を使用してカスタム ビューを呼び出す](./call-endpoint-nodejs.md)
- [Python を使用してカスタム ビューを呼び出す](./call-endpoint-python.md)

- [C# SDK を使用してカスタム ビューを呼び出す](./sdk-csharp-quick-start.md)