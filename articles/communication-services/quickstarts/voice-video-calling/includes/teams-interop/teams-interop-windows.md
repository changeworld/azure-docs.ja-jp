---
title: クイックスタート - Windows アプリから Teams 会議に参加する
description: このチュートリアルでは、Windows 用 Azure Communication Services Calling SDK を使用して Teams 会議に参加する方法について説明します
author: aurighet
ms.author: aurighet
ms.date: 05/13/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 62a13298e62a9c7f1cfe7a0643f9db2a1dfc9d15
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520945"
---
このクイックスタートでは、Windows 用 Azure Communication Services Calling SDK を使用して Teams 会議に参加する方法について説明します。

## <a name="prerequisites"></a>前提条件

- 実際に動作する [Communication Services 通話 Windows アプリ](../../getting-started-with-calling.md)。
- [Teams のデプロイ](/deployoffice/teams-install)。

## <a name="add-the-teams-ui-controls-and-enable-the-teams-ui-controls"></a>Teams UI コントロールを追加し、Teams UI コントロールを有効にする

MainPage.xaml のコードを次のスニペットに置き換えます。 テキスト ボックスは、Teams の会議のコンテキストを入力する目的で、また、ボタンは、指定した会議に参加する目的で使用されます。

```xml
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
        <TextBox PlaceholderText="Please enter the Teams meeting link." TextWrapping="Wrap" x:Name="TeamsLinkTextBox" Margin="10,10,10,10" />
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Center">
            <Button Content="Join Teams Meeting" Click="JoinButton_ClickAsync" x:Name="JoinButton" Margin="10,10,10,10" />
            <Button Content="Leave Meeting" Click="LeaveButton_ClickAsync" x:Name="LeaveButton" Margin="10,10,10,10" />
        </StackPanel>
        <TextBlock TextWrapping="Wrap" x:Name="CallStatusTextBlock" Margin="10,10,10,10" />
        <TextBlock TextWrapping="Wrap" x:Name="RecordingStatusTextBlock" Margin="10,10,10,10" />
    </StackPanel>
</Page>
```

## <a name="enable-the-teams-ui-controls"></a>Teams の UI コントロールを有効にする

`MainPage.xaml.cs` の内容を次のスニペットで置き換えます。

```csharp
using System;
using System.Threading.Tasks;

using Windows.UI.Core;
using Windows.UI.Popups;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

using Azure.Communication;
using Azure.Communication.Calling;

namespace CallingQuickstart
{
    public sealed partial class MainPage : Page
    {
        string user_token_ = "<User_Access_Token>";

        Call call_;
        CallClient call_client;
        CallAgent call_agent;        

        public MainPage()
        {
            this.InitializeComponent();
            call_client = new CallClient();
        }

        private async void JoinButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            if (!await ValidateInput())
            {
                return;
            }

            //
            //  Create CallAgent
            //
            CommunicationTokenCredential token_credential;
            try
            {
                token_credential = new CommunicationTokenCredential(user_token_);
                CallAgentOptions call_agent_options = new CallAgentOptions();
                call_agent = await call_client.CreateCallAgent(token_credential, call_agent_options);
            }
            catch
            {
                await new MessageDialog("It was not possible to create call agent. Please check if token is valid.").ShowAsync();
                return;
            }

            //
            //  Join a Teams meeting
            //
            try
            {
                JoinCallOptions joinCallOptions = new JoinCallOptions();
                TeamsMeetingLinkLocator teamsMeetingLinkLocator = new TeamsMeetingLinkLocator(TeamsLinkTextBox.Text);
                call_ = await call_agent.JoinAsync(teamsMeetingLinkLocator, joinCallOptions);
            }
            catch
            {
                await new MessageDialog("It was not possible to join the Teams meeting. Please check if Teams Link is valid.").ShowAsync();
                return;
            }

            //
            //  Set up call callbacks
            //
            call_.OnStateChanged += Call_OnStateChangedAsync;
            call_.OnIsRecordingActiveChanged += Call_OnIsRecordingActiveChangedAsync;
        }

        private async void Call_OnStateChangedAsync(object sender, PropertyChangedEventArgs args)
        {
            await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                CallStatusTextBlock.Text = call_.State.ToString();
            });
        }
        
        private async void Call_OnIsRecordingActiveChangedAsync(object sender, PropertyChangedEventArgs args)
        {
            await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                RecordingStatusTextBlock.Text = call_.IsRecordingActive ? "Recording is active." : "Recording is inactive.";
            });
        }
        
        private async void LeaveButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            try
            {
                await call_.HangUp(new HangUpOptions());
            }
            catch
            {
                await new MessageDialog("It was not possible to leave the Teams meeting.").ShowAsync();
            }
        }
        
        private async Task<bool> ValidateInput()
        {
            if (user_token_.StartsWith("<"))
            {
                await new MessageDialog("Please enter token in source code.").ShowAsync();
                return false;
            }
        
            if (TeamsLinkTextBox.Text.Trim().Length == 0 || !TeamsLinkTextBox.Text.StartsWith("http"))
            {
                await new MessageDialog("Please enter Teams meeting link.").ShowAsync();
                return false;
            }
        
            return true;
        }
    }
}
```

## <a name="get-the-teams-meeting-link"></a>Teams 会議のリンクを取得する

Teams 会議のリンクは、Graph API を使用して取得できます。 この点については、[Graph のドキュメント](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)で詳しく説明されています。
Communication Services 通話 SDK は、Teams 会議のフル リンクを受け入れます。 このリンクは `onlineMeeting` リソースの一部として返され、[`joinWebUrl` プロパティ](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)の下からアクセスできます。 また、必要な会議情報は、Teams 会議の招待状自体にある **[Join Meeting]\(会議に参加\)** の URL から取得することもできます。

## <a name="launch-the-app-and-join-teams-meeting"></a>アプリを起動し、Teams 会議に参加する

Visual Studio から **[デバッグ]**  >  **[デバッグの開始]** を選択するか F5 キーボード ショートカットを使用して、アプリをビルド、実行できます。

Communication Services アプリケーション内から Teams の会議に参加するには、Teams のコンテキストをテキスト ボックスに挿入し、 *[Join Teams Meeting]\(Teams の会議に参加\)* を押します。
