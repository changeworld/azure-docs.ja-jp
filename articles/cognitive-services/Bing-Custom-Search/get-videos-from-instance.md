---
title: カスタム ビューから動画を取得する - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Bing Custom Search を使用して、Web のカスタム ビューから動画を取得する方法の概要です。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.openlocfilehash: 1266349f169875d788ad65f7fc6fa7382908e31b
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130039165"
---
# <a name="get-videos-from-your-custom-view"></a>カスタム ビューから動画を取得する

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

Bing Custom Videos Search を使用すると、動画でカスタム検索エクスペリエンスを強化することができます。 Web の結果と同様に、カスタム検索では Web サイトのインスタンスのリストでの動画の検索がサポートされています。 Bing Custom Videos Search API または Hosted UI 機能を使用して、動画を取得できます。 ホステッド UI 機能は簡単に使用でき、検索エクスペリエンスを短時間で稼働させる場合に推奨されます。 動画を含むように ホステッド UI を構成する方法について詳しくは、「[ホステッド UI エクスペリエンスを構成する](hosted-ui.md)」をご覧ください。

検索結果の表示をより詳細に制御したい場合は、Bing Custom Video Search API を使用することができます。 API の呼び出しは、Bing Video Search API の呼び出しと似ているので、API 呼び出しの例については、「[Bing Video Search](../bing-video-search/overview.md)」をご覧ください。 ただしその前に、[Custom Videos Search API リファレンス](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)の内容をよく理解してください。 主な違いは、サポートされているクエリ パラメーター (customConfig クエリ パラメーターを含める必要があります) と、要求送信先のエンドポイントです。

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->