---
title: 'Bing Custom Search: カスタム ビューの検索 | Microsoft Docs'
description: Web のカスタム ビューを検索する方法について説明します
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 75f6c8d299c7eed901dda0631fca74b040f72e30
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374696"
---
# <a name="call-your-custom-search"></a>カスタム検索の呼び出し
インスタンスの検索結果を取得するために Custom Search API に最初の呼び出しを行う前に、Cognitive Services サブスクリプション キーを取得する必要があります。 Custom Search API のキーを取得するには、「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)」を参照してください。

> [!NOTE]
> 2017 年 10 月 15 日以前にプロビジョニングされたプレビュー キーを保有する既存の Bing Custom Search カスタマーは、2017 年 11 月 30 日まで、または許可されている最大数のクエリをｔ使い切るまで、そのキーをご利用いただけます。 その後は、Azure での一般提供版への移行が必要になります。

## <a name="try-it-out"></a>試してみる
カスタム検索エクスペリエンスを構成した後は、Custom Search ポータル内から構成をテストできます。 [Custom Search](https://customsearch.ai) にサインインし、Custom Search インスタンスをクリックして、**[実稼働]** タブをクリックします。**[エンドポイント]** タブが表示されます。 お使いのサブスクリプションに応じて、どのエンドポイントを試用できるかが決まります。詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)を参照してください。 エンドポイントをテストするには、ドロップダウンから該当のエンドポイントを選択し、関連する構成オプションを設定します。 

使用可能なオプションを次に示します。

- **クエリ**: 検索する検索用語。 Web、Image、および Autosuggest エンドポイントに対してのみ使用できます。
- **Custom Configuration ID\(カスタム構成 ID\)**: 選択した Custom Search インスタンスの構成 ID。 このフィールドは読み取り専用です。
- **市場**: 結果の取得元の市場です。 Web、Image、および Hosted UI エンドポイントに対してのみ使用できます。
- **サブスクリプション キー**: テストに使用するサブスクリプション キー。 ドロップダウン リストからキーを選択するか、手動で入力します。
- **セーフ サーチ**: 成人向けコンテンツの Web ページをフィルター処理するために使用されるフィルター。 Web、Image、および Hosted UI エンドポイントに対してのみ使用できます。
- **カウント**: 応答で返される検索結果の数。 Web および Image エンドポイントに対してのみ使用できます。
- **オフセット**: 応答で返される検索結果の数。 Web および Image エンドポイントに対してのみ使用できます。

Web、Image、または Autosuggest のすべての必須オプションを指定した後、**[呼び出し]** をクリックすると、右側のウィンドウに JSON 応答が表示されます。 

Hosted UI エンドポイントを選択した場合は、右側のウィンドウから検索エクスペリエンスをテストできます。

## <a name="next-steps"></a>次の手順
- [C# を使用してカスタム ビューを呼び出す](./call-endpoint-csharp.md)
- [Java を使用してカスタム ビューを呼び出す](./call-endpoint-java.md)
- [NodeJs を使用してカスタム ビューを呼び出す](./call-endpoint-nodejs.md)
- [Python を使用してカスタム ビューを呼び出す](./call-endpoint-python.md)