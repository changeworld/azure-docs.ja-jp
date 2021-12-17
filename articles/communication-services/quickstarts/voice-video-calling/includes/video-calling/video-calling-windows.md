---
ms.openlocfilehash: e90438e0987f26aa57264825cd2b2bae24f1c12e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253498"
---
このクイックスタートでは、Windows 用の Azure Communication Services Calling SDK を使用して、1:1 のビデオ通話を開始する方法について説明します。

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

プロジェクトを右クリックし、[`Manage Nuget Packages`]\(NuGet パッケージの管理\) に移動して `[Azure.Communication.Calling](https://www.nuget.org/packages/Azure.Communication.Calling)` をインストールします。 [Include Preleased]\(事前リースを含める\) チェックボックスがオンになっており、パッケージソースが https://www.nuget.org/api/v2/ からのものであることを確認します。 

### <a name="request-access"></a>アクセスの要求

[`Package.appxmanifest`] に移動し、[`Capabilities`]\(機能\) をクリックします。
インターネットへのインバウンド アクセスとアウトバウンド アクセスを取得するには、[`Internet (Client & Server)`]\(インターネット (クライアント & サーバー)\) チェック ボックスをオンにします。 マイクの音声フィードにアクセスするには、[`Microphone`]\(マイク\) チェック ボックスをオンにします。 デバイスのカメラにアクセスするには、[`WebCam`]\(Web カメラ\) チェック ボックスをオンにします。 

右クリックして [コードの表示] を選択し、`Package.appxmanifest` に次のコードを追加します。 
```xml
<Extensions>
<Extension Category="windows.activatableClass.inProcessServer">
<InProcessServer>
<Path>RtmMvrUap.dll</Path>
<ActivatableClass ActivatableClassId="VideoN.VideoSchemeHandler" ThreadingModel="both" />
</InProcessServer>
</Extension>
</Extensions>
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

ロジックをアタッチするには、基本的なレイアウトを構成する必要があります。 発信通話を行うためには、呼び出し先のユーザー ID を指定するための `TextBox` が必要です。 また、[`Start Call`]\(通話を開始\) ボタンと [`Hang Up`]\(通話終了\) ボタンも必要となります。 さらに、ローカル ビデオをプレビューすると共に、もう一方の参加者のリモート ビデオをレンダリングする必要があります。 そのため、ビデオ ストリームを表示するための要素が 2 つ必要となります。

プロジェクトの `MainPage.xaml` を開き、その内容を次の実装に置き換えます。 

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
        <StackPanel>
            <TextBox Text="Who would you like to call?" TextWrapping="Wrap" x:Name="CalleeTextBox" Margin="10,10,10,10"></TextBox>
            <Button Content="Start Call" Click="CallButton_ClickAsync" x:Name="CallButton" Margin="10,10,10,10"></Button>
            <Button Content="Hang Up" Click="HangupButton_Click" x:Name="HangupButton" Margin="10,10,10,10"></Button>
        </StackPanel>
        <StackPanel Orientation="Vertical" HorizontalAlignment="Center">
            <MediaElement x:Name="RemoteVideo" AutoPlay="True" Stretch="UniformToFill"/>
            <MediaElement x:Name="LocalVideo" AutoPlay="True"  Stretch="UniformToFill" HorizontalAlignment="Right"  VerticalAlignment="Bottom"/>
        </StackPanel>
    </StackPanel>
</Page>
```

`App.xaml.cs` を開き (右クリックして [コードの表示] を選択)、次の行を先頭に追加します。
```C#
using CallingQuickstart;
```

