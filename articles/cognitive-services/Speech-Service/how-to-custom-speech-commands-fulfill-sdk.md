---
title: Speech SDK を使用してクライアント上でコマンドを実行する
titleSuffix: Azure Cognitive Services
description: この記事では、Speech SDK を使用して、クライアントでカスタム コマンド アクティビティを処理する方法について説明します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: trbye
ms.openlocfilehash: f11f5f3c2ad4c9f0241d34edeb664f739f88d15c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871745"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Speech SDK (プレビュー) を使用してクライアントからのコマンドを実行する

カスタム コマンド アプリケーションを使用してタスクを実行するために、接続されているクライアント デバイスにカスタム ペイロードを送信できます。

この記事では、次のことを行います。

- カスタム コマンド アプリケーションからカスタム JSON ペイロードを定義して送信する
- C# UWP Speech SDK クライアント アプリケーションからカスタム JSON ペイロード コンテンツを受信して視覚化する

## <a name="prerequisites"></a>前提条件
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * 音声サービス用の Azure サブスクリプション キー: [無料で入手する](get-started.md)か、[Azure portal](https://portal.azure.com) で作成します
> * 以前に作成したカスタム コマンド アプリ: [クイック スタート: パラメーターを使用してカスタム コマンドを作成する (プレビュー)](./quickstart-custom-speech-commands-create-parameters.md)
> * Speech SDK が有効なクライアント アプリケーション: [クイック スタート: Speech SDK でカスタム コマンド アプリケーションに接続する (プレビュー)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>省略可能:すぐに開始

この記事では、カスタム コマンド アプリケーションと対話するクライアント アプリケーションを作成する方法を順を追って説明します。 この記事で使用されている、すぐにコンパイルできる完全なソース コードは、[Speech SDK のサンプル](https://aka.ms/csspeech/samples)のページに含まれています。

## <a name="fulfill-with-json-payload"></a>JSON ペイロードでの処理

1. 前に作成したカスタム コマンド アプリケーションを開きます ([パラメーターを使用してカスタム コマンドを作成するクイックスタート](./quickstart-custom-speech-commands-create-parameters.md)で作成したもの)
1. **[Completion Rules]/(完了規則/)** セクションを調べて、ユーザーに応答する規則が事前に作成されていることを確認します
1. ペイロードをクライアントに直接送信するには、アクティビティの送信アクションで新しい規則を作成します

   > [!div class="mx-imgBorder"]
   > ![アクティビティの送信の完了規則](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | 設定 | 推奨値 | 説明 |
   | ------- | --------------- | ----------- |
   | 規則の名前 | UpdateDeviceState | ルールの目的を説明する名前 |
   | 条件 | 必須のパラメーター - `OnOff` および `SubjectDevice` | ルールを実行できるタイミングを決定する条件 |
   | Actions | `SendActivity` (下記参照) | ルール条件が真のときに実行するアクション |

1. 以下の JSON を**アクティビティ コンテンツ**にコピーしてします
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
   > [!div class="mx-imgBorder"]
   > ![アクティビティの送信のペイロード](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

## <a name="create-visuals-for-device-on-or-off-state"></a>デバイスのオン/オフ状態のビジュアルを作成する

[Quickstart:Speech SDK でカスタム コマンド アプリケーションに接続する](./quickstart-custom-speech-commands-speech-sdk.md)」では、`turn on the tv`、`turn off the fan` などのコマンドを処理する Speech SDK クライアント アプリケーションを作成しました。 いくつかのビジュアルを追加して、それらのコマンドの結果を確認できます。

`MainPage.xaml` に追加される次の XML を使用して、**オン**または**オフ**を示すテキストを含むラベル付きボックスを追加します

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>カスタマイズ可能なペイロードを処理する
### <a name="add-reference-libraries"></a>参照ライブラリを追加する

JSON ペイロードを作成したので、[JSON.NET](https://www.newtonsoft.com/json) ライブラリへの参照を追加して、逆シリアル化を処理する必要があります。
- ソリューションを右クリックします。
- **[ソリューションの NuGet パッケージの管理]** を選択し、 **[インストール]** を選択します 
- 更新リストで **Newtonsoft.json** を探し、**Microsoft.NETCore.UniversalWindowsPlatform** を最新バージョンに更新します

> [!div class="mx-imgBorder"]
> ![アクティビティの送信のペイロード](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

"MainPage.xaml.cs" に以下を追加します
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

### <a name="handle-received-payload"></a>受信したペイロードを処理する

`InitializeDialogServiceConnector` で、`ActivityReceived` イベント ハンドラーを次のコードに置き換えます。 変更した `ActivityReceived` イベント ハンドラーでは、アクティビティからペイロードが抽出され、それに応じて TV またはファンの表示状態が変更されます。

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.device != null ? activity.state.ToString() : string.Empty;
        var device = activity?.device != null ? activity.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>試してみる

1. アプリケーションの起動
1. [Enable Microphone]\(マイクを有効にする\) を選択します
1. [Talk]/(トーク/) ボタンを選択します
1. `turn on the tv` と言います
1. TV の表示状態が "オン" に変わるはずです
   > [!div class="mx-imgBorder"]
   > ![アクティビティの送信のペイロード](media/custom-speech-commands/fulfill-sdk-turn-on-tv.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [方法: カスタム コマンド パラメーターに検証を追加する (プレビュー)](./how-to-custom-speech-commands-validations.md)
