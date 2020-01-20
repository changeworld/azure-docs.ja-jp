---
title: クイック スタート:Speech SDK を使用したカスタム コマンド アプリへの接続 - Speech Service
titleSuffix: Azure Cognitive Services
description: この記事では、カスタム コマンドを使用して Speech SDK クライアント アプリケーションを作成します。
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 69a03ce5a8da7c8af6c17d122be3744e7b79e246
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75381105"
---
# <a name="quickstart-connect-to-a-custom-commands-application-with-the-speech-sdk-preview"></a>クイック スタート:Speech SDK でカスタム コマンド アプリケーションに接続する (プレビュー)

ホストされたカスタム コマンド アプリケーションを作成した後、クライアント デバイスからそのアプリケーションとの対話を開始できます。

この記事では、次のことを行います。

- カスタム コマンド アプリケーションを発行してアプリケーション識別子 (アプリ ID) を取得する
- Speech SDK を使用してクライアント アプリを作成し、カスタム コマンド アプリケーションと対話できるようにする

## <a name="prerequisites"></a>前提条件

この記事を完了するには、カスタム コマンド アプリケーションが必要です。 カスタム コマンド アプリケーションをまだ作成していない場合は、次に示す以前のクイックスタートで作成できます。

- [クイック スタート:カスタム コマンドを作成する (プレビュー)](./quickstart-custom-speech-commands-create-new.md)
- [クイック スタート:パラメーターを使用してカスタム コマンドを作成する (プレビュー)](./quickstart-custom-speech-commands-create-parameters.md)