`MainPage.xaml.cs` を開き (右クリックして [コードの表示] を選択)、内容を次の実装に置き換えます。 
```C#
using System;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

using Azure.WinRT.Communication;
using Azure.Communication.Calling;
using System.Diagnostics;
using System.Threading.Tasks;
using System.Collections.Generic;

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
            this.InitCallAgentAndDeviceManager();
        }
        
        private async void InitCallAgentAndDeviceManager()
        {
            // Initialize call agent and Device Manager
        }

        private async void CallButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            // Authenticate the client and start call
        }
        
        private async void Agent_OnIncomingCall(object sender, IncomingCall incomingcall)
        {
            // Accept an incoming call
        }

        private async void HangupButton_Click(object sender, RoutedEventArgs e)
        {
            // End the current call
        }

        CallClient callClient;
        CallAgent callAgent;
        Call call;
        DeviceManager deviceManager;
        LocalVideoStream[] localVideoStream;
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

`CallAgent` を初期化するには、ユーザー アクセス トークンが必要になります。 通常このトークンは、アプリケーション固有の認証を使用してサービスから生成されます。 ユーザー アクセス トークンの詳細については、[ユーザー アクセス トークン](../../../access-tokens.md)のガイドを参照してください。 

クイック スタートでは、`<USER_ACCESS_TOKEN>` を Azure Communication Service リソース用に生成されたユーザー アクセス トークンに置き換えます。

トークンが与えられたら、それで `CallAgent` インスタンスを初期化します。これにより、通話を開始したり、受信したりできるようになります。 また、デバイス上のカメラにアクセスするためには、デバイス マネージャーのインスタンスを取得する必要があります。 

```C#
private async void InitCallAgentAndDeviceManager()
{
    CallClient callClient = new CallClient();
    deviceManager = await callClient.GetDeviceManager();

    CommunicationTokenCredential token_credential = new CommunicationTokenCredential("<USER_ACCESS_TOKEN>");
    
    CallAgentOptions callAgentOptions = new CallAgentOptions()
    {
        DisplayName = "<DISPLAY_NAME>"
    };
    callAgent = await callClient.CreateCallAgent(token_credential, callAgentOptions);
    callAgent.OnCallsUpdated += Agent_OnCallsUpdated;
    callAgent.OnIncomingCall += Agent_OnIncomingCall;
}
```

## <a name="start-a-call-with-video"></a>ビデオ通話を開始する

ビデオ通話を開始するには、 デバイス マネージャーのインスタンスを使用してカメラを列挙し、`LocalVideoStream` を構築する必要があります。 `LocalVideoStream` を使用して `VideoOptions` を設定し、`startCallOptions` を使用してそれを渡すことで、通話の初期オプションを設定する必要があります。 `LocalVideoStream` を `MediaElement` にアタッチすることで、ローカル ビデオのプレビューを表示できます。 

```C#
private async void CallButton_ClickAsync(object sender, RoutedEventArgs e)
{
    Debug.Assert(deviceManager.Microphones.Count > 0);
    Debug.Assert(deviceManager.Speakers.Count > 0);
    Debug.Assert(deviceManager.Cameras.Count > 0);

    if (deviceManager.Cameras.Count > 0)
    {
        VideoDeviceInfo videoDeviceInfo = deviceManager.Cameras[0];
        localVideoStream = new LocalVideoStream[1];
        localVideoStream[0] = new LocalVideoStream(videoDeviceInfo);

        Uri localUri = await localVideoStream[0].CreateBindingAsync();

        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            LocalVideo.Source = localUri;
            LocalVideo.Play();
        });

    }

    StartCallOptions startCallOptions = new StartCallOptions();
    startCallOptions.VideoOptions = new VideoOptions(localVideoStream);
    ICommunicationIdentifier[] callees = new ICommunicationIdentifier[1]
    {
        new CommunicationUserIdentifier(CalleeTextBox.Text)
    };

    call = await callAgent.StartCallAsync(callees, startCallOptions);
}
```

## <a name="accept-an-incoming-call"></a>電話の着信を受け入れる

ビデオで着信に応答するには、`LocalVideoStream` を `acceptCallOptions` に渡します。 

```C#
private async void Agent_OnIncomingCall(object sender, IncomingCall incomingcall)
{
    Debug.Assert(deviceManager.Microphones.Count > 0);
    Debug.Assert(deviceManager.Speakers.Count > 0);
    Debug.Assert(deviceManager.Cameras.Count > 0);

    if (deviceManager.Cameras.Count > 0)
    {
        VideoDeviceInfo videoDeviceInfo = deviceManager.Cameras[0];
        localVideoStream = new LocalVideoStream[1];
        localVideoStream[0] = new LocalVideoStream(videoDeviceInfo);

        Uri localUri = await localVideoStream[0].CreateBindingAsync();

        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            LocalVideo.Source = localUri;
            LocalVideo.Play();
        });

    }
    AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
    acceptCallOptions.VideoOptions = new VideoOptions(localVideoStream);

    call = await incomingcall.AcceptAsync(acceptCallOptions);
}
```

## <a name="remote-participant-and-remote-video-streams"></a>リモート参加者とリモート ビデオ ストリーム

リモート参加者は全員、通話インスタンスの `RemoteParticipants` コレクションを通じて利用できます。 通話の接続後、通話のリモート参加者にアクセスし、リモート ビデオ ストリームを処理できます。 

```C#
private async void Agent_OnCallsUpdated(object sender, CallsUpdatedEventArgs args)
{
    foreach (var call in args.AddedCalls)
    {
        foreach (var remoteParticipant in call.RemoteParticipants)
        {
            await AddVideoStreams(remoteParticipant.VideoStreams);
            remoteParticipant.OnVideoStreamsUpdated += async (s, a) => await AddVideoStreams(a.AddedRemoteVideoStreams);
        }
        call.OnRemoteParticipantsUpdated += Call_OnRemoteParticipantsUpdated;
        call.OnStateChanged += Call_OnStateChanged;
    }
}

