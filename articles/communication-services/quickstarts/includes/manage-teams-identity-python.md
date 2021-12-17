---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 60c534a64bae703de30cd7aaddd1da31d27e750b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461211"
---
## <a name="set-up-prerequisites"></a>前提条件の設定

- [Python](https://www.python.org/downloads/) 2.7 または 3.6 以降。

## <a name="set-up"></a>設定

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

1. ターミナルまたはコマンド ウィンドウを開き、自分のアプリ用に新しいディレクトリを作成し、そこに移動します。

   ```console
   mkdir communication-access-tokens-quickstart && cd communication-access-tokens-quickstart
   ```

1. テキスト エディターを使用して、`exchange-communication-access-tokens.py` という名前のファイルをプロジェクトのルート ディレクトリに作成し、基本的な例外処理を含むプログラムの構造を追加します。 このクイックスタートのソース コードはすべて、以降のセクションでこのファイルに追加します。

   ```python
   import os
   from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

   try:
      print("Azure Communication Services - Access Tokens Quickstart")
      # Quickstart code goes here
   except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>パッケージをインストールする

引き続きアプリケーション ディレクトリで、`pip install` コマンドを使用して、Python 用の Azure Communication Services ID SDK パッケージをインストールします。

```console
pip install azure-communication-identity
pip install msal
```

### <a name="step-1-receive-the-azure-ad-user-token-via-the-msal-library"></a>手順 1: MSAL ライブラリを使用して Azure AD ユーザー トークンを受け取る

トークン交換フローの最初の手順は、[Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md) を使用して、Teams ユーザーのトークンを取得することです。

```python
from msal.application import PublicClientApplication

client_id = "<contoso_application_id>"
tenant_id = "<contoso_tenant_id>"
authority = "https://login.microsoftonline.com/%s" % tenant_id

app = PublicClientApplication(client_id, authority=authority)

scope = [ "https://auth.msft.communication.azure.com/VoIP" ]
teams_token_result = app.acquire_token_interactive(scope)
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>手順 2: CommunicationIdentityClient を初期化する

接続文字列を使用して `CommunicationIdentityClient` をインスタンス化します。 次のコードは、 `COMMUNICATION_SERVICES_CONNECTION_STRING` という名前の環境変数からリソースの接続文字列を取得します。 [リソースの接続文字列を管理する](../create-communication-resource.md#store-your-connection-string)方法について確認してください。

このコードを `try` ブロック内に追加します。

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>手順 3: Azure AD ユーザー トークンを Teams アクセス トークンと交換する

`get_token_for_teams_user` メソッドを使用して、Azure Communication Services SDK で使用できる Teams ユーザーのアクセス トークンを発行します。

```python
token_result = client.get_token_for_teams_user(teams_token_result['access_token'])
print("Token: " + token_result.token)
```

## <a name="run-the-code"></a>コードの実行

コンソール プロンプトから、*exchange-teams-access-tokens.py* ファイルが格納されているディレクトリに移動し、次の `python` コマンドを実行してアプリを実行します。

```console
python ./exchange-communication-access-tokens.py
```
