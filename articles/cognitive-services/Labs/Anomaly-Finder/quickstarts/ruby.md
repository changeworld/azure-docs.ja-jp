---
title: Anomaly Finder API と Ruby の使用方法 - Microsoft Cognitive Services | Microsoft Docs
description: Ruby と Cognitive Services の Anomaly Finder API の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: ca4754514ba5012f7e9e28981d0869d174561fb3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35375328"
---
# <a name="use-the-anomaly-finder-api-with-ruby"></a>Anomaly Finder API と Ruby の使用

この記事は、Anomaly Finder API と Ruby の使用をすぐに開始し、時系列データの異常検出結果を取得するタスクを実行するために役立つ情報とコード サンプルを提供します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-ruby"></a>Ruby を使用して Anomaly Finder API で異常ポイントを取得する 
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>時系列データの例
時系列データ ポイントの例は、次のとおりです。[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-ruby-example"></a>データを分析して異常ポイントを取得する Ruby の例

この例を使用する手順は次のとおりです。

1. 'gem install rest-client' を実行して [rest-client](https://github.com/rest-client/rest-client) をインストールします。
2. 以下のコードを .rb ファイルとして保存します。
3. `[YOUR_SUBSCRIPTION_KEY]` 値を、有効なサブスクリプション キーに置き換えます。
4. `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` を例または独自のデータ ポイントに置き換えます。
5. 実行し、応答を確認します。

```ruby
# https://github.com/rest-client/rest-client
require 'rest_client'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscription_key = '[YOUR_SUBSCRIPTION_KEY]';

endpoint = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

# Replace the request data with your real data.
requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

header = {
    :content_type => 'application/json',
    :'Ocp-Apim-Subscription-Key' => subscription_key
}

response = RestClient::Request.execute(
    :url => endpoint,
    :method => :post,
    :verify_ssl => true,
    :payload => requestData,
    :header => header)

# You will see the response with anomaly results
puts response.body
```

### <a name="example-response"></a>応答の例

成功応答が JSON で返されます。 応答のサンプルは次のとおりです。
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [REST API リファレンス](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
