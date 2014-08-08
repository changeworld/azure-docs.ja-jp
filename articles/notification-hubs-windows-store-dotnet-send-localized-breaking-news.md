<properties linkid="develop-notificationhubs-tutorials-send-localized-breaking-news-windowsdotnet" urlDisplayName="ローカライズしたニュース速報" pageTitle="通知ハブ ローカライズしたニュース速報チュートリアル" metaKeywords="" description="Azure のサービス バス通知ハブを使用して、ローカライズしたニュース速報通知を送信する方法を説明します。" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="通知ハブを使用したローカライズ ニュース速報の送信" authors="ricksal" solutions="" manager="" editor="" />
# 通知ハブを使用したローカライズ ニュース速報の送信

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/ja-jp/manage/services/notification-hubs/breaking-news-localized-dotnet" title="Windows ストア C#" class="current">Windows ストア C#</a><a href="/ja-jp/manage/services/notification-hubs/breaking-news-localized-ios" title="iOS">iOS</a>
</div>

このトピックでは、Azure 通知ハブの**テンプレート**機能を使用して、言語およびデバイスごとにローカライズしたニュース速報通知をブロードキャストする方法について説明します。このチュートリアルでは、「[通知ハブを使用したニュース速報の送信]」で作成した Windows ストア アプリケーションを使用します。完了すると、興味のあるニュース速報カテゴリに登録して受信する通知の言語を指定し、選択したカテゴリのその言語のプッシュ通知だけを受信できるようになります。

このチュートリアルでは、このシナリオを有効にするための、次の基本的な手順について説明します。

1. [テンプレートの概念] 
2. [アプリケーションのユーザー インターフェイス]
3. [Windows ストア クライアント アプリケーションを構築する]
4. [バックエンドから通知を送信する]


このシナリオは次の 2 つに分けられます。

- Windows ストア アプリケーションで、クライアント デバイスが言語を指定し、さまざまなニュース速報カテゴリを購読できるようになります。

- Azure 通知ハブの**タグ**機能と**テンプレート**機能を使用して、バックエンドからブロードキャストされます。



##前提条件##

「[通知ハブを使用したニュース速報の送信]」のチュートリアルを完了し、コードが使用可能な状態になっている必要があります。このチュートリアルでは、そのコードに基づいているためです。

Visual Studio 2012 も必要です。


<h2><a name="concepts"></a><span class="short-header">概念</span>テンプレートの概念</h2>

「[通知ハブを使用したニュース速報の送信]」では、**タグ**を使用してさまざまなニュース カテゴリの通知を購読するアプリケーションを構築しました。
しかし、多くのアプリケーションは複数の市場をターゲットとしており、ローカライズが必要です。これは、通知自体の内容をローカライズし、適切なデバイス セットに配信する必要があることを意味します。
このトピックでは、通知ハブの**テンプレート**機能を使用して、ローカライズしたニュース速報通知を簡単に配信する方法について説明します。

注: ローカライズした通知を送信する 1 つの方法として、各タグの複数のバージョンを作成する方法があります。たとえば、ワールド ニュースで英語、フランス語、標準中国語をサポートするには、3 つのタグ ("world_en"、"world_fr"、"world_ch") が必要です。その後、ワールド ニュースのローカライズ バージョンをこれらの各タグに送信する必要があります。このトピックでは、テンプレートを使用することでタグの増加を抑制し、複数のメッセージを送信しなくてもよいようにします。

大まかに言えば、テンプレートとは、特定のデバイスが通知をどのように受信するかを特定するための手段です。テンプレートは、アプリケーション バックエンドにより送信されるメッセージの一部となっているプロパティを参照することで、正確なペイロードを指定します。このケースでは、サポートされるすべての言語を含む、ロケールにとらわれないメッセージを送信します。

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

次に、デバイスが、適切なプロパティを参照するテンプレートを登録するようにします。たとえば、簡単なトースト メッセージを受信する Windows ストア アプリケーションは、次のテンプレートを登録します。

	<toast>
	  <visual>
	    <binding template=\"ToastText01\">
	      <text id=\"1\">$(News_English)</text>
	    </binding>
	  </visual>
	</toast>



テンプレートは有用な機能です。詳細については、「[通知ハブの概要]」の記事を参照してください。テンプレート式言語のリファレンスは、「[方法: Azure 通知ハブ (Windows ストア アプリ)]」にあります。


<h2><a name="ui"></a><span class="short-header">アプリケーションの UI</span>アプリケーションのユーザー インターフェイス</h2>

ここでは、「[通知ハブを使用したニュース速報の送信]」で作成したニュース速報アプリケーションを変更し、テンプレートを使用してローカライズしたニュース速報を送信します。


クライアント アプリケーションがローカライズしたメッセージを受信できるようにするには、*ネイティブ*登録 (つまり、テンプレートを指定する登録) をテンプレート登録に置き換える必要があります。


Windows ストア アプリケーションで、以下の手順を実行します。

