---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 4be8821a949527fefcc9005b1de7f4f7c438c568
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "90945750"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python](https://www.python.org/downloads/) 2.7、3.5 以降
- アクティブな Communication Services リソースと接続文字列。 [Communication Services のリソースを作成する](../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

1. ターミナルまたはコマンド ウィンドウを開き、アプリ用の新しいディレクトリを作成し、そこに移動します。

   ```console
   mkdir user-tokens-quickstart && cd user-tokens-quickstart
   ```

1. テキスト エディターを使用して、**issue-tokens.py** という名前のファイルをプロジェクトのルート ディレクトリに作成し、基本的な例外処理を含むプログラムの構造を追加します。 このクイックスタートのソース コードはすべて、以降のセクションでこのファイルに追加します。

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - User Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>パッケージをインストールする

まだアプリケーション ディレクトリにいる間に、`pip install` コマンドを使用して、Python 用の Azure Communication Services 管理クライアント ライブラリ パッケージをインストールします。

```console
pip install azure-communication-administration
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>クライアントを認証する

接続文字列を使用して `CommunicationIdentityClient` をインスタンス化します。 次のコードは、 `COMMUNICATION_SERVICES_CONNECTION_STRING` という名前の環境変数からリソースの接続文字列を取得します。 [リソースの接続文字列を管理する](../create-communication-resource.md#store-your-connection-string)方法について確認してください。

このコードを `try` ブロック内に追加します。

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

## <a name="create-a-user"></a>ユーザーを作成する

Azure Communication Services は、軽量の ID ディレクトリを保持します。 `create_user` メソッドを使用して、一意の `Id` を持つディレクトリに新しいエントリを作成します。 お使いのアプリケーションのユーザーと、Communication Services で生成された ID の間のマッピングを (アプリケーション サーバーのデータベースにこれらを格納するなどによって) 維持する必要があります。

```python
user = client.create_user()
print("\nCreated a user with ID: " + user.identifier + ":")
```

## <a name="issue-user-access-tokens"></a>ユーザー アクセス トークンを発行する

Communication Services ユーザーのアクセス トークンを発行するには、`issue_token` メソッドを使用します。 オプションの `user` パラメーターを指定しない場合は、新しいユーザーが作成され、トークンと共に返されます。

```python
# Issue an access token with the "voip" scope for a new user
token_result = client.issue_token(user, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued a token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

ユーザー アクセス トークンは、ユーザーのサービス中断を防ぐために再発行する必要がある、有効期間の短い資格情報です。 `expires_on` 応答プロパティは、トークンの有効期間を示します。

## <a name="revoke-user-access-tokens"></a>ユーザー アクセス トークンを取り消す

場合によっては、ユーザーがサービスへの認証に使用するパスワードを変更したときなどに、ユーザー アクセス トークンを明示的に取り消す必要があります。 この機能は、Azure Communication Services 管理クライアント ライブラリを通じて利用できます。

```python  
client.revoke_tokens(user)
print("\nSuccessfully revoked all tokens for user with ID: " + user.identifier)
```

## <a name="delete-a-user"></a>ユーザーの削除

ID を削除すると、すべてのアクティブなトークンが取り消され、その ID についての後続のトークンを発行できなくなります。 また、ユーザーに関連付けられているすべての永続化されたコンテンツも削除されます。

```python
client.delete_user(user)
print("\nDeleted the user with ID: " + user.identifier)
```

## <a name="run-the-code"></a>コードの実行

コンソール プロンプトから、*issue-token.py* ファイルが格納されているディレクトリに移動し、次の `python` コマンドを実行してアプリを実行します。

```console
python ./issue-token.py
```
