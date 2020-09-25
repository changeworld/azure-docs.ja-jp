---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: dademath
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: cdd4988f9a23904c0771852c4539aa9bce2ee683
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946006"
---
Communication Services JavaScript SMS クライアント ライブラリを使用して SMS メッセージを送信することによって、Azure Communication Services の使用を開始します。

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-js-pr/tree/feature/communication/sdk/communication/communication-sms) | [Package (NPM)](https://www.npmjs.com/package/@azure/communication-sms) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/) アクティブ LTS およびメンテナンス LTS バージョン (8.11.1 および 10.14.1 を推奨)。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- SMS が有効になっている電話番号。 [電話番号を取得します](../get-phone-number.md)。

### <a name="prerequisite-check"></a>前提条件のチェック

- ターミナルまたはコマンド ウィンドウで `node --version` を実行して、Node.js がインストールされていることを確認します。
- Communication Services リソースに関連付けられている電話番号を表示するには、[Azure portal](https://portal.azure.com/) にサインインし、Communication Services リソースを見つけて、左側のナビゲーション ペインから **[電話番号]** タブを開きます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

まず、ターミナルまたはコマンド ウィンドウを開き、アプリ用の新しいディレクトリを作成して、そこに移動します。

```console
mkdir sms-quickstart && cd sms-quickstart
```

既定の設定で `npm init -y` を実行して、**package.json** ファイルを作成します。

```console
npm init -y
```

テキスト エディターを使用して、プロジェクトのルート ディレクトリに **send-sms.js** というファイルを作成します。 このクイックスタートのソース コードはすべて、以降のセクションでこのファイルに追加することになります。

### <a name="install-the-package"></a>パッケージをインストールする

`npm install` コマンドを使用して、JavaScript 用の Azure Communication Services SMS クライアント ライブラリをインストールします。

```console
npm install @azure/communication-sms --save
```

`--save` オプションを使用すると、**package.json** ファイル内の依存関係としてライブラリが表示されます。

## <a name="object-model"></a>オブジェクト モデル

Node.js 用 Azure Communication Services SMS クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | このクラスは、すべての SMS 機能に必要となります。 サブスクリプション情報を使用してこれをインスタンス化し、そのインスタンスを使用して SMS を送信します。 |
| SendSmsOptions | このインターフェイスには、配信レポートを構成するためのオプションが用意されています。 `enable_delivery_report` が `true` に設定されている場合、配信が成功したときにイベントが生成されます。 |
| SendMessageRequest | このインターフェイスは、sms 要求を作成するためのモデルです (例: 発信および着信電話番号と sms コンテンツの構成)。 |

## <a name="authenticate-the-client"></a>クライアントを認証する

クライアント ライブラリから **SmsClient** をインポートし、接続文字列を使用してインスタンス化します。 次のコードは、`COMMUNICATION_SERVICES_CONNECTION_STRING` という名前の環境変数からリソースの接続文字列を取得します。 [リソースの接続文字列を管理する](../../create-communication-resource.md#store-your-connection-string)方法を参照してください。

次のコードを **send-sms.js** に追加します。

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>SMS メッセージの送信

`send` メソッドを呼び出して、SMS メッセージを送信します。 **send-sms.js** の末尾に次のコードを追加します。

```javascript
await smsClient.send({
  from: "<leased-phone-number>",
  to: ["<to-phone-number>"],
  message: "Hello World 👋🏻 via Sms"
}, {
  enableDeliveryReport: true //Optional parameter
});
```

`<leased-phone-number>` は Communication Services リソースに関連付けられている、SMS が有効になっている電話番号で置き換え、`<to-phone-number>` はメッセージの送信先の電話番号で置き換える必要があります。 すべての電話番号パラメーターは、[E.164 規格](../../../concepts/telephony-sms/plan-solution.md#optional-reading-international-public-telecommunication-numbering-plan-e164)に準拠している必要があります。

`enableDeliveryReport` パラメーターは、配信レポートを構成するために使用できる省略可能なパラメーターです。 これは、SMS メッセージが配信されたときにイベントを生成する場合に便利です。 SMS メッセージの配信レポートを構成するには、[SMS イベントの処理](../handle-sms-events.md)に関するクイックスタートを参照してください。

## <a name="run-the-code"></a>コードの実行

`node` コマンドを使用して、**send-sms.js** ファイルに追加したコードを実行します。

```console

node ./send-sms.js

```