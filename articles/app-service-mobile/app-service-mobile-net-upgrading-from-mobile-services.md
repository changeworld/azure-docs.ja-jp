<properties 
	pageTitle="Mobile Services から Azure App Service にアップグレードする" 
	description="Mobile Services アプリケーションを App Service モバイル アプリに簡単にアップグレードする方法について説明します。" 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="mahender"/>

# 既存の .NET Azure Mobile Service を App Service にアップグレードする

App Service Mobile は、Microsoft Azure を使用してモバイル アプリケーションを構築する新しい方法です。詳細については、「[Mobile Apps とは]」を参照してください。

このトピックでは、既存の .NET バックエンド アプリケーションを Azure Mobile Services から新しい App Service モバイル アプリに移行する方法について説明します。このアップグレードを実行するときは、既存の Mobile Services アプリケーションの動作を続行できます。

Azure App Service にアップグレードされたモバイル バックエンドは、App Service のすべての機能にアクセスでき、Mobile Services の価格ではなく [App Service の価格]に従って課金されます。

##移行とアップグレード

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

###Mobile Apps .NET サーバー SDK の機能強化

新しい [Mobile Apps SDK NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)にアップグレードすると、次のような利点があります。

- NuGet の依存関係の柔軟性の向上。ホスティング環境で独自のバージョンの NuGet パッケージが提供されなくなったので、互換性のある代わりのバージョンを使用できます。ただし、Mobile Server SDK または依存関係に対して新しい重要なバグ修正やセキュリティ更新プログラムがある場合は、サービスを手動で更新する必要があります。

- 新しい App Service 認証機能のサポート。Web アプリとモバイル アプリの両方で共通の認証構成を使用できます。また、認証機能は専用の OWIN ミドルウェア コンポーネントに格納されるようになったので、同じ認証設定をモバイル ルートと Web ルートに使用できます。

- ASP.NET プロジェクトの他の種類とルートのサポート。MVC および Web API コントローラーをモバイル バックエンド プロジェクトと同じプロジェクトでホストできるようになりました。

- モバイル SDK の柔軟性の向上。認証、テーブル API、プッシュ登録エンドポイントなど、セットアップされる機能とルートを明示的に制御できます。詳細については、「[How to use the .NET server SDK for Azure Mobile Apps (Azure Mobile Apps に .NET サーバー SDK を使用する方法)](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup)」を参照してください。

