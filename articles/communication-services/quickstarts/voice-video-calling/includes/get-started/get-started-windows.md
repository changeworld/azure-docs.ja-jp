---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: rifox
ms.openlocfilehash: b02c91690672d214017c554043e8ed1ec9a2df62
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2021
ms.locfileid: "129440096"
---
このクイックスタートでは、Windows 用の Azure Communication Services Calling SDK を使用して、通話を開始する方法について説明します。

## <a name="sample-code"></a>サンプル コード

サンプル アプリは [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/VoiceCalling) からダウンロードできます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) のインストール。[ユニバーサル Windows プラットフォーム開発] ワークロードを選択してください。 
- デプロイ済みの Communication Services リソース。 [Communication Services のリソースを作成する](../../../create-communication-resource.md)。
- Azure Communication Service の[ユーザー アクセス トークン](../../../access-tokens.md)。

## <a name="setting-up"></a>設定

### <a name="creating-the-project"></a>プロジェクトの作成

Visual Studio の **[空白のアプリ (ユニバーサル Windows)]** テンプレートを使用して新しいプロジェクトを作成し、シングルページ ユニバーサル Windows プラットフォーム (UWP) アプリをセットアップします。

:::image type="content" source="../../media/windows/create-a-new-project.png" alt-text="Visual Studio 内で新しいプロジェクトを作成するウィンドウのスクリーンショット。":::

### <a name="install-the-package"></a>パッケージをインストールする

プロジェクトを右クリックし、[`Manage Nuget Packages`]\(NuGet パッケージの管理\) に移動して `Azure.Communication.Calling` をインストールします。 

### <a name="request-access"></a>アクセスの要求

[`Package.appxmanifest`] に移動し、[`Capabilities`]\(機能\) をクリックします。
インターネットへのインバウンド アクセスとアウトバウンド アクセスを取得するには、[`Internet (Client & Server)`]\(インターネット (クライアント & サーバー)\) チェック ボックスをオンにします。 マイクの音声フィードにアクセスするには、[`Microphone`]\(マイク\) チェック ボックスをオンにします。 

:::image type="content" source="../../media/windows/request-access.png" alt-text="Visual Studio でインターネットとマイクへのアクセスを要求する画面のスクリーンショット。":::

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

ロジックをアタッチするには、基本的なレイアウトを構成する必要があります。 発信通話を行うためには、呼び出し先のユーザー ID を指定するための `TextBox` が必要です。 また、[`Start Call`]\(通話を開始\) ボタンと [`Hang Up`]\(通話終了\) ボタンも必要となります。 プロジェクトの `MainPage.xaml` を開き、`Page` に `StackPanel` ノードを追加します。 

```C#
<Page
    x:Class="CallingQuickstart.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:CallingQuickstart"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <StackPanel>
        <TextBox Text="Who would you like to call?" TextWrapping="Wrap" x:Name="CalleeTextBox" Margin="10,10,10,10"></TextBox>
        <Button Content="Start Call" Click="CallButton_ClickAsync" x:Name="CallButton" Margin="10,10,10,10"></Button>
        <Button Content="Hang Up" Click="HangupButton_Click" x:Name="HangupButton" Margin="10,10,10,10"></Button>
    </StackPanel>
</Page>
```

`MainPage.xaml.cs` を開き、その内容を次の実装に置き換えます。 
```C#
using System;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

using Azure.Communication;
using Azure.Communication.Calling;

namespace CallingQuickstart
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.InitCallAgent();
        }
        
        private async void InitCallAgent()
        {
            // Create Call Client and initialize Call Agent
        }
        
        private async void CallButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            // Start call
        }

        private async void HangupButton_Click(object sender, RoutedEventArgs e)
        {
            // End the current call
        }

        CallClient call_client_;
        CallAgent call_agent_;
        Call call_;
    }
}
```

## <a name="object-model"></a>オブジェクト モデル

Azure Communication Services Calling SDK の主な機能のいくつかは、次のクラスとインターフェイスによって処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient は、Calling SDK へのメイン エントリ ポイントです。|
| CallAgent | CallAgent は、通話を開始および管理するために使用します。 |
| CommunicationTokenCredential | CommunicationTokenCredential は、CallAgent をインスタンス化するためのトークン資格情報として使用されます。| 
| CommunicationUserIdentifier | CommunicationUserIdentifier はユーザーの ID を表すために使用され、次のいずれかになります: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication。 |

## <a name="authenticate-the-client"></a>クライアントを認証する

ユーザー アクセス トークンを使用して `CallAgent` インスタンスを初期化します。これにより、電話をかけたり受けたりすることができるようになります。 

次のコードで、`<USER_ACCESS_TOKEN>` をユーザー アクセス トークンに置き換えます。 まだトークンを入手していない場合は、[ユーザー アクセス トークン](../../../access-tokens.md)に関するドキュメントを参照してください。

次のコードを `InitCallAgent` 関数に追加します。 

```C#
CommunicationTokenCredential token_credential = new CommunicationTokenCredential("<USER_ACCESS_TOKEN>");
call_client_ = new CallClient();

CallAgentOptions callAgentOptions = new CallAgentOptions()
{
    DisplayName = "<YOUR_DISPLAY_NAME>"
};
call_agent_ = await call_client_.CreateCallAgent(token_credential, callAgentOptions);
```

## <a name="start-a-call"></a>通話を開始する

作成した `call_agent` を使用して通話を開始するための実装を `CallButton_ClickAsync` に追加します。 

```C#
StartCallOptions startCallOptions = new StartCallOptions();
ICommunicationIdentifier[] callees = new ICommunicationIdentifier[1]
{
    new CommunicationUserIdentifier(CalleeTextBox.Text)
};
call_ = await call_agent_.StartCallAsync(callees, startCallOptions);
```

## <a name="end-a-call"></a>通話を終了する

[`Hang Up`]\(通話終了\) ボタンがクリックされたら、現在の通話を終了します。 

```C#
await call_.HangUpAsync(new HangUpOptions());
```

## <a name="run-the-code"></a>コードの実行

コードは、Visual Studio でビルドして実行できます。 ソリューションのプラットフォームに関しては、`ARM64`、`x64`、`x86` がサポートされる点に注意してください。 

発信通話を行うには、テキスト フィールドにユーザー ID を指定し、[`Start Call`]\(通話を開始\) ボタンをクリックします。 `8:echo123` を呼び出すとエコー ボットに接続されます。これは、オーディオ デバイスを起動し、デバイスが機能していることを確認する場合に役立ちます。

:::image type="content" source="../../media/windows/run-the-app.png" alt-text="クイックスタート アプリの実行を示すスクリーンショット":::
