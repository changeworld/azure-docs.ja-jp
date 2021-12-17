---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 26c60bd88ad53936f2a2440eee9c726ebb469c76
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091838"
---
[リファレンス ドキュメント](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/)


## <a name="prerequisites"></a>前提条件

* 最新バージョンの [cURL](https://curl.haxx.se/)。
* Azure サブスクリプションを入手したら、Azure portal で<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="言語リソースを作成する"  target="_blank">言語リソースを作成</a>して、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

> [!NOTE]
> * 以降に掲載した BASH の例では、行連結文字として `\` を使用しています。 ご利用のコンソールまたはターミナルで異なる行連結文字が使用されている場合は、その文字を使用してください。
> * 言語固有のサンプルについては [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code) を参照してください。
> * Azure portal に移動し、前提条件で作成した言語リソースのキーとエンドポイントを探します。 それらは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 次に、以下のコード内の文字列を実際のキーとエンドポイントに置き換えます。
API を呼び出すには、次の情報が必要です。


|パラメーター  |Description  |
|---------|---------|
|`-X POST <endpoint>`     | API にアクセスするためのエンドポイントを指定します。        |
|`-H Content-Type: application/json`     | JSON データを送信するためのコンテンツ タイプ。          |
|`-H "Ocp-Apim-Subscription-Key:<key>`    | API にアクセスするためのキーを指定します。        |
|`-d <documents>`     | 送信するドキュメントを含む JSON。         |

次の cURL コマンドは、BASH シェルから実行されます。 これらのコマンドは、実際のリソース名、リソース キー、JSON の値に合わせて編集してください。


## <a name="key-phrase-extraction"></a>キー フレーズの抽出

[!INCLUDE [REST API quickstart instructions](../../../includes/rest-api-instructions.md)]

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1/keyPhrases \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```

### <a name="json-response"></a>JSON 応答

```json
{
   "documents":[
      {
         "id":"1",
         "keyPhrases":[
            "wonderful trip",
            "Seattle"
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2021-06-01"
}
```