private async void Call_OnRemoteParticipantsUpdated(object sender, ParticipantsUpdatedEventArgs args)
{
    foreach (var remoteParticipant in args.AddedParticipants)
    {
        await AddVideoStreams(remoteParticipant.VideoStreams);
        remoteParticipant.OnVideoStreamsUpdated += async (s, a) => await AddVideoStreams(a.AddedRemoteVideoStreams);
    }
}
```

### <a name="render-remote-videos"></a>リモート ビデオをレンダリングする

それぞれのリモート ビデオ ストリームを `MediaElement` にアタッチします。 

```C#
private async Task AddVideoStreams(IReadOnlyList<RemoteVideoStream> streams)
{

    foreach (var remoteVideoStream in streams)
    {
        var remoteUri = await remoteVideoStream.CreateBindingAsync();

        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            RemoteVideo.Source = remoteUri;
            RemoteVideo.Play();
        });
        remoteVideoStream.Start();
    }
}
```

## <a name="call-state-update"></a>通話状態の更新
通話が切断されたら、ビデオ レンダラーをクリーンする必要があります。 

```C#
private async void Call_OnStateChanged(object sender, PropertyChangedEventArgs args)
{
    switch (((Call)sender).State)
    {
        case CallState.Disconnected:
            await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                LocalVideo.Source = null;
                RemoteVideo = null;
            });
            break;
        default:
            Debug.WriteLine(((Call)sender).State);
            break;
    }
}
```

## <a name="end-a-call"></a>通話を終了する

[`Hang Up`]\(通話終了\) ボタンがクリックされたら、現在の通話を終了します。 

```C#
private async void HangupButton_Click(object sender, RoutedEventArgs e)
{
    var hangUpOptions = new HangUpOptions();
    await call.HangUpAsync(hangUpOptions);
}
```

## <a name="run-the-code"></a>コードの実行

コードは、Visual Studio でビルドして実行できます。 ソリューションのプラットフォームに関しては、`ARM64`、`x64`、`x86` がサポートされる点に注意してください。 

発信ビデオ通話を行うには、テキスト フィールドにユーザー ID を指定し、[`Start Call`]\(通話を開始\) ボタンをクリックします。 

注: エコー ボットではビデオ ストリーミングがサポートされていないので、`8:echo123` を呼び出すとビデオ ストリームが停止します。 

ユーザー ID の詳細については、[ユーザー アクセス トークン](../../../access-tokens.md)のガイドを参照してください。 
