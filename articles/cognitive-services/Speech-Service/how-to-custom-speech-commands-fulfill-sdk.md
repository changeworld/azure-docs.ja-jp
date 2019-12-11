---
title: Speech SDK を使用してクライアントでカスタム コマンドを実行する方法
titleSuffix: Azure Cognitive Services
description: この記事では、Speech SDK を使用して、クライアントでのカスタム コマンド アクティビティを処理します
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 39e0a512e42dd861c0ee2c833501c7594204cb8b
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806152"
---
# <a name="how-to-fulfill-commands-on-the-client-with-the-speech-sdk-preview"></a>方法:Speech SDK を使用してクライアントでコマンドを実行する (プレビュー)

カスタム コマンド アプリケーションを使用してタスクを実行するために、接続されているクライアント デバイスにカスタム ペイロードを送信できます。

この記事では、次のことを行います。

- カスタム コマンド アプリケーションからカスタム JSON ペイロードを定義して送信する
- C# UWP Speech SDK クライアント アプリケーションからカスタム JSON ペイロード コンテンツを受信して視覚化する

## <a name="prerequisites"></a>前提条件

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- 音声サービス用の Azure サブスクリプション キー
  - [無料で入手する](get-started.md)か、[Azure portal](https://portal.azure.com) で作成します
- 以前に作成したカスタム コマンド アプリ
  - [クイック スタート:パラメーターを使用してカスタム コマンドを作成する (プレビュー)](./quickstart-custom-speech-commands-create-parameters.md)
- Speech SDK が有効なクライアント アプリケーション
  - [クイック スタート:Speech SDK でカスタム コマンド アプリケーションに接続する (プレビュー)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>省略可能:すぐに開始

この記事では、カスタム コマンド アプリケーションと対話するクライアント アプリケーションを作成する方法を順を追って説明します。 この記事で使用されている、すぐにコンパイルできる完全なソース コードは、[Speech SDK のサンプル](https://aka.ms/csspeech/samples)のページに含まれています。

## <a name="fulfill-with-json-payload"></a>JSON ペイロードでの処理

1. [Speech Studio](https://speech.microsoft.com/) から前に作成したカスタム コマンド アプリケーションを開きます
1. **[Completion Rules]/(完了規則/)** セクションを調べて、ユーザーに応答する規則が事前に作成されていることを確認します
1. ペイロードをクライアントに直接送信するには、アクティビティの送信アクションで新しい規則を作成します

   > [!div class="mx-imgBorder"]
   > ![アクティビティの送信の完了規則](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Setting | 推奨値 | 説明 |
   | ------- | --------------- | ----------- |
   | 規則の名前 | UpdateDeviceState | 規則の目的を説明する名前 |
   | 条件 | 必須のパラメーター - `OnOff` および `SubjectDevice` | 規則を実行できるタイミングを決定する条件 |
   | Actions | `SendActivity` (下記参照) | 規則条件が真のときに実行するアクション |

   > [!div class="mx-imgBorder"]
   > ![アクティビティの送信のペイロード](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>デバイスのオン/オフ状態のビジュアルを作成する

[Quickstart:Speech SDK でカスタム コマンド アプリケーションに接続する (プレビュー)](./quickstart-custom-speech-commands-speech-sdk.md) では、`turn on the tv`、`turn off the fan` などのコマンドを処理する Speech SDK クライアント アプリケーションを作成しました。 次に、それらのコマンドの結果を確認できるように、いくつかのビジュアルを追加します。

`MainPage.xaml.cs` に追加される次の XML を使用して、**オン**または**オフ**を示すテキストを含むラベル付きボックスを追加します

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

JSON ペイロードを作成したので、[JSON.NET](https://www.newtonsoft.com/json) ライブラリへの参照を追加して、逆シリアル化を処理することができます。

> [!div class="mx-imgBorder"]
> ![アクティビティの送信のペイロード](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

`InitializeDialogServiceConnector` で、次の内容を `ActivityReceived` イベント ハンドラーに追加します。 追加のコードは、アクティビティからペイロードを抽出し、それに応じて TV またはファンの表示状態を変更します。

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var payload = activity?.Value;

    if(payload?.name == "SetDeviceState")
    {
        var state = payload?.state;
        var device = payload?.device;
        switch(device)
        {
            case "tv":
                State_TV.Text = state;
                break;
            case "fan":
                State_Fan.Text = state;
                break;
            default:
                NotifyUser($"Received request to set unsupported device {device} to {state}");
                break;
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

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [方法: カスタム コマンド パラメーターに検証を追加する (プレビュー)](./how-to-custom-speech-commands-validations.md)
