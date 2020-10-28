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
ms.openlocfilehash: c8bce6afd914a22be80a57ae234e39161d182b7f
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2020
ms.locfileid: "92499153"
---
Communication Services C# SMS クライアント ライブラリを使用して SMS メッセージを送信することによって、Azure Communication Services の使用を開始します。

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- お使いのオペレーティング システムに対応した最新バージョンの [.NET Core クライアント ライブラリ](https://dotnet.microsoft.com/download/dotnet-core)。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- SMS が有効になっている電話番号。 [電話番号を取得します](../get-phone-number.md)。

### <a name="prerequisite-check"></a>前提条件のチェック

- ターミナルまたはコマンド ウィンドウで `dotnet` コマンドを実行して、.NET クライアント ライブラリがインストールされていることを確認します。
- Communication Services リソースに関連付けられている電話番号を表示するには、 [Azure portal](https://portal.azure.com/) にサインインし、Communication Services リソースを見つけて、左側のナビゲーション ペインから **[電話番号]** タブを開きます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`SmsQuickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイルを使用する単純な "Hello World" C# プロジェクトが作成されます。 **Program.cs** 。

```console
dotnet new console -o SmsQuickstart
```

新しく作成したアプリ フォルダーにディレクトリを変更し、`dotnet build` コマンドを使用して自分のアプリケーションをコンパイルします。

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>パッケージをインストールする

まだアプリケーション ディレクトリにいる間に、`dotnet add package` コマンドを使用して、.NET 用の Azure Communication Services SMS クライアント ライブラリ パッケージをインストールします。

```console
dotnet add package Azure.Communication.Sms --version 1.0.0-beta.2
```

**Program.cs** の先頭に `using` ディレクティブを追加して、`Azure.Communication` 名前空間を含めます。

```csharp

using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>オブジェクト モデル

C# 用 Azure Communication Services SMS クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                       | 説明                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | このクラスは、すべての SMS 機能に必要となります。 サブスクリプション情報を使用してこれをインスタンス化し、そのインスタンスを使用して SMS を送信します。                           |
| SendSmsOptions | このクラスには、配信レポートを構成するためのオプションが用意されています。 enable_delivery_report が True に設定されている場合、配信が成功したときにイベントが生成されます |

## <a name="authenticate-the-client"></a>クライアントを認証する

 テキスト エディターで **Program.cs** を開き、`Main` メソッドの本文を、接続文字列を使用して `SmsClient` を初期化するコードで置き換えます。 次のコードは、`COMMUNICATION_SERVICES_CONNECTION_STRING` という名前の環境変数からリソースの接続文字列を取得します。 [リソースの接続文字列を管理する](../../create-communication-resource.md#store-your-connection-string)方法を参照してください。


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>SMS メッセージの送信

Send メソッドを呼び出して、SMS メッセージを送信します。 **Program.cs** の `Main` メソッドの末尾に次のコードを追加します。

```csharp
smsClient.Send(
    from: new PhoneNumber("<leased-phone-number>"),
    to: new PhoneNumber("<to-phone-number>"),
    message: "Hello World via SMS",
    new SendSmsOptions { EnableDeliveryReport = true } // optional
);
```

`<leased-phone-number>` は Communication Services リソースに関連付けられている、SMS が有効になっている電話番号で置き換え、`<to-phone-number>` はメッセージの送信先の電話番号で置き換える必要があります。

`EnableDeliveryReport` パラメーターは、配信レポートを構成するために使用できる省略可能なパラメーターです。 これは、SMS メッセージが配信されたときにイベントを生成する場合に便利です。 SMS メッセージの配信レポートを構成するには、[SMS イベントの処理](../handle-sms-events.md)に関するクイックスタートを参照してください。

## <a name="run-the-code"></a>コードの実行

アプリケーション ディレクトリから `dotnet run` コマンドを使用してアプリケーションを実行します。

```console
dotnet run
```

## <a name="sample-code"></a>サンプル コード

サンプル アプリは [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS) からダウンロードできます
