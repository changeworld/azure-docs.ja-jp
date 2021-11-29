---
title: リファレンス - Azure Web PubSub 用 Python サーバー SDK
description: このリファレンスでは、Azure Web PubSub サービス用 Python サーバー SDK について説明します。
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 1345eec1d0868e602ba90624cd6dcc6c8635f68c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132705135"
---
# <a name="azure-web-pubsub-service-client-library-for-python"></a>Python 用 Azure Web PubSub サービス クライアント ライブラリ

[Azure Web PubSub サービス](./index.yml)は、開発者がリアルタイムの機能とパブリッシュ-サブスクライブ パターンを使って Web アプリケーションを簡単に作成できるようにするための Azure マネージド サービスです。 サーバーとクライアント間、またはクライアント間で、リアルタイムのパブリッシュ-サブスクライブ メッセージングを必要とするあらゆるシナリオに、Azure Web PubSub サービスを使用できます。 従来のリアルタイム機能は、多くの場合、サーバーからのポーリングや HTTP 要求の送信を必要としますが、そのようなリアルタイム機能にも Azure Web PubSub サービスを使用できます。

次の図に示すように、このライブラリをアプリ サーバー側で使用して、WebSocket クライアント接続を管理できます。

![このオーバーフロー図は、サービス クライアント ライブラリを使用したオーバーフローを示しています。](media/sdk-reference/service-client-overflow.png)

このライブラリは次のことに使うことができます。
- ハブとグループにメッセージを送信します。
- 特定のユーザーと接続にメッセージを送信します。
- ユーザーと接続をグループに整理します。
- 接続を終了します
- 既存の接続のアクセス許可を付与、取り消し、確認します

[ソース コード](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/webpubsub/azure-messaging-webpubsubservice) | [パッケージ (Pypi)][package] | [API リファレンス ドキュメント](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/webpubsub/azure-messaging-webpubsubservice) | [製品ドキュメント][webpubsubservice_docs]

## <a name="_disclaimer_"></a>_免責事項_

"Azure SDK Python パッケージによる Python 2.7 のサポートは、2022 年 1 月 1 日に終了します。詳しい情報やご質問については、 https://github.com/Azure/azure-sdk-for-python/issues/20691 をご覧ください。"

## <a name="getting-started"></a>作業の開始

### <a name="prerequisites"></a>前提条件

- このパッケージを使用するには、Python 2.7 または3.6 以降が必要です。
- このパッケージを使用するには、[Azure サブスクリプション][azure_sub]と [Azure WebPubSub][webpubsubservice_docs] が必要です。
- 既存の Azure Web PubSub サービス インスタンス。

### <a name="1-install-the-package"></a>1. パッケージをインストールする

```bash
python -m pip install azure-messaging-webpubsubservice
```

### <a name="2-create-and-authenticate-a-webpubsubserviceclient"></a>2. WebPubSubServiceClient を作成して認証する

[接続文字列][connection_string]を使用して `WebPubSubServiceClient` を認証することができます。

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient

>>> service = WebPubSubServiceClient.from_connection_string(connection_string='<connection_string>', hub='hub')
```

または、サービス エンドポイントとアクセス キーを使用します。

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential

>>> service = WebPubSubServiceClient(endpoint='<endpoint>', hub='hub', credential=AzureKeyCredential("<access_key>"))
```

あるいは、[Azure Active Directory ][aad_doc]を使用します。
1. [pip][pip] を使用して [`azure-identity`][azure_identity_pip] をインストールします
2. ドキュメントに従って、[Webpubsub リソースで AAD 認証を有効にします][aad_doc]
3. [DefaultAzureCredential][default_azure_credential] を使用するようにコードを更新します

    ```python
    >>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
    >>> from azure.identity import DefaultAzureCredential
    >>> service = WebPubSubServiceClient(endpoint='<endpoint>', hub='hub', credential=DefaultAzureCredential())
    ```

## <a name="key-concepts"></a>主要な概念

### <a name="connection"></a>Connection

接続は、クライアントまたはクライアント接続とも呼ばれ、Web PubSub サービスに接続されている個々の WebSocket 接続を表します。 正常に接続されると、一意の接続 ID が Web PubSub サービスによってこの接続に割り当てられます。

### <a name="hub"></a>ハブ

ハブは、一連のクライアント接続の論理的な概念です。 通常、"チャット" ハブや "通知" ハブなど、1 つの目的に 1 つのハブを使います。 クライアント接続は、作成されると 1 つのハブに接続され、その有効期間中はそのハブに属します。 異なるアプリケーションでは、異なるハブ名を使用して、1 つの Azure Web PubSub サービスを共有できます。

### <a name="group"></a>グループ

グループはハブへの接続のサブセットです。 必要な場合はいつでも、クライアント接続をグループに追加したり、グループからクライアント接続を削除したりできます。 たとえば、クライアントがチャット ルームに参加したり、クライアントがチャット ルームを離れたりするときに、このチャット ルームをグループと見なすことができます。 クライアントは複数のグループに参加できます。また、1 つのグループに複数のクライアントを含めることもできます。

