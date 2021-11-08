---
title: クイックスタート - Teams の会議に参加する
author: juramir
ms.author: juramir
ms.date: 10/15/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: acab57f774d97c2cd9710b77f6a0ebabf73e4fdb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520094"
---
このクイックスタートでは、C# 用 Azure Communication Services Chat SDK を使用して Teams 会議でチャットする方法について説明します。

## <a name="sample-code"></a>サンプル コード
このクイックスタートのコードは [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/ChatTeamsInteropQuickStart) にあります。

## <a name="prerequisites"></a>前提条件 

*  [Teams のデプロイ](/deployoffice/teams-install)。 
* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。  
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) のインストール。[ユニバーサル Windows プラットフォーム開発] ワークロードを選択してください。  
* デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../create-communication-resource.md)。 
* Teams 会議のリンク。

## <a name="joining-the-meeting-chat"></a>会議チャットへの参加 

Communication Services ユーザーは、Calling SDK を使用して、匿名ユーザーとして Teams 会議に参加できます。 会議に参加すると、会議チャットにも参加者として追加されます。会議チャットでは、会議に参加している他のユーザーとメッセージを送受信できます。 ユーザーは、会議に参加する前に送信されたチャット メッセージにアクセスすることはできず、会議の終了後はメッセージを送受信することはできなくなります。 会議に参加してチャットを開始するには、次の手順に従います。

## <a name="run-the-code"></a>コードの実行
コードは、Visual Studio でビルドして実行できます。 ソリューションのプラットフォームに関しては、`x64`、`x86`、`ARM64` がサポートされる点に注意してください。 

1. PowerShell、Windows ターミナル、コマンド プロンプト、またはそれと同等のインスタンスを開き、サンプルの複製先のディレクトリに移動します。
2. `git clone https://github.com/Azure-Samples/Communication-Services-dotnet-quickstarts.git`
3. Visual Studio でプロジェクト ChatTeamsInteropQuickStart/ChatTeamsInteropQuickStart.csproj を開きます。
4. 次の NuGet パッケージ バージョン (またはそれ以降) をインストールします。
``` csharp
Install-Package Azure.Communication.Calling -Version 1.0.0-beta.29
Install-Package Azure.Communication.Chat -Version 1.1.0
Install-Package Azure.Communication.Common -Version 1.0.1
Install-Package Azure.Communication.Identity -Version 1.0.1

```

5. 前提条件で入手済みの Communication Services リソースを使用して、**ChatTeamsInteropQuickStart/MainPage.xaml.cs** ファイルに connectionstring を追加します。 

``` csharp
//ACS resource connection string i.e = "endpoint=https://your-resource.communication.azure.net/;accesskey=your-access-key";
private const string connectionString_ = "";
```

> [!IMPORTANT]
> * コードを実行する <b>前</b> に、Visual Studio の [ソリューション プラットフォーム] ドロップダウン リストから適切なプラットフォームを選択してください。 つまり `x64`
> * Windows 10 で '開発者モード' が有効になっていることを確認します [(開発者の設定)](/windows/apps/get-started/enable-your-device-for-development)
>  
>  *適切に構成されていない場合、次の手順はうまくいきません*


6. F5 キーを押して、プロジェクトをデバッグ モードで起動します。
7. [Teams 会議のリンク] ボックスに有効な Teams 会議のリンクを貼り付けます (次のセクションを参照)
8. [Teams 会議に参加] を押して、チャットを開始します。

> [!IMPORTANT]
> 呼び出し元の SDK によって Teams 会議との接続が確立されたら ([Communication Services 通話 Windows アプリを参照](../../voice-video-calling/getting-started-with-calling.md))、チャット操作を処理するための主要な関数は、StartPollingForChatMessages と SendMessageButton_Click です。 両方のコード スニペットは ChatTeamsInteropQuickStart\MainPage.xaml.cs にあります 

```csharp
        /// <summary>
        /// Background task that keeps polling for chat messages while the call connection is stablished
        /// </summary>
        private async Task StartPollingForChatMessages()
        {
            CommunicationTokenCredential communicationTokenCredential = new(user_token_);
            chatClient_ = new ChatClient(EndPointFromConnectionString(), communicationTokenCredential);
            await Task.Run(async () =>
            {
                keepPolling_ = true;

                ChatThreadClient chatThreadClient = chatClient_.GetChatThreadClient(thread_Id_);
                int previousTextMessages = 0;
                while (keepPolling_)
                {
                    try
                    {
                        CommunicationUserIdentifier currentUser = new(user_Id_);
                        AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
                        SortedDictionary<long, string> messageList = new();
                        int textMessages = 0;
                        string userPrefix;
                        await foreach (ChatMessage message in allMessages)
                        {
                            if (message.Type == ChatMessageType.Html || message.Type == ChatMessageType.Text)
                            {
                                textMessages++;
                                userPrefix = message.Sender.Equals(currentUser) ? "[you]:" : "";
                                messageList.Add(long.Parse(message.SequenceId), $"{userPrefix}{StripHtml(message.Content.Message)}");
                            }
                        }

                        //Update UI just when there are new messages
                        if (textMessages > previousTextMessages)
                        {
                            previousTextMessages = textMessages;
                            await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
                            {
                                TxtChat.Text = string.Join(Environment.NewLine, messageList.Values.ToList());
                            });

                        }
                        if (!keepPolling_)
                        {
                            return;
                        }

                        await SetInCallState(true);
                        await Task.Delay(3000);
                    }
                    catch (Exception e)
                    {
                        await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
                        {
                            _ = new MessageDialog($"An error ocurred while fetching messages in PollingChatMessagesAsync(). The application will shutdown. Details : {e.Message}").ShowAsync();
                            throw e;
                        });
                        await SetInCallState(false);
                    }
                }
            });
        }
        private async void SendMessageButton_Click(object sender, RoutedEventArgs e)
        {
            SendMessageButton.IsEnabled = false;
            ChatThreadClient chatThreadClient = chatClient_.GetChatThreadClient(thread_Id_);
            _ = await chatThreadClient.SendMessageAsync(TxtMessage.Text);
            
            TxtMessage.Text = "";
            SendMessageButton.IsEnabled = true;
        }
```



## <a name="get-a-teams-meeting-link"></a>Teams 会議のリンクを取得する

Teams 会議のリンクは Graph API を使用して取得できます。詳細については、[Graph のドキュメント](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)を参照してください。 このリンクは `onlineMeeting` リソースの一部として返され、[`joinWebUrl` プロパティ](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)の下からアクセスできます。 

また、必要な会議のリンクは、Teams 会議の招待状自体にある **[Join Meeting]\(会議に参加\)** の URL から取得することもできます。
Teams の会議リンクは、`https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` のようになっています。 

:::image type="content" source="../join-teams-meeting-chat-quickstart-windows.png" alt-text="完了した C# アプリケーションのスクリーンショット。":::

> [!NOTE] 
> 現在、Teams との相互運用性のシナリオでは、メッセージの送受信と編集、および入力通知の送信のみがサポートされています。 開封確認メッセージや、Communication Services ユーザーが Teams 会議に他のユーザーを追加または削除するなどの機能は、まだサポートされていません。
