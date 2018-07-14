---
title: Project URL Preview の Python のクイック スタート - Microsoft Cognitive Services | Microsoft Docs
description: Azure 上の Microsoft Cognitive Services の Project URL Preview の使用をすばやく開始するためのスクリプト サンプル。
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 78b2d83b02aa9ea32509029c7456e04e420b8572
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376248"
---
# <a name="url-preview-python-quickstart"></a>URL Preview の Python のクイック スタート

次の Python の例では、SwiftKey Web サイト (https://swiftkey.com/en) の URL Preview を作成します。

## <a name="prerequisites"></a>前提条件

[Cognitive Services Labs](https://aka.ms/answersearchsubscription) で無料試用版のアクセス キーを取得します。

この例では Python 3.6 を使用します。

## <a name="code-scenario"></a>シナリオのコードを書く 

次のコードは、URL Preview を作成します。
これは、次の手順で実装されます。
1. エンドポイントをホストとパスで指定する変数を宣言します。
2. プレビューするクエリ URL を指定し、クエリ パラメーターを追加します。  
3. クエリ パラメーターを設定します。
4. 要求を作成し、*Ocp-Apim-Subscription-Key* ヘッダーを追加する Search 関数を定義します。
5. *Ocp-Apim-Subscription-Key* ヘッダーを設定します。 
6. 接続を作成し、要求を送信します。
7. JSON の結果を出力します。

このデモの完全なコードを次に示します。

````
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'your-subscription-key'

host = 'api.labs.cognitive.microsoft.com'
path = '/urlpreview/v7.0/search'

query = 'https://SwiftKey.com'

params = '?q=' + urllib.parse.quote (query)

def get_preview ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_preview ()
print (json.dumps(json.loads(result), indent=4))
````
## <a name="next-steps"></a>次の手順
- [C# のクイック スタート](csharp.md)
- [Java のクイック スタート](java-quickstart.md)
- [JavaScript のクイック スタート](javascript.md)
- [Node URL のクイック スタート](node-quickstart.md)