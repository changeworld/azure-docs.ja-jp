---
title: カスタム ビューの検索 - Bing Custom Search
titlesuffix: Azure Cognitive Services
description: Web のカスタム ビューを検索する方法について説明します。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: maheshb
ms.openlocfilehash: 77a1756aba0d8473051cdf335f33ed9ca5a8fb24
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558330"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>ポータルから Bing Custom Search インスタンスを呼び出す

カスタム検索エクスペリエンスを構成した後、Bing Custom Search [ポータル](https://customsearch.ai)内でそれをテストできます。 

![Bing Custom Search ポータルのスクリーンショット](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>検索クエリを作成する 

Bing Custom Search [ポータル](https://customsearch.ai)にサインインした後、ご自分の検索インスタンスを選択し、**[運用]** タブをクリックします。**[エンドポイント]** で、API エンドポイント (たとえば、Web API) を選択します。 表示されるエンドポイントは、お使いのサブスクリプションによって決まります。

検索クエリを作成するには、エンドポイントのパラメーター値を入力します。 ポータルに表示されるパラメーターは、選択したエンドポイントに応じて変更される可能性があることに注意してください。 詳細については、[Custom Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)を参照してください。 

いくつかの重要なパラメーターを次に示します。


|パラメーター  |説明  |
|---------|---------|
|クエリ     | 検索する検索用語。 Web、Image、Video、および Autosuggest エンドポイントに対してのみ使用できます。 |
|カスタム構成 ID | 選択された Custom Search インスタンスの構成 ID。 このフィールドは読み取り専用です。 |
|市場     | 結果が引き出される市場。 Web、Image、Video、および Hosted UI エンドポイントに対してのみ使用できます。        |
|サブスクリプション キー | テストに使用するサブスクリプション キー。 ドロップダウン リストからキーを選択するか、手動で入力できます。          |

**[追加パラメーター]** をクリックすると、次のパラメーターが表示されます。  

|パラメーター  |説明  |
|---------|---------|
|セーフ サーチ     | 成人向けコンテンツの Web ページをフィルター処理するために使用されるフィルター。 Web、Image、Video、および Hosted UI エンドポイントに対してのみ使用できます。        |
|ユーザー インターフェイスの言語    | ユーザー インターフェイス文字列に使用する言語。 たとえば、Hosted UI でイメージおよびビデオを有効にした場合、**[イメージ]** タブおよび **[ビデオ]** タブでは指定した言語が使用されます。        |
|カウント     | 応答で返される検索結果の数。 Web、Image、および Video エンドポイントに対してのみ使用できます。         |
|オフセット    | 結果を返す前にスキップする検索の数です。 Web、Image、および Video エンドポイントに対してのみ使用できます。        |
    
すべての必須オプションを指定した後、**[呼び出し]** をクリックすると、右側のウィンドウに JSON 応答が表示されます。 Hosted UI エンドポイントを選択した場合は、下側のウィンドウで検索エクスペリエンスをテストできます。

## <a name="next-steps"></a>次の手順

- [C# を使用してカスタム ビューを呼び出す](./call-endpoint-csharp.md)
- [Java を使用してカスタム ビューを呼び出す](./call-endpoint-java.md)
- [NodeJs を使用してカスタム ビューを呼び出す](./call-endpoint-nodejs.md)
- [Python を使用してカスタム ビューを呼び出す](./call-endpoint-python.md)

- [C# SDK を使用してカスタム ビューを呼び出す](./sdk-csharp-quick-start.md)