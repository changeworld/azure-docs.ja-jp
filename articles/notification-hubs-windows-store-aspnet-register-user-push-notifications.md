<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-aspnet-webapi-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Web API" pageTitle="Register the current user for push notifications by using Web API - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by ASP.NET Web API." metaCanonical="" services="notification-hubs" documentationCenter="" title="Register the current user for push notifications by using ASP.NET" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# ASP.NET を使用した現在のユーザーのプッシュ通知への登録

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/notification-hubs-windows-store-aspnet-register-user-push-notifications/" title="Windows ストア C#" class="current">Windows ストア C#</a><a href="/ja-jp/documentation/articles/notification-hubs-ios-aspnet-register-user-push-notifications/" title="iOS">iOS</a>
</div>

このトピックでは、ASP.NET Web API により登録が実行されるときに Azure 通知ハブを使用してプッシュ通知登録を要求する方法について説明します。このトピックは、チュートリアル「[通知ハブによるユーザーへの通知][通知ハブによるユーザーへの通知]」を拡張したものです。認証されたモバイル サービスを作成するには、このチュートリアルの必要な手順を既に完了している必要があります。ユーザー通知シナリオの詳細については、「[通知ハブによるユーザーへの通知][通知ハブによるユーザーへの通知]」を参照してください。

1.  Visual Studio 2012 で、前提条件のチュートリアルである「[Notification Hubs によるユーザーへの通知][通知ハブによるユーザーへの通知]」を実行したときに作成したプロジェクトの app.xaml.cs ファイルを開きます。

2.  **InitNotificationsAsync** メソッドを見つけ、メソッドのコードをコメント解除します。

    代わりに、ASP.NET Web API を使用して通知に登録します。

3.  **ソリューション エクスプローラー**でプロジェクト名を右クリックし、**[NuGet パッケージの管理]** を選択します。

4.  左側のウィンドウで、**[オンライン]** カテゴリを選択し、`json.net` を探します。**Json.NET** パッケージで **[インストール]** をクリックし、使用許諾契約に同意します。

    ![][]

    これにより、サードパーティの Newtonsoft.Json.dll アセンブリがプロジェクトに追加されます。

5.  ソリューション エクスプローラーで、プロジェクトを右クリックし、**[追加]**、**[クラス]** の順にクリックします。次に、「`LocalStorageManager`」と入力し、**[追加]** をクリックします。

    ![][1]

    これにより、**LocalStorageManager** クラスのコード ファイルがプロジェクトに追加されます。

6.  新しい LocalStorageManager.cs プロジェクト ファイルを開き、次の **using** ステートメントを追加します。

        using Windows.Storage;

7.  **LocalStorageManager** クラス定義を、次のコードに置き換えます。

        class LocalStorageManager
        {
            private static ApplicationDataContainer GetLocalStorageContainer()
            {
                if (!ApplicationData.Current.LocalSettings
                    .Containers.ContainsKey("InstallationContainer"))
                {
                    ApplicationData.Current.LocalSettings
                        .CreateContainer("InstallationContainer",                                                            
                        ApplicationDataCreateDisposition.Always);
                }
                return ApplicationData.Current.LocalSettings
                    .Containers["InstallationContainer"];
            }

            public static string GetInstallationId()
            {
                var container = GetLocalStorageContainer();
                if (!container.Values.ContainsKey("InstallationId"))
                {
                    container.Values["InstallationId"] = Guid.NewGuid().ToString();
                }
                return (string)container.Values["InstallationId"];
            }
        }

    このコードにより、通知の作成時にタグとして使用されるデバイス固有のインストール ID が作成されて格納されます。インストール ID が存在する場合、そのインストール ID が代わりに使用されます。

