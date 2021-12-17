---
title: チュートリアル:Blazor Server チャット アプリを構築する - Azure SignalR
description: このチュートリアルでは、Azure SignalR Service を使用して Blazor Server アプリの構築と変更を行う方法について説明します。
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: 5ed5107cadcfbf247b79c18a6a2e391ab3043565
ms.sourcegitcommit: dd425ae91675b7db264288f899cff6add31e9f69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2021
ms.locfileid: "108331828"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>チュートリアル:Blazor Server チャット アプリを構築する

このチュートリアルでは、Blazor Server アプリの構築と変更を行う方法について説明します。 学習内容は次のとおりです。
> [!div class="checklist"] 
> * Blazor Server アプリ テンプレートを使用して簡単なチャット ルームを作成する。
> * Razor コンポーネントを操作する。
> * Razor コンポーネントのイベント処理とデータ バインディングを使用する。
> * Visual Studio で Azure App Service にすばやくデプロイする。
> * ローカルの SignalR から Azure SignalR Service に移行する。

## <a name="prerequisites"></a>前提条件

* [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) (バージョン 3.0.100 以上) をインストールする
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) (バージョン 16.3 以上) をインストールする


[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsblazor)

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>Blazor Server アプリでローカル チャット ルームを作成する

Visual Studio 2019 バージョン 16.2.0 以降では、Azure SignalR Service が Web アプリケーションの発行プロセスに組み込まれているため、Web アプリと SignalR Service との間の依存関係の管理がはるかに容易になっています。 ローカル開発環境のローカル SignalR インスタンスでの作業と、Azure App Service 向けの Azure SignalR Service での作業を、コードに変更を加えることなく同時に行うことができます。