MainPage.xaml を変更してロケール コンボボックスを追加します。

	<Grid Margin="120, 58, 120, 80"  
			Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="Button_Click" />
    </Grid>

<h2><a name="building-client"></a><span class="building app">App ui</span>Building the Windows Store client app</h2>

1. In your Notifications class, add a locale parameter to your  *StoreCategoriesAndSubscribe* and *SubscribeToCateories* methods.

		public async Task StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            await SubscribeToCategories(locale, categories);
        }

        public async Task SubscribeToCategories(string locale, IEnumerable<string> categories)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            var template = String.Format(@"<toast><visual><binding template=""ToastText01""><text id=""1"">$(News_{0})</text></binding></visual></toast>", locale);

            await hub.RegisterTemplateAsync(channel.Uri, template, "newsTemplate", categories);
        }

	*RegisterNativeAsync* メソッドを呼び出す代わりに *RegisterTemplateAsync* を呼び出す点に注意してください。テンプレートがロケールに依存している特定の通知形式を登録します。さらに、テンプレートの名前 ("newsTemplate") も指定します。複数のテンプレートを登録する可能性があり (たとえば、トースト通知用のテンプレートとタイル通知用のテンプレートなど)、それらを更新または削除するには名前を付ける必要があるためです。

	同じタグを持つ複数のテンプレートがデバイスによって登録された場合、そのタグをターゲットとするメッセージが受信されると複数の通知がデバイスに配信される点に注意してください (テンプレートごとに 1 つずつ)。この動作は、同じ論理メッセージによって複数のビジュアル通知を生成する必要がある場合に役立ちます。たとえば、Windows ストア アプリケーションにバッジとトーストの両方を表示する場合などです。

2. 次のメソッドを追加して、格納されたロケールを取得します。

		public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. 次に示すように、MainPage.xaml.cs でロケール コンボボックスの現在の値を取得し、それを Notifications クラスへの呼び出しに提供することで、ボタン クリック ハンドラーを更新します。

		 var locale = (string)Locale.SelectedItem;
            
         var categories = new HashSet<string>();
         if (WorldToggle.IsOn) categories.Add("World");
         if (PoliticsToggle.IsOn) categories.Add("Politics");
         if (BusinessToggle.IsOn) categories.Add("Business");
         if (TechnologyToggle.IsOn) categories.Add("Technology");
         if (ScienceToggle.IsOn) categories.Add("Science");
         if (SportsToggle.IsOn) categories.Add("Sports");

         await ((App)Application.Current).Notifications.StoreCategoriesAndSubscribe(locale, categories);

         var dialog = new MessageDialog(String .Format("Locale: {0}; Subscribed to: {1}", locale, string.Join(",", categories)));
         dialog.Commands.Add(new UICommand("OK"));
         await dialog.ShowAsync();

4. 最後に、App.xaml.csファイルから、* OnLaunched*方式で通知シングルトンを作るために、コールを更新してください：

		Notifications.SubscribeToCategories(Notifications.RetrieveLocale(), Notifications.RetrieveCategories());


<h2><a name="send"></a><span class="short-header">ローカライズした通知を送信する</span>バックエンドからローカライズした通知を送信する</h2>

[WACOM.INCLUDE [notification-hubs-localized-back-end](../includes/notification-hubs-localized-back-end.md)]





## 次のステップ

テンプレートの使用方法の詳細については、「[通知ハブによるユーザーへの通知: ASP.NET]」、「[通知ハブによるユーザーへの通知: モバイル サービス]」、「[通知ハブの概要]」を参照してください。テンプレート式言語のリファレンスは、「[方法: Azure 通知ハブ (Windows ストア アプリ)]」にあります。

<!-- Anchors. -->
[テンプレートの概念]: #concepts
[アプリケーションのユーザー インターフェイス]: #ui
[Windows ストア クライアント アプリケーションを構築する]: #building-client
[バックエンドから通知を送信する]: #send
[次のステップ]:#next-steps

<!-- Images. -->





















<!-- URLs. -->
[モバイル サービス]: /ja-jp/develop/mobile/tutorials/get-started
[通知ハブによるユーザーへの通知: ASP.NET]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet
[通知ハブによるユーザーへの通知: モバイル サービス]: /ja-jp/manage/services/notification-hubs/notify-users
[通知ハブを使用したニュース速報の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet 

[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK に関するページ]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users (アプリケーション ユーザーへのプッシュ通知)]: /ja-jp/develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[スクリプトを使用したユーザーの認証]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js

[Azure 管理ポータル]: https://manage.windowsazure.com/
[wns オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[通知ハブの概要]: http://msdn.microsoft.com/ja-jp/library/jj927170.aspx
[方法: Azure 通知ハブ (iOS アプリ)]: http://msdn.microsoft.com/ja-jp/library/jj927168.aspx
[方法: Azure 通知ハブ (Windows ストア アプリ)]: http://msdn.microsoft.com/ja-jp/library/jj927172.aspx

