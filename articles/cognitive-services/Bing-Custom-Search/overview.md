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
ms.openlocfilehash: b6f50844d6571cca6d63c1db7a85863e3d22d411
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948079"
---
# <a name="what-is-bing-custom-search"></a>Bing Custom Search とは

Bing Custom Search を使用すると、関心のあるトピックに合わせてカスタマイズした検索エクスペリエンスを作成できます。 たとえば、検索エクスペリエンスを提供する Web サイトを所有している場合は、Bing が検索するドメイン、Web サイト、および Web ページを指定できます。 ユーザーは、関係のないコンテンツが含まれる検索結果のページを移動し続けるのではなく、関心のあるコンテンツに合わせてカスタマイズされた検索結果を参照できます。

Web のカスタム ビューを作成するには、Bing Custom Search [ポータル](https://customsearch.ai)を使用します。 ポータルでは、Bing に検索させたいドメイン、Web サイト、および Web ページと、検索させたくない Web サイトを指定できるカスタムの検索インスタンスを作成できます。 ポータルを使うと、わかっているコンテンツの URL を指定するだけでなく、追加したい関連コンテンツを検索することもできます。

ポータルでは、ユーザーが特定の検索用語を入力した場合に、検索結果の一番上に特定の Web ページをピン留めすることもできます。 

インスタンスを定義した後、Custom Search API を呼び出すことによって、Web サイト、デスクトップ アプリ、またはモバイル アプリにカスタム検索を統合できます。 Web ベースのサイトまたはアプリケーションがある場合は、ホストされている UI に検索インターフェイスを表示させることができます。

次の画像は、カスタム検索が簡単に統合できることを示しています。

![画像 alt](./media/bcs-overview.png "Bing Custom Search の動作方法")

## <a name="adding-custom-search-box-suggestions"></a>カスタム検索ボックスの候補の追加

カスタム検索ボックスの候補を使用して、カスタム検索エクスペリエンスを強化することができます。 この機能を使うと、検索エクスペリエンスに関連するカスタムの検索候補を提供できます。 ユーザーが検索ボックスに入力すると、ユーザーの部分的なクエリ文字列に基づき、ドロップダウン リストにクエリ文字列の候補が表示されます。 カスタム検索候補のみを返すか、Bing の検索候補も含めるかを指定できます。 詳細については、[こちら](define-custom-suggestions.md)を参照してください。

## <a name="adding-custom-image-search-experience"></a>カスタム画像検索エクスペリエンスの追加

画像によりカスタム検索エクスペリエンスを強化できます。 Web の結果と同様に、カスタム検索では Web サイトのインスタンスのリストでの画像の検索がサポートされています。 詳細については、[こちら](get-images-from-instance.md)を参照してください。

## <a name="adding-custom-video-search-experience"></a>カスタム動画検索エクスペリエンスの追加

動画によりカスタム検索エクスペリエンスを強化できます。 Web の結果と同様に、カスタム検索では Web サイトのインスタンスのリストでの動画の検索がサポートされています。 詳細については、[こちら](get-videos-from-instance.md)を参照してください。

## <a name="sharing-your-custom-search-instance-with-others"></a>カスタム検索インスタンスの他のユーザーとの共有

インスタンスをチームのメンバーと共有することで、インスタンスの編集やテストを簡単に共同で行うことができます。 詳細については、[こちら](share-your-custom-search.md)を参照してください。

## <a name="next-steps"></a>次の手順

すぐに使い始める方法は、「[Create your first Bing Custom Search instance](quick-start.md)」 (最初の Bing Custom Search インスタンスを作成する) を参照してください。

検索インスタンスのカスタマイズについて詳しくは、[カスタム検索インスタンスの定義](define-your-custom-view.md)に関するページをご覧ください。

各カスタム検索エンドポイントのリファレンス コンテンツをよく理解してください。 リファレンスには、検索結果を要求するときに使用するエンドポイント、ヘッダー、クエリ パラメーターが記載されています。 また、応答オブジェクトの定義も記載されています。

- [Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [Custom Image API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [Custom Video API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [Custom Autosuggest API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)


検索結果の使用に関するルールを逸脱しないよう、[Bing の使用上および表示上の要件](./use-and-display-requirements.md)に関するページを必ず読んでください。