- DI フレームワーク用のオプションの増加Mobile Services .NET サーバー SDK には autofac に対する依存関係がありましたが、Mobile Apps サーバー SDK では [Unity](https://unity.codeplex.com/) などの他のフレームワークを使用できます。

>[AZURE.NOTE]Mobile Services クライアント SDK と新しい Mobile Apps サーバー SDK の間に互換性は**ありません**。既存のモバイル サービスに接続しているモバイル クライアント アプリがある場合、すべてのモバイル クライアントが Mobile Apps **クライアント SDK** にアップグレードされるまで、サービスを動かしておく必要があります。
> 
> 既存のクライアント アプリを変更しなくても、既存のモバイル サービスを App Service に自動的に*移行*できます。移行の詳細については、「[既存の Azure Mobile Service を App Service に移行する]」を参照してください。


##<a name="overview"></a>基本的なアップグレードの概要
最も簡単なアップグレード方法は、App Service モバイル アプリの新しいインスタンスを作成することです。多くの場合、新しい Mobile Apps サーバー SDK に切り替えて、コードを新しいモバイル アプリに再発行するだけで、簡単にアップグレードできます。ただし、一部のシナリオは、高度な認証シナリオやスケジュールされたジョブの操作など、いくつか追加の構成が必要になります。次の各のセクションで、これらについて説明します。

>[AZURE.NOTE]アップグレードを開始する前に、このトピックの残りの部分を読み、よく理解しておくことをお勧めします。下に列記した機能のうち、使用する機能をすべてメモしてください。

自分のペースでコードを移行し、テストできます。モバイル アプリ バックエンドの準備ができたら、クライアント アプリケーションの新しいバージョンをリリースすることができます。この時点で、アプリケーション バックエンドの 2 つのコピーが同時に実行しています。バグの修正が両方に適用されることを確認する必要があります。最後に、ユーザーが最新のバージョンに更新したら、元のモバイル サービスを削除することができます。

このアップグレードの全手順は、次のとおりです。

1. 新しいモバイル アプリを作成して、構成する
2. 認証の問題に対処する
3. クライアント アプリケーションの新しいバージョンをリリースする
4. 元の Mobile Services インスタンスを削除する


##<a name="mobile-app-version"></a>アプリケーションのモバイル アプリ バージョンを設定する
アップグレードの最初の手順では、アプリケーションの新しいバージョンをホストする Mobile App リソースを作成します。[Microsoft Azure の管理ポータルのプレビュー]で新しいモバイル アプリを作成することができます。詳細については、[モバイル アプリの作成]に関するトピックを参照してください。

Mobile Services で使用したのと同じデータベースと通知ハブを使用することもできます。[Microsoft Azure 管理ポータル]の [Mobile Services] セクションの **[構成]** タブから次の値をコピーします。**[接続文字列]** の `MS_NotificationHubConnectionString` と `MS_TableConnectionString` をコピーします。Mobile App サイトに移動し、**[設定]**、**[アプリケーションの設定]** の順にクリックし、これらの接続文字列を **[接続文字列]** セクションに追加して、既存の値を上書きします。

Mobile Apps は、Mobile Services ランタイムとほぼ同じ機能を持つ新しい[モバイル アプリ サーバー SDK] を提供します。最初に既存のプロジェクトから Mobile Services NuGet を削除して、代わりに、サーバー SDK を含めてください。このアップグレードでは、大部分の開発者は `Microsoft.Azure.Mobile.Server.Quickstart` パッケージをダウンロードしてインストールしようと考えます。これにより、必要な設定全体を取得できるからです。このため、WebApiConfig.cs では、

    // Use this class to set configuration options for your mobile service
    ConfigOptions options = new ConfigOptions();
    
    // Use this class to set WebAPI configuration options
    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

を以下に置き換えることができます。

    HttpConfiguration config = new HttpConfiguration();

    new MobileAppConfiguration()
	    .UseDefaultConfiguration()
	    .ApplyTo(config);


>[AZURE.NOTE]新しい .NET サーバー SDK と、アプリから機能を追加/削除する方法の詳細については、「[How to use the .NET server SDK (.NET サーバー SDK の使用方法)]」トピックを参照してください。

Mobile Services と Mobile Apps SDK には他にもいくつかの変更点がありますが、簡単に対処できます。プロジェクト全体にわたって、Visual Studio を使用して、一部の using ステートメントを修正する必要が生じる場合があります。

クラス定義の直前に単にそのデコレーターを置くことで、`[MobileAppController]` 属性をすべての ApiControllers に追加する必要があります。

`[AuthorizeLevel]` 属性はなくなるため、代わりに標準 ASP.NET `[Authorize]` 属性を使用してコントローラーとメソッドを装飾する必要があります。また、`[AuthorizeLevel]` が含まれないデコレーターはアプリケーション キーによって保護されなくなることにも注意してください。これはパブリックとして扱われます。新しい SDK ではアプリケーション キーとマスター キーは使用されません。

プッシュについては、サーバー SDK に含まれていない主な項目として、PushRegistrationHandler クラスがあります。登録処理は Mobile Apps とはわずかに異なり、タグのない登録が既定で有効になっています。カスタム API を使用してタグを管理することができます。詳細については、[モバイル アプリへのプッシュ通知の追加]に関するトピックを参照してください。

スケジュールされたジョブは、Mobile Apps には組み込まれません。そのため、.NET バックエンド内にある既存のジョブは個別にアップグレードする必要があります。1 つの方法としては、スケジュールされた [Web ジョブ]をモバイル アプリ コード サイトで作成します。また、ジョブ コードを保持するコントローラーを設定し、必要なスケジュールでそのエンドポイントをヒットするように [Azure Scheduler] を構成することもできます。

`ApiServices` オブジェクトは SDK に含まれなくなりました。Mobile App の設定にアクセスするために、次の手順を使用できます。

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings(); 

同様に、ロギングは標準の ASP.NET トレース書き込みを使用して行われるようになりました。

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>認証に関する考慮事項
Mobile Apps と Mobile Services の大きな違いの 1 つは、Mobile Apps の場合、ログインはコードを実行しているサイトではなく、App Service ゲートウェイによって処理されるということです。リソース グループにそのゲートウェイがない場合、管理ポータルの Azure Mobile App に移動することでゲートウェイをプロビジョニングできます。次に **[設定]** を選択し、**[モバイル]** カテゴリの下にある **[ユーザー認証]** を選択します。**[作成]** をクリックしてゲートウェイと Mobile App を関連付けます。

この他に、ほとんどのアプリケーションでは追加のアクションは必要ありませんが、注目すべき高度なシナリオがいくつかあります。

ほとんどのアプリケーションでは、ターゲット ID プロバイダーへの新しい登録を使用できます。App Service アプリへの ID の追加の詳細については、[モバイル アプリへの認証の追加]に関するチュートリアルを参照してください。

アプリケーションでユーザー ID をデータベースに格納しているなど、ユーザー ID と依存関係がある場合は、Mobile Services のユーザー ID と App Service Mobile Apps のユーザー ID が異なることに注意する必要があります。ただし、次を使用して、App Service Mobile App アプリケーションで Mobile Services のユーザー ID を取得することができます (例として Facebook を使用)。

    MobileAppUser = (MobileAppUser) this.User;
    FacebookCredentials creds = await user.GetIdentityAsync<FacebookCredentials>();
    string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

さらに、ユーザー ID と依存関係がある場合は、可能であれば、ID プロバイダーへの同じ登録を使用することが重要です。ユーザー ID は通常、使用されたアプリケーション登録に制限されるため、新しい登録を導入した場合、ユーザーとそのデータを照合する際に問題が生じる可能性があります。何かの理由で、アプリケーションで同じ ID プロバイダー登録を使用する必要がある場合は、次の手順を使用できます。

1. アプリケーションで使用している各プロバイダーのクライアント ID とクライアント シークレット接続情報をコピーします。
2. プロバイダーごとに、追加リダイレクト URI としてゲートウェイの /signin-* エンドポイントを追加します。 

>[AZURE.NOTE]Twitter、Microsoft アカウントなどの一部のプロバイダーでは、異なるドメインのリダイレクト URI を複数指定することはできません。アプリケーションでこれらプロバイダーのいずれかを使用しており、ユーザー ID と依存関係がある場合は、現時点ではアップグレードしないことをお勧めします。

##<a name="updating-clients"></a>クライアントの更新
モバイル アプリ バックエンドを運用している場合は、クライアント アプリケーションを更新して新しいモバイル アプリを使用することができます。Mobile Apps には、新しいバージョンの Mobile Services クライアント SDK も含まれ、開発者は App Service の新しい機能を活用できるようになります。モバイル アプリ バージョンのバックエンドを使用している場合は、新しいバージョンの SDK を利用する新しいバージョンのクライアント アプリケーションをリリースすることができます。

クライアント コードに必要にな主な変更は、コンストラクターに関するものです。Mobile App サイトの URL のほかに、認証設定をホストする App Service ゲートウェイの URL を指定する必要があります。

    public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://contoso.azurewebsites.net", // URL of the Mobile App
        "https://contoso-gateway.azurewebsites.net", // URL of the App Service Gateway
        "" // Formerly app key. To be removed in future client SDK update
    );

