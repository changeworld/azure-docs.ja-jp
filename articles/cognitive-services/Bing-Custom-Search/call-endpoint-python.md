---
title: Python を利用してエンドポイントを呼び出す - Bing Custom Search - Microsoft Cognitive Services
description: このクイックスタートでは、Python を利用して Bing Custom Search エンドポイントを呼び出すことで、カスタム検索インスタンスから検索結果を要求する方法について紹介します。
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 88bf82805ba46abf79b7899e0428a83485062302
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504969"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Bing Custom Search エンドポイントを呼び出す (Python)

このクイックスタートでは、Python を利用して Bing Custom Search エンドポイントを呼び出すことで、カスタム検索インスタンスから検索結果を要求する方法について紹介します。 

## <a name="prerequisites"></a>前提条件
このクイック スタートを完了するには、次のものが必要です。

- カスタム検索インスタンス。 「[Create your first Bing Custom Search instance](quick-start.md)」 (最初の Bing Custom Search インスタンスを作成する) を参照してください。

-  [Python](https://www.python.org/) がインストールされていること。

- [Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と **Bing Search API**。 このクイックスタートには[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)で十分です。 無料試用版を起動するとき、アクセス キーを入力する必要があります。または、Azure ダッシュボードの有料サブスクリプション キーを使用できます。 

## <a name="run-the-code"></a>コードの実行

Bing Custom Search エンドポイントは次の手順で呼び出します。

1. コードのフォルダーを作成します。

2. 管理者コマンド プロンプトまたはターミナルから、先ほど作成したフォルダーに移動します。

3. **requests** python モジュールをインストールします。

    <pre>
    pip install pipenv
    pipenv install requests
    </pre>
    
7. BingCustomSearch.py ファイルを作成し、それに次のコードをコピーします。

8. **YOUR-SUBSCRIPTION-KEY** と **YOUR-CUSTOM-CONFIG-ID** をそれぞれ、自分のキーと構成 ID に変更します (手順 1 参照)。

    ``` Python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=' + searchTerm + '&customconfig=' + customConfigId
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```
9. 次のコマンドでコードを実行します。
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>次の手順
- [ホストされている UI エクスペリエンスの構成](./hosted-ui.md)
- [装飾マーカーを使用してテキストを強調表示する](./hit-highlighting.md)
- [Web ページのページング](./page-webpages.md)
