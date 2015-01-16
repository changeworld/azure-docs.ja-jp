<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs によるユーザーへの通知" metaKeywords="Azure push notifications, Azure notification hubs" description="Azure でセキュリティで保護されたプッシュ通知を送信する方法について説明します。コード サンプルは .NET API を使用して C# で記述されています。" documentationCenter="" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="glenga" manager="dwrede" services="notification-hubs" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />

#Azure Notification Hubs によるユーザーへの通知

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/ja-jp/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal" class="current">Windows ユニバーサル</a><a href="/ja-jp/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
		<a href="/ja-jp/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

Azure でプッシュ通知がサポートされたことで、マルチプラットフォームに対応し、簡単に使用できる、スケールアウトされたプッシュ通知インフラストラクチャを利用できるようになりました。これにより、モバイル プラットフォーム向けアプリケーション (コンシューマー用途およびエンタープライズ用途) にプッシュ通知機能を実装する作業が大幅に簡略化されます。このチュートリアルでは、Azure Notification Hubs を使用して特定のデバイスで特定のアプリケーション ユーザーにプッシュ通知を送信する方法について説明します。ガイダンス トピック「[アプリ バックエンドからの登録](http://msdn.microsoft.com/ja-jp/library/dn743807.aspx)」に示すように、ASP.NET WebAPI バックエンドを使用してクライアントを認証し、通知を生成します。このチュートリアルは、「**通知ハブの使用**」チュートリアルで作成した通知ハブが基になっています。

また、このチュートリアルは、「**Azure Notification Hubs の安全なプッシュ**」チュートリアルの前提条件でもあります。この「**ユーザーへの通知**」チュートリアルの手順を完了した後は、**ユーザーへの通知**のコードを変更してプッシュ通知を安全に送信する方法を示した「**安全なプッシュ**」チュートリアルに進むことができます。 

> [AZURE.NOTE] このチュートリアルでは、「[通知ハブの使用 (Windows ストア)](http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/)」の説明に従って通知ハブが作成され、構成されていることを前提にしています。
> バックエンド サービスとして Mobile Services を使用している場合は、このチュートリアルの [Mobile Services バージョン](/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/) を参照してください。
>このチュートリアルでは、Windows Phone ストア 8.1 アプリケーションを作成する点にも注意してください。同じコードを Windows ストア アプリケーションと Windows ユニバーサル アプリケーションに使用することができます。これらのアプリケーションはすべて、Windows (Windows Phone ではなく) の資格情報を使用する必要があります。

## 通知ハブを作成し構成する

このチュートリアルを開始する前に、アプリケーション名を予約してから、Azure Notification Hub を作成してそのアプリケーションに接続します。「[通知ハブの使用 (Windows ストア)](http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/)」の特に、「[アプリケーションを Windows ストアに登録する](http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#register) 」および「[通知ハブを構成する](http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub)」のセクションの手順に従います。特に、ポータルで通知ハブの **[構成]** タブに **[パッケージ SID]** と **[クライアント シークレット]** の値を入力してください。この構成手順は、セクション「[通知ハブを構成する](http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub)」で説明しています。これは重要な手順です。ポータルの資格情報が、選択したアプリケーション名に指定した資格情報と一致しない場合、プッシュ通知は成功しません。

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Windows Phone プロジェクトを作成する

次の手順では、Windows Phone アプリケーションを作成します。次の手順を実行して、このプロジェクトを現在のソリューションに追加します。

1. ソリューション エクスプローラーで、ソリューションの最上位ノード (この場合は、**Solution NotifyUsers**) を右クリックし、**[追加]**、**[新しいプロジェクト]** の順にクリックします。

2. **[ストア アプリ]** を展開し、**[Windows Phone アプリ]**、**[空のアプリケーション (Windows Phone)]** の順にクリックします。

	![][9]

3. **[名前]** ボックスに、「**NotifyUserWindowsPhone**」と入力し、**[OK]** をクリックしてプロジェクトを生成します。

 
4. このアプリケーションを Windows Phone ストアと関連付けます。ソリューション エクスプローラーで、**NotifyUserWindowsPhone (Windows Phone 8.1)** を右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

	![][10]
 
5. ウィザードの手順に従ってサインインし、アプリケーションをストアに関連付けます。

	![][11]
	
	> [AZURE.NOTE] この手順で選択したアプリケーションの名前をメモしてください。この特定の予約されたアプリケーション名に対し、 [Windows デベロッパー センター](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409) から取得した資格情報を使用して、ポータルの通知ハブを構成する必要があります。この構成手順は、「[通知ハブを構成する](http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub)」で説明しています。これは重要な手順です。ポータルの資格情報が、選択したアプリケーション名に指定した資格情報と一致しない場合、プッシュ通知は成功しません。

6. ソリューション エクスプローラーで **NotifyUserWindowsPhone (Windows Phone 8.1)** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

7. 左側で、**[オンライン]** をクリックします。

8. **[検索]** ボックスに、「**Http Client**」と入力します。

9. 結果の一覧で、**[Microsoft HTTP Client Libraries]**、**[インストール]** の順にクリックします。インストールを完了します。

10. NuGet **[検索]** ボックスに戻り、「**Json.net**」と入力します。**Json.NET** パッケージをインストールし、NuGet パッケージ マネージャーのウィンドウを閉じます。

11. ソリューション エクスプローラーで、**NotifyUserWindowsPhone (Windows Phone 8.1)** プロジェクトの **[MainPage.xaml]** をダブルクリックして、Visual Studio エディターで開きます。

12. **MainPage.xaml** XML コードで、`<Grid>` セクションを次のコードで置き換えます。

		<Grid>
	        <Grid.RowDefinitions>
	            <RowDefinition Height="Auto"/>
	            <RowDefinition Height="*"/>
	        </Grid.RowDefinitions>

	        <TextBlock Grid.Row="0" Text="Secure Push" HorizontalAlignment="Center" FontSize="48"/>

        	<StackPanel Grid.Row="1" VerticalAlignment="Center">
        	    <Grid>
        	        <Grid.RowDefinitions>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="*"/>
        	        </Grid.RowDefinitions>
            	    <TextBlock Grid.Row="0" Text="Username" FontSize="24" Margin="20,0,20,0"/>
            	    <TextBox Name="UsernameTextBox" Grid.Row="1" Margin="20,0,20,0"/>
            	    <TextBlock Grid.Row="2" Text="Password" FontSize="24" Margin="20,0,20,0" />
            	    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Margin="20,0,20,0"/>
	
            	    <Button Grid.Row="4" HorizontalAlignment="Center" VerticalAlignment="Center" Content="1. Login and register" Click="LoginAndRegisterClick" />

            	    <Button Grid.Row="5" HorizontalAlignment="Center" VerticalAlignment="Center" Content="2. Send push" Click="PushClick" />
            	</Grid>
        	</StackPanel>
    	</Grid>


13. ソリューション エクスプローラーで、**NotifyUserWindowsPhone (Windows Phone 8.1)** プロジェクトを右クリックし、**[追加]**、**[クラス]** の順にクリックします。クラスに **RegisterClient.cs** という名前を付け、**[OK]** をクリックしてクラスを生成します。プッシュ通知を登録するために、このコンポーネントはアプリケーション バックエンドにアクセスするのに必要な REST 呼び出しを実装します。「[アプリ バックエンドからの登録](http://msdn.microsoft.com/ja-jp/library/dn743807.aspx)」で説明しているとおり、通知ハブによって作成された *registrationIds* もローカルに格納されます。**[ログインして登録]** をクリックすると、ローカル ストレージに格納した認証トークンが使用されることに注意してください。

14. `RegisterClient` クラス定義内で、次のコードを追加します。必ず、前のセクションで取得したバックエンド エンドポイントで `{backend endpoint}` を置き換えてください。

		private string POST_URL = "{backend endpoint}/api/register";

        private class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        public async Task RegisterAsync(string handle, IEnumerable<string> tags)
        {
            var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

            var deviceRegistration = new DeviceRegistration
            {
                Platform = "wns",
                Handle = handle,
                Tags = tags.ToArray<string>()
            };

            var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

            if (statusCode == HttpStatusCode.Gone)
            {
                // regId is expired, deleting from local storage & recreating
                var settings = ApplicationData.Current.LocalSettings.Values;
                settings.Remove("__NHRegistrationId");
                regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
                statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
            }

            if (statusCode != HttpStatusCode.Accepted)
            {
                // log or throw
            }
        }

        private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
        {
            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                var putUri = POST_URL + "/" + regId;

                string json = JsonConvert.SerializeObject(deviceRegistration);
                                var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
                return response.StatusCode;
            }
        }

        private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            if (!settings.ContainsKey("__NHRegistrationId"))
            {
                using (var httpClient = new HttpClient())
                {
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                    if (response.IsSuccessStatusCode)
                    {
                        string regId = await response.Content.ReadAsStringAsync();
                        regId = regId.Substring(1, regId.Length - 2);
                        settings.Add("__NHRegistrationId", regId);
                    }
                    else
                    {
                        throw new Exception();
                    }
                }
            }
            return (string)settings["__NHRegistrationId"];

        }


15. RegisterClient.cs ファイルの先頭に次の `using` ステートメントを追加します。

		using Windows.Storage;
		using System.Net;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using Newtonsoft.Json;
		
16. MainPage.xaml.cs でボタンのコードを追加します。**Log in and register** に対するコールバックは、ローカル ストレージに基本認証トークン (これは認証スキームが使用する任意のトークンを表します) を格納し、`RegisterClient` を使用してバックエンドを呼び出します。**AppBackend** に対するコールバックは、バックエンドを呼び出し、このユーザーのすべてのデバイスに対してセキュリティで保護された通知をトリガーします。 

	MainPage.xaml.cs で `OnNavigatedTo()` メソッドの後に次のコードを追加します。必ず、前のセクションで取得したバックエンド エンドポイントで `{backend endpoint}` を置き換えてください。

		private async void PushClick(object sender, RoutedEventArgs e)
        {
            var POST_URL = "{backend endpoint}/api/notifications";

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                await httpClient.PostAsync(POST_URL, new StringContent(""));
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();
            
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            await new RegisterClient().RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;
            
            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }


17. MainPage.xaml.cs ファイルの先頭に次の `using` ステートメントを追加します。

		using System.Net.Http;
		using Windows.Storage;
		using System.Net.Http.Headers;
		using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

## アプリケーションの実行

アプリケーションを実行するには、以下の手順に従います。

1. Visual Studio で、**NotifyUserWindowsPhone (Windows Phone 8.1)** Windows Phone アプリケーションを実行します。Windows Phone エミュレーターが自動的に起動し、アプリケーションを読み込みます。

2. **NotifyUserWindowsPhone** アプリケーションの UI で、ユーザー名とパスワードを入力します。文字列は任意ですが、値は同じである必要があります。

3. **NotifyUserWindowsPhone** アプリケーションの UI で、**[Log in and register]** をクリックします。次に、**[Send push]** をクリックします。


[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push13.png

<!--HONumber=35.1-->