8.  MainPage.xaml プロジェクト ファイルを開き、ルートの **Grid** 要素を次の XAML コードに置き換えます。

        <Grid Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
            <Grid Margin="120, 58, 120, 80" >
                <Grid.RowDefinitions>
                    <RowDefinition />
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition MaxWidth="200"/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  
                               TextWrapping="Wrap" Text="Push To Users with Notification Hubs" 
                               FontSize="42"  VerticalAlignment="Top"/>
                <TextBlock Grid.Row="1" Grid.Column="0" Text="Installation Id:" 
                               FontSize="24" VerticalAlignment="Center" />
                <TextBlock Grid.Row="1" Grid.Column="1" Name="InstId" FontSize="24" 
                               VerticalAlignment="Center"/>
                <TextBlock Grid.Row="2" Grid.Column="0" Text="Username:" FontSize="24" 
                               VerticalAlignment="Center"/>
                <TextBox Grid.Row="2" Grid.Column="1" Name="User" FontSize="24" 
                             Width="300" Height="40" HorizontalAlignment="Left"/>
                <TextBlock Grid.Row="3" Grid.Column="0" Text="Password:" FontSize="24" 
                               VerticalAlignment="Center" />
                <PasswordBox Grid.Row="3" Grid.Column="1" Name="Password" FontSize="24" 
                                 Width="300" Height="40" HorizontalAlignment="Left"/>
                <Button Name="Login" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" 
                            Content="Login and Register for Push" FontSize="24" Click="Login_Click" />
            </Grid>
        </Grid>

    このコードにより、ユーザー名とパスワードを収集してインストール ID を表示する UI が作成されます。

9.  MainPage.xaml.cs ファイルに戻り、次の **using** ディレクティブを追加します。

        using Newtonsoft.Json;
        using System.Net.Http;
        using System.Text;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

10. **MainPage** クラス内で、次の定義を追加します。

        private string registerUri = "https://<SERVICE_ROOT_URL>/api/register";

    *`<SERVICE_ROOT_URL>`* を、**「通知ハブによるユーザーへの通知」**で作成した Web API のルート URI に置き換えます。

11. **MainPage** クラス内で、次のメソッドを追加します。

        private async void Login_Click(object sender, RoutedEventArgs e)
        {
            // Get the info that we need to request registration.
            var installationId = InstId.Text = LocalStorageManager.GetInstallationId();
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();
            var user = User.Text;
            var pwd = Password.Password;

            // Define a registration to pass in the body of the POST request.
            var registration = new Dictionary<string, string>()
                                   {{"platform", "windows"},
                                   {"instId", installationId.ToString()},
                                   {"channelUri", channel.Uri}};

            // Create a client to send the HTTP registration request.
            var client = new HttpClient();
            var request =
                new HttpRequestMessage(HttpMethod.Post, new Uri(registerUri));

            // Add the Authorization header with the basic login credentials.
            var auth = "Basic " +
                Convert.ToBase64String(Encoding.UTF8.GetBytes(user + ":" + pwd));
            request.Headers.Add("Authorization", auth);
            request.Content = new StringContent(JsonConvert.SerializeObject(registration),
                Encoding.UTF8, "application/json");

            string message;

            try
            {
                // Send the registration request.
                HttpResponseMessage response = await client.SendAsync(request);

                // Get and display the response, either the registration ID
                // or an error message.
                message = await response.Content.ReadAsStringAsync();
                message = string.Format("Registration ID: {0}", message);
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display a message dialog.
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    このメソッドは、プッシュ通知のインストール ID とチャネルの両方を取得します。これらの ID とチャネルは、デバイスの種類と共に、通知ハブで登録を作成する認証済みの Web API メソッドに送信されます。この Web API は、「[通知ハブによるユーザーへの通知][通知ハブによるユーザーへの通知]」で定義されたものです。

これで、クライアント アプリケーションが更新されました。「[通知ハブによるユーザーへの通知][通知ハブによるユーザーへの通知]」に戻り、通知ハブを使用することで通知を送信するようにモバイル サービスを更新します。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows ストア C#]: /ja-jp/documentation/articles/notification-hubs-windows-store-aspnet-register-user-push-notifications/ "Windows ストア C#"
  [iOS]: /ja-jp/documentation/articles/notification-hubs-ios-aspnet-register-user-push-notifications/ "iOS"
  [通知ハブによるユーザーへの通知]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet
  []: ./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-add-nuget-package-json.png
  [1]: ./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-create-aspnet-class.png
