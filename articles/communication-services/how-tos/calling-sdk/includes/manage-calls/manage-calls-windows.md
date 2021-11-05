---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 9737c4e756e034c88800264e5cc56a4ca1f81741
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253334"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-windows.md)]

### <a name="request-access-to-the-microphone"></a>マイクへのアクセスを要求する

アプリを正常に実行するには、マイクへのアクセスが必要です。 UWP アプリでは、マイク機能をアプリ マニフェスト ファイルで宣言する必要があります。 それを行うための手順を次に示します。

1. `Solution Explorer` パネルで、`.appxmanifest` 拡張子が付いたファイルをダブルクリックします。
2. [`Capabilities`] タブをクリックします。
3. 機能の一覧から [`Microphone`] チェック ボックスをオンにします。

### <a name="create-ui-buttons-to-place-and-hang-up-the-call"></a>通話の発信と終了を行うための UI ボタンを作成する

この単純なサンプル アプリには、2 つのボタンが含まれます。 1 つは呼び出しを行い、もう 1 つは通話を終了します。
次の手順では、これらのボタンをアプリに追加する方法を示します。

1. `Solution Explorer` パネルで、`MainPage.xaml` という名前のファイルをダブルクリックします。
2. 中央のパネルで、UI プレビューの下にある XAML コードを探します。
3. `<Grid>` から `</Grid>` までの XAML コードを次の抜粋に置き換えます。
```xml
<StackPanel Orientation="Horizontal" VerticalAlignment="Center" HorizontalAlignment="Center">
    <Button x:Name="callButton" Click="CallHandler" Margin="10,10,10,10" HorizontalAlignment="Stretch" VerticalAlignment="Stretch">Call</Button>
    <Button x:Name="hangupButton" Click="HangupHandler" Margin="10,10,10,10" HorizontalAlignment="Stretch" VerticalAlignment="Stretch">Hang up</Button>
</StackPanel>
```

### <a name="setting-up-the-app-with-calling-sdk-apis"></a>Calling SDK API を使用したアプリの設定

Calling SDK API は、2 つの異なる名前空間に含まれています。
次の手順では、これらの名前空間について C# コンパイラに通知し、Visual Studio の Intellisense がコード開発を支援できるようにします。

1. `Solution Explorer` パネルで、`MainPage.xaml` という名前のファイルの左側にある矢印をクリックします。
2. 表示された `MainPage.xaml.cs` という名前のファイルをダブルクリックします。
3. 現在の `using` ステートメントの下部に次のコマンドを追加します。

```csharp
using Azure.Communication;
using Azure.Communication.Calling;
```

`MainPage.xaml.cs` は開いたままにしておいてください。 次の手順で、さらにコードを追加します。

## <a name="allow-app-interactions"></a>アプリの対話を許可する

以前に追加した UI ボタンは、発信された `Call` の上で操作する必要があります。 つまり、`Call` データ メンバーを `MainPage` クラスに追加する必要があります。
さらに、非同期操作の `CallAgent` 作成を成功させるには、`CallAgent` データ メンバーも同じクラスに追加する必要があります。

`MainPage` クラスに次のデータ メンバーを追加します。
```csharp
CallAgent agent_;
Call call_;
```

## <a name="create-button-handlers"></a>ボタン ハンドラーを作成する

前に、2 つの UI ボタンが XAML コードに追加されました。 次のコードでは、ユーザーがボタンを選択すると実行されるハンドラーを追加します。
次のコードは、前のセクションのデータ メンバーの後に追加する必要があります。

```csharp
private void CallHandler(object sender, RoutedEventArgs e)
{
}

private void HangupHandler(object sender, RoutedEventArgs e)
{
}
```

## <a name="object-model"></a>オブジェクト モデル

UWP 用の Azure Communication Services Calling クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| Name                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient は、通話クライアント ライブラリへのメイン エントリ ポイントです。 |
| CallAgent | CallAgent は、通話の開始および参加のために使用します。 |
| Call (英語の可能性あり) | Call は、開始されたまたは参加した通話を管理するために使用されます。 |
| CommunicationTokenCredential | CommunicationTokenCredential は、CallAgent をインスタンス化するためのトークン資格情報として使用されます。|
| CallAgentOptions | CallAgentOptions には、呼び出し元を識別するための情報が含まれています。 |
| HangupOptions | HangupOptions は、呼び出しを終了する必要があるかどうかをすべての参加者に対して通知します。 |

## <a name="initialize-the-callagent"></a>CallAgent を初期化する

`CallClient` から `CallAgent` インスタンスを作成するには、初期化されると `CallAgent` オブジェクトを非同期に返す `CallClient.CreateCallAgent` メソッドを使用する必要があります。

`CallAgent` を作成するには、`CommunicationTokenCredential` オブジェクトと `CallAgentOptions` オブジェクトを渡す必要があります。 形式に誤りがあるトークンが渡されると `CommunicationTokenCredential` がスローされることに注意してください。

次のコードを `CallHandler` 内に追加する必要があります。

```csharp
CallClient client = new CallClient();
CommunicationTokenCredential credentials;

CallAgentOptions callAgentOptions = new CallAgentOptions
{
  DisplayName = "<CALLER NAME>"
};

try
{
    credentials = new CommunicationTokenCredential("<CREDENTIAL TOKEN>");
}
catch (Exception)
{
    throw new Exception("Invalid credential token");
}
```

`<USER ACCESS TOKEN>` は、リソース用の有効な資格情報トークンに置き換える必要があります。 資格情報トークンを入手する必要がある場合は、[ユーザー アクセス トークン](../../../../quickstarts/access-tokens.md)のドキュメントを参照してください。

## <a name="create-callagent-and-place-a-call"></a>CallAgent を作成して通話を行う

`CallAgent` を作成するために必要なオブジェクトの準備ができました。 次に、`CallAgent` を非同期的に作成して通話を行います。

前の手順の例外を処理した後に、次のコードを追加する必要があります。

```csharp
client.CreateCallAgent(creds, callAgentOptions).Completed +=
async (IAsyncOperation<CallAgent> asyncInfo, AsyncStatus asyncStatus) =>
{
    agent_ = asyncInfo.GetResults();

    CommunicationUserIdentifier target = new CommunicationUserIdentifier("<CALLEE>");

    StartCallOptions startCallOptions = new StartCallOptions();
    call_ = await agent_.StartCallAsync(new List<ICommunicationIdentifier>() { target }, startCallOptions);
};
```

`<CALLEE>` は、テナントの他の ID に置き換えます。 または、`8:echo123` を使用して ACS エコー ボットと話すことができます。

## <a name="end-a-call"></a>通話を終了する

通話を開始したら、`Call` オブジェクトの `Hangup` メソッドを使用して通話を終了する必要があります。

また、すべての参加者に対して通話を終了する必要があるかどうかを通知するために、`HangupOptions` のインスタンスも使用する必要があります。

次のコードを `HangupHandler` 内に追加する必要があります。

```csharp
HangUpOptions hangupOptions = new HangUpOptions();
call_.HangUpAsync(hangupOptions).Completed += (IAsyncAction asyncInfo, AsyncStatus asyncStatus) =>
{

};
```

## <a name="run-the-code"></a>コードの実行

Visual Studio がアプリを `x64`、`x86`、または `ARM64` 向けにビルドすることを確認し、`F5` を押してアプリの実行を開始します。 その後、`Call` ボタンをクリックして、定義された呼び出し先と通話を行います。

アプリを初めて実行すると、マイクへのアクセスを許可するようにユーザーに求めるダイアログが表示されます。