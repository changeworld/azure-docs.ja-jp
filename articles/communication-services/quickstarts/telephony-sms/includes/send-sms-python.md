---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: danieldoolabh
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 09/03/2020
ms.topic: include
ms.custom: include file
ms.author: dadoolab
ms.openlocfilehash: a24d9531b7b2d2d2f31eec275da7db7e48b9c74a
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2020
ms.locfileid: "96615942"
---
Communication Services Python SMS クライアント ライブラリを使用して SMS メッセージを送信することによって、Azure Communication Services の使用を開始します。

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)--> 

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- [Python](https://www.python.org/downloads/) 2.7、3.5 以降
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- SMS が有効になっている電話番号。 [電話番号を取得します](../get-phone-number.md)。

### <a name="prerequisite-check"></a>前提条件のチェック

- ターミナルまたはコマンド ウィンドウで `python --version` コマンドを実行して、Python がインストールされていることを確認します。
- Communication Services リソースに関連付けられている電話番号を表示するには、[Azure portal](https://portal.azure.com/) にサインインし、Communication Services リソースを見つけて、左側のナビゲーション ペインから **[電話番号]** タブを開きます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開き、アプリ用の新しいディレクトリを作成して、そこに移動します。

```console
mkdir sms-quickstart && cd sms-quickstart
```

テキスト エディターを使用して、**send-sms.py** という名前のファイルをプロジェクトのルート ディレクトリに作成し、基本的な例外処理を含むプログラムの構造を追加します。 このクイックスタートのソース コードはすべて、以降のセクションでこのファイルに追加することになります。

```python
import os
from azure.communication.sms import PhoneNumber
from azure.communication.sms import SendSmsOptions
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>パッケージをインストールする

まだアプリケーション ディレクトリにいる間に、`pip install` コマンドを使用して、Python 用の Azure Communication Services SMS クライアント ライブラリ パッケージをインストールします。

```console
pip install azure-communication-sms --pre
```

## <a name="object-model"></a>オブジェクト モデル

Python 用 Azure Communication Services SMS クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | このクラスは、すべての SMS 機能に必要となります。 サブスクリプション情報を使用してこれをインスタンス化し、そのインスタンスを使用して SMS を送信します。 |
| SendSmsOptions | このクラスには、配信レポートを構成するためのオプションが用意されています。 enable_delivery_report が True に設定されている場合、配信が成功したときにイベントが生成されます |

## <a name="authenticate-the-client"></a>クライアントを認証する

接続文字列を使用して **SmsClient** をインスタンス化します。 次のコードは、`COMMUNICATION_SERVICES_CONNECTION_STRING` という名前の環境変数からリソースの接続文字列を取得します。 [リソースの接続文字列を管理する](../../create-communication-resource.md#store-your-connection-string)方法を参照してください。

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.getenv('COMMUNICATION_SERVICES_CONNECTION_STRING')

# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(connection_string)
```

## <a name="send-an-sms-message"></a>SMS メッセージの送信

Send メソッドを呼び出して、SMS メッセージを送信します。 **send-sms.py** の `try` ブロックの末尾に、次のコードを追加します。

```python

# calling send() with sms values
sms_response = sms_client.send(
        from_phone_number=PhoneNumber("<leased-phone-number>"),
        to_phone_numbers=[PhoneNumber("<to-phone-number>")],
        message="Hello World via SMS",
        send_sms_options=SendSmsOptions(enable_delivery_report=True)) # optional property

```

`<leased-phone-number>` は通信サービス リソースに関連付けられている、SMS が有効になっている電話番号で置き換え、`<to-phone-number>` はメッセージの送信先の電話番号で置き換える必要があります。 

`send_sms_options` パラメーターは、配信レポートを構成するために使用できる省略可能なパラメーターです。 これは、SMS メッセージが配信されたときにイベントを生成する場合に便利です。 SMS メッセージの配信レポートを構成するには、[SMS イベントの処理](../handle-sms-events.md)に関するクイックスタートを参照してください。

## <a name="run-the-code"></a>コードの実行

アプリケーション ディレクトリから `python` コマンドを使用してアプリケーションを実行します。

```console
python send-sms.py
```