### <a name="user"></a>User

Web PubSub への接続を 1 人のユーザーに属させることができます。 1 人のユーザーが複数のデバイスまたは複数のブラウザー タブに接続されている場合など、ユーザーが複数の接続を持つ場合があります。

### <a name="message"></a>Message

クライアントが接続されている場合は、WebSocket 接続を介して、アップストリーム アプリケーションにメッセージを送信したり、アップストリーム アプリケーションからメッセージを受信したりできます。

## <a name="examples"></a>例

### <a name="broadcast-messages-in-json-format"></a>JSON 形式でメッセージをブロードキャストする

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient

>>> service = WebPubSubServiceClient.from_connection_string('<connection_string>', hub='hub1')
>>> service.send_to_all(message = {
        'from': 'user1',
        'data': 'Hello world'
    })
```

WebSocket クライアントは、JSON でシリアル化されたテキストを受信します: `{"from": "user1", "data": "Hello world"}`。

### <a name="broadcast-messages-in-plain-text-format"></a>プレーンテキスト形式のブロードキャスト メッセージ

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> service = WebPubSubServiceClient.from_connection_string('<connection_string>', hub='hub1')
>>> service.send_to_all(message = 'Hello world', content_type='text/plain')
```

WebSocket クライアントは、テキストを受信します: `Hello world`。

### <a name="broadcast-messages-in-binary-format"></a>バイナリ形式でメッセージをブロードキャストする

```python
>>> import io
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> service = WebPubSubServiceClient.from_connection_string('<connection_string>', hub='hub')
>>> service.send_to_all(message=io.StringIO('Hello World'), content_type='application/octet-stream')
```
WebSocket クライアントは、バイナリ テキストを受信します: `b'Hello world'`。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="logging"></a>ログ記録

この SDK は Python 標準ログ ライブラリを使用します。
stdout または任意の場所へのログ出力デバッグ情報を構成できます。

```python
import sys
import logging
from azure.identity import DefaultAzureCredential
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient

# Create a logger for the 'azure' SDK
logger = logging.getLogger('azure')
logger.setLevel(logging.DEBUG)

# Configure a console output
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

endpoint = "<endpoint>"
credential = DefaultAzureCredential()

# This WebPubSubServiceClient will log detailed information about its HTTP sessions, at DEBUG level
service = WebPubSubServiceClient(endpoint=endpoint, hub='hub', credential=credential, logging_enable=True)
```

同様に、`logging_enable` は、詳細なログ記録が WebPubSubServiceClient で有効になっていない場合でも、1 回の呼び出しに対してこれを有効にすることができます。

```python
result = service.send_to_all(..., logging_enable=True)
```

このログ構成を使用して、Http 要求と応答の詳細が stdout に出力されます。

## <a name="next-steps"></a>次のステップ

[こちらで、その他のサンプルをご確認ください。][samples]

## <a name="contributing"></a>共同作成

このプロジェクトでは、共同作成と提案を歓迎しています。 ほとんどの共同作成では、共同作成者使用許諾契約書 (CLA) にご同意いただき、ご自身の共同作成内容を使用する権利を Microsoft に供与する権利をお持ちであり、かつ実際に供与することを宣言していただく必要があります。
詳細については、 https://cla.microsoft.com を参照してください。

pull request を送信すると、CLA を提供して PR (ラベル、コメントなど) を適宜装飾する必要があるかどうかを CLA ボットが自動的に決定します。 ボットによって提供される手順にそのまま従ってください。 この操作は、Microsoft の CLA を使用するすべてのリポジトリについて、1 回だけ行う必要があります。

このプロジェクトでは、[Microsoft オープン ソースの倫理規定][code_of_conduct]を採用しています。 詳細については、「倫理規定の FAQ」をご覧ください。追加の質問やコメントがある場合は opencode@microsoft.com にお問い合わせください。

<!-- LINKS -->
[webpubsubservice_docs]: ./index.yml
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[package]: https://pypi.org/project/azure-messaging-webpubsubservice/
[default_cred_ref]: https://aka.ms/azsdk-python-identity-default-cred-ref
[cla]: https://cla.microsoft.com
[code_of_conduct]: https://opensource.microsoft.com/codeofconduct/
[coc_faq]: https://opensource.microsoft.com/codeofconduct/faq/
[coc_contact]: mailto:opencode@microsoft.com
[azure_identity_credentials]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity#credentials
[azure_identity_pip]: https://pypi.org/project/azure-identity/
[default_azure_credential]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity#defaultazurecredential
[pip]: https://pypi.org/project/pip/
[enable_aad]: howto-develop-create-instance.md
[api_key]: howto-websocket-connect.md#authorization
[connection_string]: howto-websocket-connect.md#authorization
[azure_portal]: howto-develop-create-instance.md
[azure-key-credential]: https://aka.ms/azsdk-python-core-azurekeycredential
[aad_doc]: howto-authorize-from-application.md
[samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/webpubsub/azure-messaging-webpubsubservice/samples