これにより、クライアントは、モバイル アプリのコンポーネントに要求をルーティングすることができます。ターゲット プラットフォームごとの詳細については、該当する[モバイル アプリの作成]に関するトピックを参照してください。

同じ更新で、実行するプッシュ通知登録の呼び出しを調整する必要があります。次のように、登録プロセスを改善する新しい API がいくつかあります (例として、Windows を使用)。

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    await MobileService.GetPush().Register(channel.Uri); 

ターゲット プラットフォームごとの詳細については、[モバイル アプリへのプッシュ通知の追加]および[クロスプラットフォーム プッシュ通知の送信]に関するトピックを参照してください。

顧客がこれらの更新プログラムを受け取ったら、Mobile Services バージョンのアプリケーションを削除できます。これで、最新の Mobile Apps サーバー SDK を使用する App Service モバイル アプリに完全にアップグレードできました。

<!-- URLs. -->

[Microsoft Azure の管理ポータルのプレビュー]: https://portal.azure.com/
[Microsoft Azure 管理ポータル]: https://manage.windowsazure.com/
[Mobile Apps とは]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /ja-JP/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[モバイル アプリ サーバー SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[モバイル アプリの作成]: app-service-mobile-xamarin-ios-get-started.md
[モバイル アプリへのプッシュ通知の追加]: app-service-mobile-xamarin-ios-get-started-push.md
[モバイル アプリへの認証の追加]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /ja-JP/documentation/services/scheduler/
[Web ジョブ]: ../app-service-web/websites-webjobs-resources.md
[クロスプラットフォーム プッシュ通知の送信]: app-service-mobile-xamarin-ios-push-notifications-to-user.md
[How to use the .NET server SDK (.NET サーバー SDK の使用方法)]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[既存の Azure Mobile Service を App Service に移行する]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[App Service の価格]: https://azure.microsoft.com/ja-JP/pricing/details/app-service/

<!---HONumber=Nov15_HO3-->