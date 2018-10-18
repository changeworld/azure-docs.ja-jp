---
title: 'クイック スタート: API Python - ナレッジ ベースの作成 - QnA Maker'
titlesuffix: Azure Cognitive Services
description: このクイック スタートでは、Cognitive Services API アカウントの Azure ダッシュボードに表示される QnA Maker ナレッジ ベースのサンプルをプログラムから作成する手順を紹介しています。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 09/12/2018
ms.author: diberry
ms.openlocfilehash: 2646cc39197145e797f61d0c2d9e6d944ed19351
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49388461"
---
# <a name="create-a-new-knowledge-base-in-python"></a>新しいナレッジ ベースを Python で作成する

このクイック スタートでは、Cognitive Services API アカウントの Azure ダッシュボードに表示される QnA Maker ナレッジ ベースのサンプルをプログラムから作成する手順を紹介しています。

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

以下 (**req** ディクショナリ要素の "urls") に、2 つのサンプル FAQ の URL が記載されています。 QnA Maker は、FAQ などの半構造化コンテンツから質問とその回答を自動的に抽出します。詳しくは、こちらの[データ ソース](../Concepts/data-sources-supported.md)に関するドキュメントをご覧ください。 このクイック スタートの FAQ URL は、独自のものを使用してもかまいません。

## <a name="prerequisites"></a>前提条件

このコードを実行するには、[Python 3.x](https://www.python.org/downloads/) が必要です。

[Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を所有していること、また使用リソースとして **QnA Maker** が選択されていることが必要です。 [Azure ダッシュボード](https://ms.portal.azure.com/)の有料サブスクリプション キーが必要です。 キーを取得するには、ダッシュボードで **[リソース管理]** の **[キー]** を選択します。 このクイック スタートでは、どちらのキーでも問題ありません。

![Azure ダッシュボードのサービス キー](../media/sub-key.png)

Visual Studio と Python について詳しくは、「[Windows 上の Visual Studio での Python の使用](https://docs.microsoft.com/visualstudio/python/overview-of-python-tools-for-visual-studio)」をご覧ください。

## <a name="create-knowledge-base"></a>ナレッジ ベースを作成する

次のコードでは、[Create](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) メソッドを利用し、新しいサポート技術情報が作成されます。

1. 適切な IDE で新しい Python プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `subscriptionKey` 値を、有効なサブスクリプション キーに置き換えます。
4. プログラムを実行します。

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'YOUR SUBSCRIPTION KEY HERE'

# Represents the various elements used to create HTTP request path
# for QnA Maker operations.
host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/create'

'''
Formats and indents JSON for display.
:param content: The JSON to format and indent.
:type: string
:return: A string containing formatted and indented JSON.
:rtype: string
'''
def pretty_print(content):
  # Note: We convert content to and from an object so we can pretty-print it.
  return json.dumps(json.loads(content), indent=4)

'''
Sends the POST request to create the knowledge base.
:param path: The URL path being called.
:type: string
:param content: The contents of your POST.
:type: string
:return: A header that creates the knowledge base, the JSON response
:rtype: string, string
'''
def create_kb(path, content):
  print('Calling ' + host + path + '.')
  headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json',
    'Content-Length': len (content)
  }
  conn = http.client.HTTPSConnection(host)
  conn.request ("POST", path, content, headers)
  response = conn.getresponse ()
  # /knowledgebases/create returns an HTTP header named Location that contains a URL
  # to check the status of the operation in creating the knowledge base.
  return response.getheader('Location'), response.read ()

'''
Checks the status of the request to create the knowledge base.
:param path: The URL path being checked
:type: string
:return: The header Retry-After if request is not finished, the JSON response
:rtype: string, string
'''
def check_status(path):
  print('Calling ' + host + path + '.')
  headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
  conn = http.client.HTTPSConnection(host)
  conn.request("GET", path, None, headers)
  response = conn.getresponse ()
  # If the operation is not finished, /operations returns an HTTP header named Retry-After
  # that contains the number of seconds to wait before we query the operation again.
  return response.getheader('Retry-After'), response.read ()

'''
Dictionary that holds the knowledge base.
The data source includes a QnA pair with metadata, the URL for the
QnA Maker FAQ article, and the URL for the Azure Bot Service FAQ article.
'''
req = {
  "name": "QnA Maker FAQ",
  "qnaList": [
    {
      "id": 0,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/58994a073d9e04097c7ba6fe/operations/58994a073d9e041ad42d9baa",
      "source": "Custom Editorial",
      "questions": [
        "How do I programmatically update my Knowledge Base?"
      ],
      "metadata": [
        {
          "name": "category",
          "value": "api"
        }
      ]
    }
  ],
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "files": []
}

# Builds the path URL.
path = service + method
# Convert the request to a string.
content = json.dumps(req)
# Retrieve the operation ID to check status, and JSON result
operation, result = create_kb(path, content)
# Print request response in JSON with presentable formatting
print(pretty_print(result))

'''
Iteratively gets the operation state, creating the knowledge base.
Once state is no longer "Running" or "NotStarted", the loop ends.
'''
done = False
while False == done:
  path = service + operation
  # Gets the status of the operation.
  wait, status = check_status(path)
  # Print status checks in JSON with presentable formatting
  print(pretty_print(status))

  # Convert the JSON response into an object and get the value of the operationState field.
  state = json.loads(status)['operationState']
  # If the operation isn't finished, wait and query again.
  if state == 'Running' or state == 'NotStarted':
    print('Waiting ' + wait + ' seconds...')
    time.sleep(int(wait))
  else:
    done = True # request has been processed, if successful, knowledge base is created
```

## <a name="understand-what-qna-maker-returns"></a>QnA Maker から返される応答

成功した応答は、次の例に示すように JSON で返されます。 実際に返される結果は若干異なる場合があります。 最後の呼び出しで "Succeeded" 状態が返された場合、ナレッジ ベースは正常に作成されています。 トラブルシューティングについては、QnA Maker API の「[Get Operation Details (操作の詳細の取得)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)」をご覧ください。

```json
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/knowledgebases/create.
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917tb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bt4ebha1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Waiting 30 seconds...
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917bt4ebfa1aae40fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Press any key to continue.
```

作成されたナレッジ ベースは、QnA Maker ポータルの [[My knowledge bases]\(マイ ナレッジ ベース\)](https://www.qnamaker.ai/Home/MyServices) ページで確認できます。 ナレッジ ベースの名前 (QnA Maker FAQ など) を選択すると表示されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)