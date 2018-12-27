---
title: 'クイック スタート: Python を使用してエンドポイントを呼び出す - Bing Custom Search'
titlesuffix: Azure Cognitive Services
description: このクイックスタートでは、Python を利用して Bing Custom Search エンドポイントを呼び出すことで、カスタム検索インスタンスから検索結果を要求する方法について紹介します。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: d7977f49a484665f133a119f491bd8986c0b6fc4
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310721"
---
# <a name="quickstart-call-bing-custom-search-endpoint-python"></a>クイック スタート: Bing Custom Search エンドポイントを呼び出す (Python)

このクイックスタートでは、Python を利用して Bing Custom Search エンドポイントを呼び出すことで、カスタム検索インスタンスから検索結果を要求する方法について紹介します。 

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

- すぐに使用できるカスタム検索インスタンス。 「[Create your first Bing Custom Search instance](quick-start.md)」 (最初の Bing Custom Search インスタンスを作成する) を参照してください。
- [Python](https://www.python.org/) がインストールされていること。
- サブスクリプション キー。 [無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)をアクティブにすると、サブスクリプション キーを取得できます。または Azure ダッシュボードから有料のサブスクリプション キーを使用することもできます ([Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を参照してください)。   「[Cognitive Services の価格 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)」も参照してください。


## <a name="run-the-code"></a>コードの実行

この例を実行するには、次の手順に従います。

1. コードのフォルダーを作成します。  
  
2. 管理者コマンド プロンプトまたはターミナルから、先ほど作成したフォルダーに移動します。  
  
3. **requests** python モジュールをインストールします。  
  
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>  
      
4. 作成したフォルダー内に BingCustomSearch.py という名前のファイルを作成し、次のコードをコピーします。 **YOUR-SUBSCRIPTION-KEY** と **YOUR-CUSTOM-CONFIG-ID** を、自分のサブスクリプション キーと構成 ID に置き換えます。  
  
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
  
7. 次のコマンドでコードを実行します。  
  
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>次の手順
- [ホストされている UI エクスペリエンスの構成](./hosted-ui.md)
- [装飾マーカーを使用してテキストを強調表示する](./hit-highlighting.md)
- [Web ページのページング](./page-webpages.md)
