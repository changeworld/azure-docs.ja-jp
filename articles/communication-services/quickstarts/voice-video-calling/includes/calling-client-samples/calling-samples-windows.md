---
title: クイック スタート - Azure Communication Services を使用して Windows アプリに通話を追加する
description: このクイックスタートでは、Windows 用の Azure Communication Services Calling SDK を使用する方法について説明します。
author: tophpalmer
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 16e7562fb37ebcf0044f9ba07e4d9f53acf2d35e
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2021
ms.locfileid: "111560517"
---
このクイックスタートでは、Windows 用の Azure Communication Services Calling SDK を使用して、通話を開始する方法について説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../../create-communication-resource.md)。
- 通話クライアントを有効にするための `User Access Token`。 [`User Access Token` を取得する方法](../../../access-tokens.md)についての詳細
- Windows SDK `10.0.17134` 以上がインストールされた任意のエディションの [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) と `Universal Windows Platform development` ワークロード。 さらに、`NuGet package manager` および `NuGet targets and build tasks` コンポーネントもインストールする必要があります。
- 省略可能: [アプリケーションへの通話の追加の概要](../../getting-started-with-calling.md)に関するクイックスタートを完了します。

## <a name="setting-up"></a>設定

### <a name="creating-the-visual-studio-project"></a>Visual Studio プロジェクトの作成

Visual Studio 2019 で、新しい `Blank App (Universal Windows)` プロジェクトを作成します。 プロジェクト名を入力した後、`10.0.17134` より大きい任意の Windows SDK を選択します。 

### <a name="install-the-package-and-dependencies-with-nuget-package-manager"></a>NuGet パッケージ マネージャーを使用してパッケージと依存関係をインストールする

Calling SDK の API とライブラリは、NuGet パッケージにより一般公開されています。
次の手順では、Calling SDK NuGet パッケージを検索、ダウンロード、インストールする方法を示します。

1. NuGet パッケージ マネージャーを開きます (`Tools` -> `NuGet Package Manager` -> `Manage NuGet Packages for Solution`)
2. [`Browse`] をクリックし、検索ボックスに「`Azure.Communication.Calling`」と入力します。
3. `Include prerelease` チェック ボックスがオンになっていることを確認します。
4. `Azure.Communication.Calling` パッケージをクリックします。
5. 右側のタブで、CS プロジェクトに対応するチェックボックスをオンにします。
6. [`Install`] ボタンをクリックします。


### <a name="request-access-to-the-microphone"></a>マイクへのアクセスを要求する

アプリを正常に実行するには、マイクへのアクセスが必要です。 UWP アプリでは、マイク機能をアプリ マニフェスト ファイルで宣言する必要があります。 それを行うための手順を次に示します。

1. `Solution Explorer` パネルで、`.appxmanifest` 拡張子が付いたファイルをダブルクリックします。
2. [`Capabilities`] タブをクリックします。
3. 機能の一覧から [`Microphone`] チェック ボックスをオンにします。


### <a name="create-ui-buttons-to-place-and-hang-up-the-call"></a>通話の発信と終了を行うための UI ボタンを作成する

この単純なサンプル アプリには、2 つのボタンが含まれます。 1 つは呼び出しを行い、もう 1 つは通話を終了します。
次の手順では、これらのボタンをアプリに追加する方法を示します。

1. `Solution Explorer` パネルで、`MainPage.xaml` という名前のファイルをダブルクリックします。
2. 中央のパネルで、UI プレビューの下の XMAL コードを探します。
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

## <a name="allow-app-interactions"></a>アプリの対話を許可する

以前に追加した UI ボタンは、発信された `Call` の上で操作する必要があります。 つまり、`Call` データ メンバーを `MainPage` クラスに追加する必要があります。
さらに、非同期操作の `CallAgent` 作成を成功させるには、`CallAgent` データ メンバーも同じクラスに追加する必要があります。

`MainPage` クラスに次のデータ メンバーを追加します。
```csharp
CallAgent agent_;
Call call_;
```

## <a name="create-button-handlers"></a>ボタン ハンドラーを作成する

前に、2 つの UI ボタンが XAML コードに追加されました。 次のコードでは、ユーザーがボタンをクリックすると実行されるハンドラーを追加します。
次のコードは、前のセクションのデータ メンバーの後に追加する必要があります。

```csharp
private void CallHandler(object sender, RoutedEventArgs e)
{
}

private void HangupHandler(object sender, RoutedEventArgs e)
{
}
```

## <a name="initialize-the-callagent"></a>CallAgent を初期化する

`CallClient` から `CallAgent` インスタンスを作成するには、初期化されると `CallAgent` オブジェクトを非同期に返す `CallClient.CreateCallAgent` メソッドを使用する必要があります。

`CallAgent` を作成するには、`CommunicationTokenCredential` オブジェクトと `CallAgentOptions` オブジェクトを渡す必要があります。 形式に誤りがあるトークンが渡されると `CommunicationTokenCredential` がスローされることに注意してください。

次のコードを `CallHandler` 内に追加する必要があります。

```csharp
CallClient client = new CallClient();
CommunicationTokenCredential creds;

CallAgentOptions callAgentOptions = new CallAgentOptions
{
  DisplayName = "<CALLER NAME>"
};

try
{
    creds = new CommunicationTokenCredential("<CREDENTIAL TOKEN>");
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
(IAsyncOperation<CallAgent> asyncInfo, AsyncStatus asyncStatus) =>
{
    agent_ = asyncInfo.GetResults();

    string[] calling = { "<CALLEE>" };

    StartCallOptions startCallOptions = new StartCallOptions(); ;
    call_ = agent_.Call(calling, startCallOptions);
};
```

`<CALLEE>` は、テナントの他の ID に置き換えます。 または、`8:echo123` を使用して ACS エコー ボットと話すことができます。

## <a name="end-a-call"></a>通話を終了する

通話を開始したら、`Call` オブジェクトの `Hangup` メソッドを使用して通話を終了する必要があります。

また、すべての参加者に対して通話を終了する必要があるかどうかを通知するために、`HangupOptions` のインスタンスも使用する必要があります。

次のコードを `HangupHandler` 内に追加する必要があります。

```csharp
HangupOptions hangupOptions = new HangupOptions();
call_.HangupAsync(hangupOptions).Completed +=
(IAsyncAction asyncInfo, AsyncStatus asyncStatus) =>
{
};
```

## <a name="run-the-code"></a>コードの実行

Visual Studio がアプリを `x64`、`x86`、または `ARM64` 向けにビルドすることを確認し、`F5` を押してアプリの実行を開始します。 その後、`Call` ボタンをクリックして、定義された呼び出し先と通話を行います。

アプリを初めて実行すると、マイクへのアクセスを許可するようにユーザーに求めるダイアログが表示されます。
