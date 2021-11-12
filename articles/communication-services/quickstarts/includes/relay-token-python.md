---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: rahulva
manager: shahen
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 07/30/2021
ms.topic: include
ms.custom: include file
ms.author: rahulva
ms.openlocfilehash: 35319f125578143abc2518d8037e0050dd9a6041
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893263"
---
> [!NOTE]
> このクイックスタートの最終的なコードは [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/relay-token-quickstart) にあります

## <a name="prerequisites-for-python"></a>Python の前提条件

 アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python](https://www.python.org/downloads/) 2.7 または 3.6 以降。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services のリソースを作成する](../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

1. ターミナルまたはコマンド ウィンドウを開き、自分のアプリ用に新しいディレクトリを作成し、そこに移動します。

   ```console
   mkdir access-tokens-quickstart && cd relay-tokens-quickstart
   ```

2. テキスト エディターを使用して、**issue-relay-tokens.py** という名前のファイルをプロジェクトのルート ディレクトリに作成し、基本的な例外処理を含むプログラムの構造を追加します。 このクイックスタートのソース コードはすべて、以降のセクションでこのファイルに追加します。

   ```python
    import os
    from azure.communication.networktraversal import CommunicationRelayClient
    from azure.identity import DefaultAzureCredential
    from azure.communication.identity import CommunicationIdentityClient
    
    try:
      print("Azure Communication Services - Access Relay Configuration  Quickstart")
      # Quickstart code goes here
    except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>パッケージをインストールする

引き続きアプリケーション ディレクトリで、`pip install` コマンドを使用して、Python 用の Azure Communication Services ID SDK パッケージをインストールします。

```console
pip install azure-communication-identity
pip install azure.communication.networktraversal
```

## <a name="authenticate-the-client"></a>クライアントを認証する

リソースのアクセス キーとエンドポイントを使用して、`CommunicationIdentityClient` をインスタンス化します。 次のコードは、 `COMMUNICATION_SERVICES_CONNECTION_STRING` という名前の環境変数からリソースの接続文字列を取得します。 [リソースの接続文字列を管理する](../create-communication-resource.md#store-your-connection-string)方法について確認してください。 

このコードを `try` ブロック内に追加します。

```python

# You can find your endpoint and access token from your resource in the Azure Portal
connection_str = "endpoint=ENDPOINT;accessKey=KEY"
endpoint = "https://<RESOURCE_NAME>.communication.azure.com"

# To use Azure Active Directory Authentication (DefaultAzureCredential) make sure to have
# AZURE_TENANT_ID, AZURE_CLIENT_ID and AZURE_CLIENT_SECRET as env variables.
# We also need Identity client to get a User Identifier
identity_client = CommunicationIdentityClient(endpoint, DefaultAzureCredential())
relay_client = CommunicationRelayClient(endpoint, DefaultAzureCredential())

#You can also authenticate using your connection string
identity_client = CommunicationIdentityClient.from_connection_string(self.connection_string)
relay_client = CommunicationRelayClient.from_connection_string(self.connection_string)
```

## <a name="create-a-user-from-identity"></a>ID からユーザーを作成する 

Azure Communication Services は、軽量の ID ディレクトリを保持します。 `create_user` メソッドを使用して、一意の `Id` を持つディレクトリに新しいエントリを作成します。 受け取った ID を、アプリケーションのユーザーへのマッピングと共に格納します。 これらは、アプリケーション サーバーのデータベースなどに格納します。 ID は、後でアクセス トークンを発行するために必要になります。

```python
user = identity_client.create_user()
```

## <a name="getting-the-relay-configuration"></a>リレー構成の取得
Azure Communication トークン サービスを呼び出して、ユーザー アクセス トークンを TURN サービス トークンと交換する

```python
relay_configuration = relay_client.get_relay_configuration(user)

for iceServer in config.ice_servers:
    assert iceServer.username is not None
    print('Username: ' + iceServer.username)

    assert iceServer.credential is not None
    print('Credential: ' + iceServer.credential)
    
    assert iceServer.urls is not None
    for url in iceServer.urls:
        print('Url:' + url)
```     

## <a name="run-the-code"></a>コードの実行

コンソール プロンプトから、*issue-relay-tokens.py* ファイルが格納されているディレクトリに移動し、次の `python` コマンドを実行してアプリを実行します。

```console
python ./issue-relay-tokens.py
```