次のものも必要です。

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Speech Services 用の Azure サブスクリプション キー。 [無料で入手する](get-started.md)か、[Azure portal](https://portal.azure.com) で作成します

## <a name="optional-get-started-fast"></a>省略可能:すぐに開始

このクイックスタートでは、カスタム コマンド アプリに接続するクライアント アプリケーションを作成する方法を順を追って説明します。 このクイック スタートで使用されている、すぐにコンパイルできる完全なソース コードは、[Speech SDK のサンプル](https://aka.ms/csspeech/samples)のページの `quickstart` フォルダーに含まれています。

## <a name="step-1-publish-custom-commands-application"></a>手順 1:カスタム コマンド アプリケーションを発行する

1. [以前に作成したカスタム コマンド アプリケーション](./quickstart-custom-speech-commands-create-new.md)を開き、 **[Publish]\(発行\)** を選択します

   > [!div class="mx-imgBorder"] 
   > ![アプリケーションの発行](media/custom-speech-commands/fulfill-sdk-publish-application.png)

1. 後で使用するために、発行通知からアプリ ID をコピーします

## <a name="step-2-create-a-visual-studio-project"></a>手順 2:Visual Studio プロジェクトを作成する

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="step-3-add-sample-code"></a>手順 3:サンプル コードを追加する

この手順では、アプリケーションのユーザー インターフェイスを定義する XAML コードを追加し、C# のコードビハインド実装を追加します。

### <a name="xaml-code"></a>XAML コード

次の XAML コードを追加してアプリケーションのユーザー インターフェイスを作成します。

1. **ソリューション エクスプローラー**で、`MainPage.xaml` を開きます

1. デザイナーの XAML ビューで、内容全体を次のコード スニペットに置き換えます。

   ```xml
   <Page
       x:Class="helloworld.MainPage"
       xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       xmlns:local="using:helloworld"
       xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
       xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
       mc:Ignorable="d"
       Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

       <Grid>
           <StackPanel Orientation="Vertical" HorizontalAlignment="Center"
                       Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
               <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"
                       Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked"
                       Height="35"/>
               <Button x:Name="ListenButton" Content="Talk"
                       Margin="0,10,10,0" Click="ListenButton_ButtonClicked"
                       Height="35"/>
               <StackPanel x:Name="StatusPanel" Orientation="Vertical"
                           RelativePanel.AlignBottomWithPanel="True"
                           RelativePanel.AlignRightWithPanel="True"
                           RelativePanel.AlignLeftWithPanel="True">
                   <TextBlock x:Name="StatusLabel" Margin="0,10,10,0"
                              TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                   <Border x:Name="StatusBorder" Margin="0,0,0,0">
                       <ScrollViewer VerticalScrollMode="Auto"
                                     VerticalScrollBarVisibility="Auto" MaxHeight="200">
                           <!-- Use LiveSetting to enable screen readers to announce
                                the status update. -->
                           <TextBlock
                               x:Name="StatusBlock" FontWeight="Bold"
                               AutomationProperties.LiveSetting="Assertive"
                               MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}"
                               Margin="10,10,10,20" TextWrapping="Wrap"  />
                       </ScrollViewer>
                   </Border>
               </StackPanel>
           </StackPanel>
           <MediaElement x:Name="mediaElement"/>
       </Grid>
   </Page>
   ```

デザイン ビューが更新されてアプリケーションのユーザー インターフェイスが表示されます。

### <a name="c-code-behind-source"></a>C# のコードビハインド ソース

アプリケーションが想定どおりに動作するよう、コードビハインド ソースを追加します。 コードビハインド ソースには、次の内容が含まれています。

- `Speech` 名前空間と `Speech.Dialog` 名前空間の、必要な `using` ステートメント
- ボタン ハンドラーに接続された、マイクへのアクセスを確実にするための簡単な実装
- アプリケーション内のメッセージとエラーを表示する基本的な UI ヘルパー
- 後で設定される初期化コード パスのランディング ポイント
- テキスト読み上げを再生するヘルパー (ストリーミングのサポートなし)
- 後で設定されるリスニングを開始するための空のボタン ハンドラー

次のようにしてコードビハインドのソースを追加します。

1. **ソリューション エクスプローラー**で、コードビハインドのソース ファイル `MainPage.xaml.cs` を開きます (`MainPage.xaml` にグループ化されています)

1. ファイルの内容を次のコードに置き換えます。

   ```csharp
   using Microsoft.CognitiveServices.Speech;
   using Microsoft.CognitiveServices.Speech.Audio;
   using Microsoft.CognitiveServices.Speech.Dialog;
   using System;
   using System.Diagnostics;
   using System.IO;
   using System.Text;
   using Windows.Foundation;
   using Windows.Storage.Streams;
   using Windows.UI.Xaml;
   using Windows.UI.Xaml.Controls;
   using Windows.UI.Xaml.Media;

   namespace helloworld
   {
       public sealed partial class MainPage : Page
       {
           private DialogServiceConnector connector;

           private enum NotifyType
           {
               StatusMessage,
               ErrorMessage
           };

           public MainPage()
           {
               this.InitializeComponent();
           }

           private async void EnableMicrophone_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               bool isMicAvailable = true;
               try
               {
                   var mediaCapture = new Windows.Media.Capture.MediaCapture();
                   var settings =
                       new Windows.Media.Capture.MediaCaptureInitializationSettings();
                   settings.StreamingCaptureMode =
                       Windows.Media.Capture.StreamingCaptureMode.Audio;
                   await mediaCapture.InitializeAsync(settings);
               }
               catch (Exception)
               {
                   isMicAvailable = false;
               }
               if (!isMicAvailable)
               {
                   await Windows.System.Launcher.LaunchUriAsync(
                       new Uri("ms-settings:privacy-microphone"));
               }
               else
               {
                   NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
               }
           }

           private void NotifyUser(
               string strMessage, NotifyType type = NotifyType.StatusMessage)
           {
               // If called from the UI thread, then update immediately.
               // Otherwise, schedule a task on the UI thread to perform the update.
               if (Dispatcher.HasThreadAccess)
               {
                   UpdateStatus(strMessage, type);
               }
               else
               {
                   var task = Dispatcher.RunAsync(
                       Windows.UI.Core.CoreDispatcherPriority.Normal,
                       () => UpdateStatus(strMessage, type));
               }
           }

           private void UpdateStatus(string strMessage, NotifyType type)
           {
               switch (type)
               {
                   case NotifyType.StatusMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Green);
                       break;
                   case NotifyType.ErrorMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Red);
                       break;
               }
               StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text)
                   ? strMessage : "\n" + strMessage;

               if (!string.IsNullOrEmpty(StatusBlock.Text))
               {
                   StatusBorder.Visibility = Visibility.Visible;
                   StatusPanel.Visibility = Visibility.Visible;
               }
               else
               {
                   StatusBorder.Visibility = Visibility.Collapsed;
                   StatusPanel.Visibility = Visibility.Collapsed;
               }
               // Raise an event if necessary to enable a screen reader
               // to announce the status update.
               var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
               if (peer != null)
               {
                   peer.RaiseAutomationEvent(
                       Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
               }
           }

           // Waits for and accumulates all audio associated with a given
           // PullAudioOutputStream and then plays it to the MediaElement. Long spoken
           // audio will create extra latency and a streaming playback solution
           // (that plays audio while it continues to be received) should be used --
           // see the samples for examples of this.
           private void SynchronouslyPlayActivityAudio(
               PullAudioOutputStream activityAudio)
           {
               var playbackStreamWithHeader = new MemoryStream();
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
               playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
               playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

               byte[] pullBuffer = new byte[2056];

               uint lastRead = 0;
               do
               {
                   lastRead = activityAudio.Read(pullBuffer);
                   playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
               }
               while (lastRead == pullBuffer.Length);

               var task = Dispatcher.RunAsync(
                   Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                   mediaElement.SetSource(
                       playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                   mediaElement.Play();
               });
           }

           private void InitializeDialogServiceConnector()
           {
               // New code will go here
           }

           private async void ListenButton_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               // New code will go here
           }
       }
   }
   ```

1. `InitializeDialogServiceConnector` のメソッド本体に次のコードを追加します

   ```csharp
   // This code creates the `DialogServiceConnector` with your subscription information.
   // create a DialogServiceConfig by providing a Custom Commands application id and Cognitive Services subscription key
   // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
   const string speechCommandsApplicationId = "YourApplicationId"; // Your application id
   const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
   const string region = "YourServiceRegion"; // The subscription service region. Note: only 'westus2' is currently supported

   var speechCommandsConfig = CustomCommandsConfig.FromSubscription(speechCommandsApplicationId, speechSubscriptionKey, region);
   speechCommandsConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-us");
   connector = new DialogServiceConnector(speechCommandsConfig);
   ```

1. `YourApplicationId`、`YourSpeechSubscriptionKey`、`YourServiceRegion` の各文字列を、実際のアプリ、音声サブスクリプション、[リージョン](regions.md)の値に置き換えます

1. `InitializeDialogServiceConnector` のメソッド本体の最後に、次のコード スニペットを追加します

   ```csharp
   //
   // This code sets up handlers for events relied on by `DialogServiceConnector` to communicate its activities,
   // speech recognition results, and other information.
   //
   // ActivityReceived is the main way your client will receive messages, audio, and events
   connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
   {
       NotifyUser(
           $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

       if (activityReceivedEventArgs.HasAudio)
       {
           SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
       }
   };

   // Canceled will be signaled when a turn is aborted or experiences an error condition
   connector.Canceled += (sender, canceledEventArgs) =>
   {
       NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
       if (canceledEventArgs.Reason == CancellationReason.Error)
       {
           NotifyUser(
               $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
       }
   };

   // Recognizing (not 'Recognized') will provide the intermediate recognized text
   // while an audio stream is being processed
   connector.Recognizing += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
   };

   // Recognized (not 'Recognizing') will provide the final recognized text
   // once audio capture is completed
   connector.Recognized += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
   };

   // SessionStarted will notify when audio begins flowing to the service for a turn
   connector.SessionStarted += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
   };

   // SessionStopped will notify when a turn is complete and
   // it's safe to begin listening again
   connector.SessionStopped += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
   };
   ```

1. `MainPage` クラスの `ListenButton_ButtonClicked` メソッドの本体に次のコード スニペットを追加します

   ```csharp
   // This code sets up `DialogServiceConnector` to listen, since you already established the configuration and
   // registered the event handlers.
   if (connector == null)
   {
       InitializeDialogServiceConnector();
       // Optional step to speed up first interaction: if not called,
       // connection happens automatically on first use
       var connectTask = connector.ConnectAsync();
   }

   try
   {
       // Start sending audio
       await connector.ListenOnceAsync();
   }
   catch (Exception ex)
   {
       NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
   }
   ```

1. メニュー バーから **[ファイル]**  >  **[すべて保存]** を選択して変更内容を保存します

## <a name="build-and-run-the-application"></a>アプリケーションの構築と実行

1. メニュー バーから **[ビルド]**  >  **[ソリューションのビルド]** を選択してアプリケーションをビルドします。 コードはエラーなくコンパイルされるはずです。

1. **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、**F5** キーを押して、アプリケーションを起動します。 **[helloworld]** ウィンドウが表示されます。

   ![サンプル UWP 仮想アシスタント アプリケーション (C#) - クイックスタート](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. **[Enable Microphone]\(マイクを有効にする\)** を選択します。 アクセス許可要求が表示された場合、 **[はい]** を選択します。

   ![マイクへのアクセス許可要求](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. **[Talk]\(会話\)** を選択し、デバイスのマイクに向かって英語のフレーズや文章を話します。 音声が Direct Line Speech チャネルに送信されてテキストに変換され、ウィンドウに表示されます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [方法: Speech SDK を使用してクライアントでコマンドを実行する (プレビュー)](./how-to-custom-speech-commands-fulfill-sdk.md)
> [ 方法: カスタム コマンド パラメーターに検証を追加する (プレビュー)](./how-to-custom-speech-commands-validations.md)
