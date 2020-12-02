---
title: 'クイック スタート: 初めての Bing Custom Search インスタンスを作成する'
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、自分で定義したドメインや Web ページを検索できるカスタム Bing インスタンスを作成します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 726a60d611abc392bc1a4629c5088ca4c6b703d9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338335"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>クイック スタート: 初めての Bing Custom Search インスタンスを作成する

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関するページを参照してください。

Bing Custom Search を使用するには、Web のビューまたはスライスを定義するカスタム検索のインスタンスを作成する必要があります。 このインスタンスには、検索するパブリック ドメイン、Web サイト、Web ページが、必要なランク付けの調整と共に含まれます。 

インスタンスを作成するには、[Bing Custom Search ポータル](https://customsearch.ai)を使用します。 

![Bing Custom Search ポータルの画像](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>[前提条件]

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>カスタム検索インスタンスの作成

Bing Custom Search インスタンスを作成するには:

1. [Bing Custom Search ポータル](https://customsearch.ai) Web ページの **[Get Started]\(作業の開始\)** をクリックして、お使いの Microsoft アカウントでサインインします。

2. **[新しいインスタンス]** をクリックして、わかりやすい名前を入力します。 インスタンスの名前はいつでも変更できます。
 
3. **[検索エクスペリエンス]** で **[アクティブ]** タブをクリックし、検索に含める 1 つまたは複数の Web サイトの URL を入力します。 

    > [!NOTE]
    > Bing Custom Search インスタンスから返されるのは、既に Bing によってインデックスが作成されているパブリックな Web ページおよびドメインの結果だけです。

4. Bing Custom Search ポータルの右側を使用して、クエリを入力したり、検索インスタンスから返された検索結果を観察したりすることができます。 まったく結果が返されない場合は、別の URL を入力してみてください。  

5. **[発行]** をクリックすると、変更内容が運用環境に発行され、インスタンスのエンドポイントが更新されます。

6.  **[エンドポイント]** の下の **[実稼働]** タブをクリックし、自分の **カスタム構成 ID** をコピーします。 Custom Search API を呼び出すには、この ID が必要となります。実際の呼び出しの `customconfig=` クエリ パラメーターにこの ID を追加します。


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [クイック スタート: Bing Custom Search エンドポイントを呼び出す](./call-endpoint-csharp.md)