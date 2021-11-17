---
title: リファレンス - Azure Web PubSub 用 Python サーバー SDK
description: このリファレンスでは、Azure Web PubSub サービス用 Python サーバー SDK について説明します。
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: f28ab302aa6899e35c080821ccddf01d5e7cb76e
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578335"
---
# <a name="python-server-sdk-for-azure-web-pubsub"></a>Azure Web PubSub 用 Python サーバー SDK

このライブラリを使用して、次の操作を実行できます。

- ハブとグループにメッセージを送信します。 
- 特定のユーザーと接続にメッセージを送信します。
- ユーザーと接続をグループに整理します。
- 接続を終了します
- 既存の接続のアクセス許可を付与、取り消し、確認します

[ソース コード](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [パッケージ (Pypi)][package] | [API リファレンス ドキュメント](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [製品ドキュメント][webpubsubservice_docs] |
[サンプル][samples_ref]

## <a name="get-started"></a>はじめに

次のようにパッケージをインストールします。

```bash
python -m pip install azure-messaging-webpubsubservice==1.0.0b1
```

### <a name="prerequisites"></a>前提条件

- このパッケージを使用するには、Python 2.7 または3.6 以降が必要です。
- [Azure サブスクリプション][azure_sub]。
- 既存の Azure Web PubSub サービス インスタンス。

### <a name="authenticate-the-client"></a>クライアントを認証する

Azure WebPubSub サービスと対話するには、[`WebPubSubServiceClient`][webpubsubservice_client_class] クラスのインスタンスを作成する必要があります。 サービスに対して認証を行うには、エンドポイントとアクセス キーを使用して AzureKeyCredential インスタンスを渡す必要があります。 エンドポイントとアクセス キーは、Azure portal で確認できます。

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential
>>> client = WebPubSubServiceClient(endpoint='<endpoint>', credential=AzureKeyCredential('somesecret'))
>>> client
<WebPubSubServiceClient endpoint:'<endpoint>'>
```

## <a name="examples"></a>例

### <a name="sending-a-request"></a>要求を送信する

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential
>>> from azure.messaging.webpubsubservice.rest import build_send_to_all_request
>>> client = WebPubSubServiceClient(endpoint='<endpoint>', credential=AzureKeyCredential('somesecret'))
>>> request = build_send_to_all_request('default', json={ 'Hello':  'webpubsub!' })
>>> request
<HttpRequest [POST], url: '/api/hubs/default/:send?api-version=2020-10-01'>
>>> response = client.send_request(request)
>>> response
<RequestsTransportResponse: 202 Accepted>
>>> response.status_code 
202
>>> with open('file.json', 'r') as f:
>>>    request = build_send_to_all_request('ahub', content=f, content_type='application/json')
>>>    response = client.send_request(request)
>>> print(response)
<RequestsTransportResponse: 202 Accepted>
```

## <a name="key-concepts"></a>主要な概念

[!INCLUDE [Terms](includes/terms.md)]

## <a name="troubleshooting"></a>トラブルシューティング

この SDK では、Python の標準ログ ライブラリを使用します。 印刷デバッグ情報のログ出力は、`stdout` または任意の場所に構成できます。

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

このログ構成を使用して、HTTP 要求と応答の詳細が `stdout` に出力されます。

[webpubsubservice_docs]: ./index.yml
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[webpubsubservice_client_class]: https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice/azure/messaging/webpubsubservice/__init__.py
[package]: https://pypi.org/project/azure-messaging-webpubsubservice/
[default_cred_ref]: https://aka.ms/azsdk-python-identity-default-cred-ref
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/python

## <a name="next-steps"></a>次のステップ

[!INCLUDE [next step](includes/include-next-step.md)]