1. Blazor チャット アプリを作成します。
   1. Visual Studio で、 **[新しいプロジェクトの作成]** を選択します。 
   1. **[Blazor アプリ]** を選択します。 
   1. アプリケーションに名前を付け、フォルダーを選択します。 
   1. **[Blazor Server App] (Blazor Server アプリ)** テンプレートを選択します。
    
       > [!NOTE]
       > Visual Studio でターゲット フレームワークが正しく認識されるよう、あらかじめ .NET Core SDK 3.0 以上をインストールしておいてください。
   
       [ ![[新しいプロジェクトの作成] で、Blazor アプリ テンプレートを選択します。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   5. .NET CLI で [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを実行してプロジェクトを作成することもできます。
   
       ```dotnetcli
       dotnet new blazorserver -o BlazorChat
       ```
   
1. `BlazorChatSampleHub.cs` という名前の新しい C# ファイルを追加し、チャット アプリの `Hub` クラスから派生した新しいクラス `BlazorSampleHub` を作成します。 ハブの作成の詳細については、「[ハブの作成と使用](/aspnet/core/signalr/hubs#create-and-use-hubs)」を参照してください。 
   
   ```cs
   using System;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.SignalR;
   
   namespace BlazorChat
   {
       public class BlazorChatSampleHub : Hub
       {
           public const string HubUrl = "/chat";
   
           public async Task Broadcast(string username, string message)
           {
               await Clients.All.SendAsync("Broadcast", username, message);
           }
   
           public override Task OnConnectedAsync()
           {
               Console.WriteLine($"{Context.ConnectionId} connected");
               return base.OnConnectedAsync();
           }
   
           public override async Task OnDisconnectedAsync(Exception e)
           {
               Console.WriteLine($"Disconnected {e?.Message} {Context.ConnectionId}");
               await base.OnDisconnectedAsync(e);
           }
       }
   }
   ```
   
1. `Startup.Configure()` メソッドにハブのエンドポイントを追加します。
   
   ```cs
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapBlazorHub();
       endpoints.MapFallbackToPage("/_Host");
       endpoints.MapHub<BlazorChatSampleHub>(BlazorChatSampleHub.HubUrl);
   });
   ```
   
1. SignalR クライアントを使用するための `Microsoft.AspNetCore.SignalR.Client` パッケージをインストールします。

   ```dotnetcli
   dotnet add package Microsoft.AspNetCore.SignalR.Client --version 3.1.7
   ```

1. SignalR クライアントを実装するために、`Pages` フォルダーの下に `ChatRoom.razor` という名前の新しい [Razor コンポーネント](/aspnet/core/blazor/components/)を作成します。 以下の手順に従うか、[ChatRoom.razor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor) ファイルを使用してください。

   1. [`@page`](/aspnet/core/mvc/views/razor#page) ディレクティブと using ステートメントを追加します。 [`@inject`](/aspnet/core/mvc/views/razor#inject) ディレクティブを使用して、[`NavigationManager`](/aspnet/core/blazor/fundamentals/routing#uri-and-navigation-state-helpers) サービスを挿入します。
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. メッセージを送受信するために、`@code` セクションで、次のメンバーを新しい SignalR クライアントに追加します。
      
      ```razor
      @code {
          // flag to indicate chat status
          private bool _isChatting = false;
          
          // name of the user who will be chatting
          private string _username;
      
          // on-screen message
          private string _message;
          
          // new message input
          private string _newMessage;
          
          // list of messages in chat
          private List<Message> _messages = new List<Message>();
          
          private string _hubUrl;
          private HubConnection _hubConnection;
      
          public async Task Chat()
          {
              // check username is valid
              if (string.IsNullOrWhiteSpace(_username))
              {
                  _message = "Please enter a name";
                  return;
              };
      
              try
              {
                  // Start chatting and force refresh UI.
                  _isChatting = true;
                  await Task.Delay(1);

                  // remove old messages if any
                  _messages.Clear();
         
                  // Create the chat client
                  string baseUrl = navigationManager.BaseUri;
      
                  _hubUrl = baseUrl.TrimEnd('/') + BlazorChatSampleHub.HubUrl;
      
                  _hubConnection = new HubConnectionBuilder()
                      .WithUrl(_hubUrl)
                      .Build();
      
                  _hubConnection.On<string, string>("Broadcast", BroadcastMessage);
      
                  await _hubConnection.StartAsync();
      
                  await SendAsync($"[Notice] {_username} joined chat room.");
              }
              catch (Exception e)
              {
                  _message = $"ERROR: Failed to start chat client: {e.Message}";
                  _isChatting = false;
              }
          }
      
          private void BroadcastMessage(string name, string message)
          {
              bool isMine = name.Equals(_username, StringComparison.OrdinalIgnoreCase);
      
              _messages.Add(new Message(name, message, isMine));
      
              // Inform blazor the UI needs updating
              StateHasChanged();
          }
      
          private async Task DisconnectAsync()
          {
              if (_isChatting)
              {
                  await SendAsync($"[Notice] {_username} left chat room.");
      
                  await _hubConnection.StopAsync();
                  await _hubConnection.DisposeAsync();
      
                  _hubConnection = null;
                  _isChatting = false;
              }
          }
      
          private async Task SendAsync(string message)
          {
              if (_isChatting && !string.IsNullOrWhiteSpace(message))
              {
                  await _hubConnection.SendAsync("Broadcast", _username, message);
      
                  _newMessage = string.Empty;
              }
          }
      
          private class Message
          {
              public Message(string username, string body, bool mine)
              {
                  Username = username;
                  Body = body;
                  Mine = mine;
              }
      
              public string Username { get; set; }
              public string Body { get; set; }
              public bool Mine { get; set; }
      
              public bool IsNotice => Body.StartsWith("[Notice]");
      
              public string CSS => Mine ? "sent" : "received";
          }
      }
      ```

   1. SignalR クライアントと対話するために、`@code` セクションの前に UI マークアップを追加します。
      
      ```razor
      <h1>Blazor SignalR Chat Sample</h1>
      <hr />
      
      @if (!_isChatting)
      {
          <p>
              Enter your name to start chatting:
          </p>
      
          <input type="text" maxlength="32" @bind="@_username" />
          <button type="button" @onclick="@Chat"><span class="oi oi-chat" aria-hidden="true"></span> Chat!</button>
      
          // Error messages
          @if (_message != null)
          {
              <div class="invalid-feedback">@_message</div>
              <small id="emailHelp" class="form-text text-muted">@_message</small>
          }
      }
      else
      {
          // banner to show current user
          <div class="alert alert-secondary mt-4" role="alert">
              <span class="oi oi-person mr-2" aria-hidden="true"></span>
              <span>You are connected as <b>@_username</b></span>
              <button class="btn btn-sm btn-warning ml-md-auto" @onclick="@DisconnectAsync">Disconnect</button>
          </div>
          // display messages
          <div id="scrollbox">
              @foreach (var item in _messages)
              {
                  @if (item.IsNotice)
                  {
                      <div class="alert alert-info">@item.Body</div>
                  }
                  else
                  {
                      <div class="@item.CSS">
                          <div class="user">@item.Username</div>
                          <div class="msg">@item.Body</div>
                      </div>
                  }
              }
              <hr />
              <textarea class="input-lg" placeholder="enter your comment" @bind="@_newMessage"></textarea>
              <button class="btn btn-default" @onclick="@(() => SendAsync(_newMessage))">Send</button>
          </div>
      }
      ```

1. `NavMenu.razor` コンポーネントを更新して、`NavMenuCssClass` の下のチャット ルームにリンクする新しい `NavLink` コンポーネントを挿入します。

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. `site.css` ファイルにいくつかの CSS クラスを追加して、チャット ページの UI 要素のスタイルを設定します。

   ```css
   /* improved for chat text box */
   textarea {
       border: 1px dashed #888;
       border-radius: 5px;
       width: 80%;
       overflow: auto;
       background: #f7f7f7
   }
   
   /* improved for speech bubbles */
   .received, .sent {
       position: relative;
       font-family: arial;
       font-size: 1.1em;
       border-radius: 10px;
       padding: 20px;
       margin-bottom: 20px;
   }
   
   .received:after, .sent:after {
       content: '';
       border: 20px solid transparent;
       position: absolute;
       margin-top: -30px;
   }
   
   .sent {
       background: #03a9f4;
       color: #fff;
       margin-left: 10%;
       top: 50%;
       text-align: right;
   }
   
   .received {
       background: #4CAF50;
       color: #fff;
       margin-left: 10px;
       margin-right: 10%;
   }
   
   .sent:after {
       border-left-color: #03a9f4;
       border-right: 0;
       right: -20px;
   }
   
   .received:after {
       border-right-color: #4CAF50;
       border-left: 0;
       left: -20px;
   }
   
   /* div within bubble for name */
   .user {
       font-size: 0.8em;
       font-weight: bold;
       color: #000;
   }
   
   .msg {
       /*display: inline;*/
   }
   ```

1. <kbd>F5</kbd> キーを押して、アプリを実行します。 これで、チャットを開始できます。

   [ ![Bob と Alice との間で交わされたチャットのアニメーションが表示されています。Alice が "Hello" と言うと、Bob が "Hi" と言います。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsblazor)

## <a name="publish-to-azure"></a>Azure に発行する

Azure App Service に Blazor アプリをデプロイする場合は、[Azure SignalR Service](/aspnet/core/signalr/scale#azure-signalr-service) を使用することをお勧めします。 Azure SignalR Service を使用すると、Blazor Server アプリを多数の同時 SignalR 接続にスケールアップできます。 さらに、SignalR Service のグローバル リーチとハイパフォーマンスのデータセンターは、地理的条件による待ち時間の短縮に大きく役立ちます。

> [!IMPORTANT]
> Blazor Server アプリでは、UI の状態はサーバー側で保持されます。つまり、状態を保持するには、スティッキー サーバー セッションが必要です。 アプリ サーバーが 1 台の場合は、設計によってスティッキー セッションが保証されます。 これに対し、複数のアプリ サーバーがある場合は、クライアントのネゴシエーションと接続に異なるサーバーが使用された結果、Blazor アプリの UI 状態管理に一貫性がなくなる可能性があります。 そのため、次に示すように、*appsettings.json* 内でスティッキー サーバー セッションを有効にすることをお勧めします。
>
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. プロジェクトを右クリックし、 **[発行]** を選択します。 次の設定を使用します。
   * **ターゲット**: Azure
   * **特定のターゲット**: すべての種類の **Azure App Service** がサポートされています。
   * **App Service**: App Service インスタンスを作成または選択します。

   [ ![このアニメーションでは、[ターゲット] として Azure を選択し、[特定のターゲット] として Azure App Service を選択しています。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. Azure SignalR Service の依存関係を追加します。

   発行プロファイルの作成後、 **[サービスの依存関係]** に Azure SignalR Service を追加することを提案するメッセージが表示されます。 このペインで **[構成]** を選択して、既存の Azure SignalR Service を選択するか、新しく作成してください。

   [ ![[発行] で、[構成] へのリンクが強調表示されています。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   Azure SignalR Service が Azure 上にあればアプリがそれに自動的に切り替わるよう、サービス依存関係によって以下のアクティビティが実行されます。

   * Azure SignalR Service を使用するように [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration) を更新する。
   * Azure SignalR Service NuGet パッケージの参照を追加する。
   * プロファイル プロパティを更新して、依存関係の設定を保存する。
   * 選択内容に従ってシークレット ストアを構成する。
   * Azure SignalR Service がアプリのターゲットとなるように *appsettings.json* に構成を追加する。

   [ ![[Summary of changes]\(変更の概要\) で、チェック ボックスを使用してすべての依存関係が選択されています。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. アプリの発行

   これで、アプリを発行する準備が整いました。 発行プロセスが完了すると、ブラウザー内でアプリが自動的に起動されます。
 
   > [!NOTE]
   > Azure App Service のデプロイが開始されるまでの待ち時間が原因で、アプリの起動に時間がかかる場合があります。 (通常は <kbd>F12</kbd> キーを押して) ブラウザーのデバッガー ツールを使用すると、トラフィックが Azure SignalR Service にリダイレクトされていることを確認できます。

   [ ![Blazor SignalR Chat Sample には、自分の名前を入力するためのテキスト ボックスと、会話を開始するための [Chat!] ボタンがあります。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsblazor)

## <a name="enable-azure-signalr-service-for-local-development"></a>ローカル開発用に Azure SignalR Service を有効にする

1. 次のコマンドを使用して、Azure SignalR SDK への参照を追加します。

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. 次に示すように、`AddAzureSingalR()` の呼び出しを `Startup.ConfigureServices()` に追加します。

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. *appsettings.json* 内で、または[シークレット マネージャー](/aspnet/core/security/app-secrets?tabs=visual-studio#secret-manager) ツールを使用して、Azure SignalR Service の接続文字列を構成します。

> [!NOTE]
> 手順 2 の代わりに、SignalR SDK を使用するように[ホスティング スタートアップ アセンブリ](/aspnet/core/fundamentals/host/platform-specific-configuration)を構成することができます。
>
> 1. Azure SignalR Service を有効にするための構成を *appsettings.json* に追加します。
>
>     ```json
>     "Azure": {
>       "SignalR": {
>         "Enabled": true,
>         "ConnectionString": <your-connection-string>       
>       }
>     }
>    
>    ```
>
> 1. Azure SignalR SDK を使用するようにホスティング スタートアップ アセンブリを構成します。 *launchSettings.json* を編集して、`environmentVariables` 内に次の例のような構成を追加します。
>
>     ```json
>    "environmentVariables": {
>         ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>  
>     ```
>

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsblazor)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースをクリーンアップするには、Azure portal を使用してリソース グループを削除します。

## <a name="additional-resources"></a>その他のリソース

* [ASP.NET Core Blazor](/aspnet/core/blazor)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Blazor Server アプリ テンプレートを使用して簡単なチャット ルームを作成する。
> * Razor コンポーネントを操作する。
> * Razor コンポーネントのイベント処理とデータ バインディングを使用する。
> * Visual Studio で Azure App Service にすばやくデプロイする。
> * ローカルの SignalR から Azure SignalR Service に移行する。

高可用性についての詳細を参照してください。
> [!div class="nextstepaction"]
> [回復性とディザスター リカバリー](signalr-concept-disaster-recovery.md)
