---
title: Web エンドポイントを設定する
titleSuffix: Azure Cognitive Services
description: Custom Commands の Web エンド ポイントを設定する
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 5f1d5318140dd14c5024e8dd3ad0def0afc7f378
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725910"
---
# <a name="set-up-web-endpoints"></a>Web エンドポイントを設定する

この記事では、クライアント アプリケーションから HTTP 要求を行うために、カスタム コマンド アプリケーションで Web エンドポイントを設定する方法について説明します。 次のタスクを実行します。

- カスタム コマンド アプリケーションで Web エンドポイントを設定する
- カスタム コマンド アプリケーションで Web エンドポイントを呼び出す
- Web エンドポイントの応答を受信する
- Web エンドポイントの応答をカスタム JSON ペイロードに統合し、C# UWP Speech SDK クライアント アプリケーションからそれを送信して視覚化する

## <a name="prerequisites"></a>前提条件

> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * 音声サービス用の Azure サブスクリプション キー: [無料で入手する](overview.md#try-the-speech-service-for-free)か、[Azure portal](https://portal.azure.com) で作成します
> * Custom Commands アプリ (「[Custom Commands を使用して音声アシスタントを作成する](quickstart-custom-commands-application.md)」を参照してください)
> * Speech SDK が有効なクライアント アプリ (「[Speech SDK を使用してクライアント アプリケーションと統合する](how-to-custom-commands-setup-speech-sdk.md)」を参照してください)

## <a name="deploy-an-external-web-endpoint-using-azure-function-app"></a>Azure Function app を使用して外部 Web エンドポイントをデプロイする

このチュートリアルでは、Custom Commands アプリケーションの **TurnOnOff** コマンドで設定したすべてのデバイスの状態を保持する HTTP エンドポイントが必要です。

呼び出す Web エンドポイントが既に存在する場合は、[次のセクション](#setup-web-endpoints-in-custom-commands)までスキップしてください。 次のセクションでは、このセクションをスキップする場合に使用できる、既定のホストされる Web エンドポイントについて詳しく説明します。

### <a name="input-format-of-azure-function"></a>Azure 関数の入力形式

次に、 [Azure Functions](../../azure-functions/index.yml) を使用してエンドポイントをデプロイします。
次に示すのは、Azure 関数に渡される Custom Commands イベントの形式です。 この情報は、Azure 関数アプリを作成するときに使用します。

```json
{
  "conversationId": "string",
  "currentCommand": {
    "name": "string",
    "parameters": {
      "SomeParameterName": "string",
      "SomeOtherParameterName": "string"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "string",
      "SomeOtherGlobalParameterName": "string"
  }
}
```

    
次の表に、この入力の主な属性を示します。
        
| 属性 | 説明 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | 会話の一意の識別子。 この ID はクライアント アプリによって生成できることに注意してください。 |
| **currentCommand** | 会話で現在アクティブになっているコマンド。 |
| **name** | コマンドの名前。 `parameters` 属性は、パラメーターの現在の値を含むマップです。 |
| **currentGlobalParameters** | `parameters` のようなマップですが、グローバル パラメーターに使用されます。 |


**DeviceState** Azure 関数の場合、Custom Commands イベントの例は次のようになります。 これは関数アプリへの **入力** として機能します。
    
```json
{
  "conversationId": "someConversationId",
  "currentCommand": {
    "name": "TurnOnOff",
    "parameters": {
      "item": "tv",
      "value": "on"
    }
  }
}
```

### <a name="azure-function-output-for-a-custom-command-app"></a>カスタム コマンド アプリのための Azure 関数の出力

Azure 関数からの出力が Custom Commands アプリによって使用される場合は、次の形式で表示されます。 詳細については、「[Web エンドポイントからコマンドを更新する](./how-to-custom-commands-update-command-from-web-endpoint.md)」を参照してください。

```json
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

### <a name="azure-function-output-for-a-client-application"></a>クライアント アプリケーションのための Azure 関数出力

Azure 関数からの出力がクライアント アプリケーションによって使用される場合、この出力はクライアント アプリケーションで必要とされるあらゆる形式を取ることができます。

**DeviceState** エンドポイントの場合、Azure 関数の出力は、Custom Commands アプリケーションではなくクライアント アプリケーションによって使用されます。 Azure 関数の出力例は次のようになります。
    
```json
{
  "TV": "on",
  "Fan": "off"
}
``` 

デバイスの状態を維持できるように、この出力は外部ストレージに書き込みます。 外部ストレージの状態は、以下の「[クライアント アプリケーションと統合する](#integrate-with-client-application)」セクションで使用します。


### <a name="deploy-azure-function"></a>Azure 関数をデプロイする

Azure Functions アプリとして構成およびデプロイできるサンプルが用意されています。 サンプル用のストレージ アカウントを作成する場合は、次の手順に従います。
 
1. デバイスの状態を保存するためのテーブル ストレージを作成します。 Azure portal で、**ストレージ アカウント** の種類の新しいリソースを、**devicestate** という名前で作成します。
1. **[devicestate] -> [アクセス キー]** から **[接続文字列]** の値をコピーします。 ダウンロードしたサンプルの関数アプリ コードにこの文字列シークレットを追加する必要があります。
1. サンプルの[関数アプリ コード](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/main/custom-commands/quick-start)をダウンロードします。
1. ダウンロードしたソリューションを Visual Studio 2019 で開きます。 **Connections.js** で、**STORAGE_ACCOUNT_SECRET_CONNECTION_STRING** を手順 2 のシークレットに置き換えます。
1.  **DeviceStateAzureFunction** コードをダウンロードします。

サンプル アプリを Azure Functions にデプロイするには、次の手順に従います。

1. Azure Functions アプリを[デプロイ](../../azure-functions/index.yml)します。
1. デプロイが正常に完了するのを待って、Azure portal にデプロイされたリソースに移動します。 
1. 左ペインで **[Funtions]** を選択し、 **[DeviceState]** を選択します。
1.  新しいウィンドウで、 **[コード + テスト]** を選択し、 **[関数の URL の取得]** を選択します。
 
## <a name="setup-web-endpoints-in-custom-commands"></a>Custom Commands で Web エンドポイントを設定する

Azure 関数と既存の Custom Commands アプリを接続してみましょう。
このセクションでは、既存の既定の **DeviceState** エンドポイントを使用します。 Azure Function を使用するか別の方法で独自の Web エンドポイントを作成した場合、既定の `https://webendpointexample.azurewebsites.net/api/DeviceState` の代わりにそれを使用します。

1. 前に作成したカスタム コマンド アプリケーションを開きます。
1. **[Web エンドポイント]** に移動し、 **[New web endpoint]\(新しい Web エンドポイント\)** をクリックします。

   > [!div class="mx-imgBorder"]
   > ![新しい Web エンドポイント](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | 設定 | 推奨値 | 説明 |
   | ------- | --------------- | ----------- |
   | 名前 | UpdateDeviceState | Web エンドポイントの名前。 |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | カスタム コマンド アプリの通信相手とするエンドポイントの URL。 |
   | Method | POST | エンドポイントに対して許可されたやりとり (GET や POST など)。|
   | ヘッダー | キー: アプリ、値: applicationId の最初の 8 桁を取得します | 要求ヘッダーに含めるヘッダー パラメーター。|

    > [!NOTE]
    > - [Azure Functions](../../azure-functions/index.yml) を使用して作成された Web エンドポイントの例。テレビとファンのデバイス状態を保存するデータベースと結合されます。
    > - 提案されたヘッダーは、エンドポイントの例にのみ必要です。
    > - ヘッダーの値が例のエンドポイントで一意になるよう、**applicationId** の最初の 8 桁を取得します。
    > - 実際には、Web エンドポイントを、ご利用のデバイスを管理する [IOT ハブ](../../iot-hub/about-iot-hub.md)のエンドポイントとすることができます。

1. **[保存]** をクリックします。

## <a name="call-web-endpoints"></a>Web エンドポイントを呼び出す

1. **TurnOnOff** コマンドにアクセスし、完了ルールの **[ConfirmationResponse]** を選択してから、 **[アクションの追加]** を選択します。
1. **[新しいアクションの種類]** で、 **[Call Web Endpoint]\(Web エンドポイントの呼び出し\)** を選択します。
1. **[アクションの編集] - [エンドポイント]** で、作成済みの Web エンドポイントである **[UpdateDeviceState]** を選択します。  
1. **[構成]** で、次の値を入力します。
   > [!div class="mx-imgBorder"]
   > ![Web エンドポイント アクション パラメーターの呼び出し](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | 設定 | 推奨値 | 説明 |
   | ------- | --------------- | ----------- |
   | エンドポイント | UpdateDeviceState | このアクションで呼び出したい Web エンドポイント。 |
   | クエリ パラメーター | item={SubjectDevice}&&value={OnOff} | Web エンドポイントの URL に追加するクエリ パラメーター。  |
   | 本文のコンテンツ | 該当なし | 要求の本文のコンテンツ。 |

    > [!NOTE]
    > - 提案されたクエリ パラメーターは、エンドポイントの例にのみ必要です

1. **[成功時] - [実行するアクション]** で、 **[Send speech response]\(音声応答の送信\)** を選択します。

    **[シンプルなエディター]** に「`{SubjectDevice} is {OnOff}`」と入力します。

   > [!div class="mx-imgBorder"]
   > ![[成功時] - [実行するアクション] 画面を示すスクリーンショット。](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | 設定 | 推奨値 | 説明 |
   | ------- | --------------- | ----------- |
   | 実行するアクション | Send speech response (音声応答の送信) | Web エンドポイントへの要求が成功した場合に実行するアクション |

   > [!NOTE]
   > - `{YourWebEndpointName.FieldName}` を使用すれば、http 応答内のフィールドに直接アクセスすることもできます。 例: `{UpdateDeviceState.TV}`

1. **[失敗時] - [実行するアクション]** で、 **[Send speech response]\(音声応答の送信\)** を選択します。

    **[シンプルなエディター]** に「`Sorry, {WebEndpointErrorMessage}`」と入力します。

   > [!div class="mx-imgBorder"]
   > ![失敗時の Web エンドポイント アクションの呼び出し](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | 設定 | 推奨値 | 説明 |
   | ------- | --------------- | ----------- |
   | 実行するアクション | Send speech response (音声応答の送信) | Web エンドポイントへの要求が失敗した場合に実行するアクション |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` はオプションです。 これは、エラー メッセージを公開したくない場合、自由に削除できます。
   > - このエンドポイントの例では、ヘッダー パラメーターの欠落などの一般的なエラーの場合、詳細なエラー メッセージと共に http 応答を返送します。

### <a name="try-it-out-in-test-portal"></a>テスト ポータルで試してみる
- 応答が成功の場合、保存、トレーニング、およびテストを行います。
   > [!div class="mx-imgBorder"]
   > ![成功時の応答を示すスクリーンショット。](media/custom-commands/setup-web-endpoint-on-success-response.png)
- 応答が失敗の場合、クエリ パラメーターの 1 つを削除し、保存、再トレーニング、およびテストを行います。
   > [!div class="mx-imgBorder"]
   > ![成功時の Web エンドポイント アクションの呼び出し](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>クライアント アプリケーションと統合する

「[クライアント アプリケーションへのカスタム コマンド アクティビティの送信](./how-to-custom-commands-send-activity-to-client.md)」で、 **[クライアントへのアクティビティの送信]** アクションを追加しました。 このアクティビティは、 **[Call Web Endpoint]\(Web エンドポイントの呼び出し\)** アクションが正常に実行されたかどうかにかかわらず、クライアント アプリケーションに送信されます。
ただし、通常の場合、クライアント アプリケーションにアクティビティを送信する必要があるのは、Web エンドポイントへの呼び出しが成功したときのみです。 この例で、それはデバイスの状態が正常に更新されたときとなります。

1. 前に追加した **[クライアントへのアクティビティの送信]** アクションを削除します。
1. Web エンドポイントの呼び出しを次のように編集します。
    1. **[構成]** で、 **[クエリ パラメーター]** が `item={SubjectDevice}&&value={OnOff}` になっていることを確認する
    1. **[成功時]** の **[実行するアクション]** を **[クライアントへのアクティビティの送信]** に変更する
    1. 以下の JSON を **[Activity Content]\(アクティビティ コンテンツ\)** にコピーする
   ```json
   {
      "type": "event",
      "name": "UpdateDeviceState",
      "value": {
        "state": "{OnOff}",
        "device": "{SubjectDevice}"
      }
    }
   ```
これで、Web エンドポイントへの要求が成功した場合にのみ、アクティビティをクライアントに送信することになります。

### <a name="create-visuals-for-syncing-device-state"></a>デバイスの状態を同期するためのビジュアルを作成する

次の XML を、`MainPage.xaml` の **EnableMicrophoneButton** ブロックの上に追加します。

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>デバイスの状態の同期

`MainPage.xaml.cs` 内に、参照 `using Windows.Web.Http;` を追加します。 次のコードを `MainPage` クラスに追加します。 このメソッドでは、エンドポイントの例に GET 要求が送信され、ご利用のアプリに対する現在のデバイス状態が抽出されます。 `<your_app_name>` を必ず、カスタム コマンド Web エンドポイントの **ヘッダー** で使用したものに変更してください。

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request.
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>試してみる

1. アプリケーションを起動します。
1. [Sync Device State]\(デバイスの状態の同期\) をクリックします。\
前のセクションで `turn on tv` でアプリをテストしたのであれば、テレビが **オン** と表示されます。
    > [!div class="mx-imgBorder"]
    > ![デバイスの状態の同期](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. **[Enable Microphone]\(マイクを有効にする\)** を選択します。
1. **[Talk]\(トーク\)** ボタンを選択します。
1. `turn on the fan` と言います。 ファンの表示状態が **オン** に変わるはずです。
    > [!div class="mx-imgBorder"]
    > ![ファンをオンにする](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Custom Commands アプリケーションをリモートのスキルとしてエクスポートする](./how-to-custom-commands-integrate-remote-skills.md)
