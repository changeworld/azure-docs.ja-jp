---
title: Bing Custom Search とは | Microsoft Docs
description: Bing Custom Search の概要を示します
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: 7cd61fc63d0d7734b842ed222c67c6753da9a418
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374736"
---
# <a name="what-is-bing-custom-search"></a>Bing Custom Search とは

Bing Custom Search を使用すると、関心のあるトピックに合わせてカスタマイズした検索エクスペリエンスを作成できます。 たとえば、検索エクスペリエンスを提供する Web サイトを所有している場合は、Bing が検索するドメイン、Web サイト、および Web ページを指定できます。 ユーザーは、関係のないコンテンツが含まれる検索結果のページを移動し続けるのではなく、関心のあるコンテンツに合わせてカスタマイズされた検索結果を参照できます。

Web のカスタム ビューを作成するには、Bing Custom Search [ポータル](https://customsearch.ai)を使用します。 ポータルでは、Bing に検索させたいドメイン、Web サイト、および Web ページと、検索させたくない Web サイトを指定できるカスタムの検索インスタンスを作成できます。 ポータルを使うと、わかっているコンテンツの URL を指定するだけでなく、追加したい関連コンテンツを検索することもできます。

ポータルでは、ユーザーが特定の検索用語を入力した場合に、検索結果の一番上に特定の Web ページをピン留めすることもできます。 

インスタンスを定義した後、Custom Search API を呼び出すことによって、Web サイト、デスクトップ アプリ、またはモバイル アプリにカスタム検索を統合できます。 Web ベースのサイトまたはアプリケーションがある場合は、ホストされている UI に検索インターフェイスを表示させることができます。

次の画像は、カスタム検索が簡単に統合できることを示しています。

![画像 alt](./media/bcs-overview.png "Bing Custom Search の動作方法")

## <a name="customize-search-suggestions"></a>検索候補のカスタマイズ

適切なレベルで Custom Search をサブスクライブしている場合 ([料金ページ](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)を参照してください)、Custom Search エクスペリエンスの検索候補をカスタマイズすることができます。 Custom Autosuggest API は、ユーザーが指定した部分的なクエリ文字列に基づいて、推奨されるクエリの一覧を返します。 Custom Autosuggest を使うと、検索エクスペリエンスに関連するカスタムの検索候補を提供できます。 カスタム候補のみを返すか Bing の候補を含めるかどうかは、指定できます。 Bing の候補が含まれる場合、カスタム候補は Bing が提供する候補の前に表示されます。 Bing の候補は、Custom Search インスタンスのコンテキストに制限されます。

## <a name="next-steps"></a>次の手順

すぐに使い始める方法は、「[Create your first Bing Custom Search instance](quick-start.md)」 (最初の Bing Custom Search インスタンスを作成する) を参照してください。

検索インスタンスのカスタマイズに使用可能なオプションについては、「[Define a custom search instance](define-your-custom-view.md)」 (カスタム検索インスタンスの定義) を参照してください。

[Custom Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)を理解します。 リファレンスには、検索結果を要求する際に使用するエンドポイント、ヘッダー、クエリ パラメーターの一覧が記載されています。 また、応答オブジェクトの定義も記載されています。

候補をカスタマイズする方法については、「[Define custom search suggestions](define-custom-suggestions.md)」 (カスタム検索候補を定義する) を参照してください。

検索結果の使用に関するルールを逸脱しないよう、[Bing の使用上および表示上の要件](./use-and-display-requirements.md)に関するページを必ず読んでください。