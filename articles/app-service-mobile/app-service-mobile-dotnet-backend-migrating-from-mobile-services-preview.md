<properties 
	pageTitle="Mobile Services から App Service モバイル アプリへの移行" 
	description="Mobile Services アプリケーションを App Service モバイル アプリに簡単に移行する方法について説明します。" 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/23/2015" 
	ms.author="mahender"/>

# 既存の Azure Mobile Service を Azure App Service モバイル アプリに移行する

このトピックでは、Azure Mobile Services の既存のアプリケーションを新しい App Service モバイル アプリに移行する方法について説明します。既存のすべての Mobile Services アプリは、新しい App Service モバイル アプリに簡単に移行できます。移行中も、既存の Mobile Services アプリケーションの動作を続行できます。時間の経過と共に、移行プロセスはさらに簡単になりますが、今すぐ移行する場合は、次の手順を使用することができます。

>[AZURE.NOTE]現在、移行がサポートされているのは、Mobile Services .NET バックエンドを使用しているユーザーのみです。Node.JS バックエンドを使用するアプリケーションは、現時点では Mobile Services のままにする必要があります。

##<a name="understand"></a>App Service Mobile Apps について

App Service Mobile Apps は、Microsoft Azure を使用してモバイル アプリケーションを構築する新しい方法です。モバイル アプリの詳細については、「[モバイル アプリとは]」を参照してください。

モバイル アプリに移行しても、すべての既存のアプリ機能 (およびコード) を維持できます。さらに、新しい機能をアプリケーションで利用できます。モバイル アプリ モデルでは、Web アプリ (新しいバージョンの Azure Web サイト) で、コードが実際に実行されます。Web アプリとその動作を完全に制御できます。さらに、Traffic Manager や開発スロットなど、以前は Mobile Services の顧客は使用できなかった Web アプリの機能が使用できるようになりました。

新しいモデルは、Mobile Services の操作における主な問題の 1 つにも対処します。現在は、依存関係の競合を気にせずに、任意のバージョンの NuGet パッケージをデプロイできます。移行の利点の詳細については、[Web サイトと Mobile Services を使用している場合に App Service を使用する利点]に関するトピックを参照してください。

App Service モバイル アプリを作成すると、以下が実現されます。

- 新しい機能が含まれるモバイル アプリ リソース 
- モバイル アプリ サーバー SDK を使用して Web API プロジェクトを実行するモバイル アプリ コード サイト
- ログインを処理し、App Service API Apps をアプリケーションに追加できる App Service ゲートウェイ

##<a name="overview"></a>基本的な移行の概要
移行の最も簡単な方法は、App Service モバイル アプリの新しいインスタンスを作成することです。多くの場合、移行は、新しいサーバー SDK に切り替え、コードを新しいモバイル アプリに再発行するのと同じくらい簡単です。ただし、一部のシナリオは、高度な認証シナリオやスケジュールされたジョブの操作など、いくつか追加の構成が必要になります。次の各のセクションで、これらについて説明します。

>[AZURE.NOTE]移行を開始する前に、このトピックの残りの部分を読み、よく理解しておくことをお勧めします。下に列記した機能のうち、使用する機能をすべてメモしてください。

自分のペースでコードを移行し、テストできます。モバイル アプリ バックエンドの準備ができたら、クライアント アプリケーションの新しいバージョンをリリースすることができます。この時点で、アプリケーションの 2 つのコピーが同時に実行しています。バグの修正が両方に適用されることを確認する必要があります。最後に、ユーザーが最新のバージョンに更新したら、元のモバイル サービスを削除することができます。

この移行の全手順は、次のとおりです。

1. 新しいモバイル アプリを作成して、構成する
2. 認証の問題に対処する
3. クライアント アプリケーションの新しいバージョンをリリースする
4. 元の Mobile Services インスタンスを削除する


##<a name="mobile-app-version"></a>アプリケーションのモバイル アプリ バージョンを設定する
移行の最初の手順では、アプリケーションの新しいバージョンをホストする App Service を作成します。[Azure の管理ポータルのプレビュー]で新しいモバイル アプリを作成することができます。詳細については、[モバイル アプリの作成]に関するトピックを参照してください。

Mobile Services で使用したのと同じデータベースと通知ハブを使用することもできます。[Azure の管理ポータル]の [Mobile Services] セクションの **[構成]** タブから次の値をコピーします。**[接続文字列]** の `MS_NotificationHubConnectionString` と `MS_TableConnectionString` をコピーします。モバイル アプリ コード サイトに移動し、**[設定]**、**[アプリケーションの設定]** の順にクリックします。これらの接続文字列を追加して、既存の値を上書きします。同様に、これらの値をモバイル アプリ リソースに追加する必要があります。そのためには、[モバイル アプリ] ブレードに移動し、**[設定]**、**[プロパティ]** の順にクリックします。**[API アプリ ホスト]** というラベルの付いたリンクをクリックして、モバイル アプリ リソースをホストしているサイトを表示します。**[設定]**、**[アプリケーションの設定]** の順にクリックして、コード サイトと同様に、接続文字列に貼り付けます。その他の値は変更しないでください。モバイル アプリの機能を損なうことになります。この時点では、この構成手順の後も、[モバイル アプリ] ブレードには既存の接続が引き続き表示されることに注意してください。モバイル アプリ エクスペリエンスが更新されると、追加の操作が必要になる場合があります。

Mobile Apps は、Mobile Services ランタイムとほぼ同じ機能を持つ新しい[モバイル アプリ サーバー SDK] を提供します。既存のプロジェクトから Mobile Services NuGet を削除して、代わりに、サーバー SDK を含めてください。Visual Studio を使用して、一部の using ステートメントを修正することが必要になる場合があります。サーバー SDK に含まれていない主な項目として、PushRegistrationHandler クラスがあります。登録処理はモバイル アプリとはわずかに異なり、タグのない登録が既定で有効になっています。カスタム API を使用してタグを管理することができます。詳細については、[モバイル アプリへのプッシュ通知の追加]に関するトピックを参照してください。

スケジュールされたジョブは、モバイル アプリには組み込まれません。そのため、.NET バックエンド内にある既存のジョブは個別に移行する必要があります。1 つの方法としては、スケジュールされた [Web ジョブ]をモバイル アプリ コード サイトで作成します。また、ジョブ コードを保持するコントローラーを設定し、必要なスケジュールでそのエンドポイントをヒットするように [Azure Scheduler] を構成することもできます。


##<a name="authentication"></a>認証に関する考慮事項
モバイル アプリと Mobile Services の最も大きな違いの 1 つは、モバイル アプリの場合、ログインは、コード サイトではなく、App Service ゲートウェイによって処理されるということです。ほとんどのアプリケーションでは、そのための追加のアクションは必要ありませんが、注目すべき高度なシナリオがいくつかあります。

ほとんどのアプリケーションでは、ターゲット ID プロバイダーへの新しい登録を使用できます。App Service アプリへの ID の追加の詳細については、[モバイル アプリへの認証の追加]に関するチュートリアルを参照してください。

アプリケーションでユーザー ID をデータベースに格納しているなど、ユーザー ID と依存関係がある場合は、Mobile Services のユーザー ID と App Service Mobile Apps のユーザー ID が異なることに注意する必要があります。ただし、次を使用して、App Service Mobile App アプリケーションで Mobile Services のユーザー ID を取得することができます (例として Facebook を使用)。

    ServiceUser user = (ServiceUser) this.User;
    FacebookCredentials creds = (await user.GetIdentitiesAsync()).OfType< FacebookCredentials >().FirstOrDefault();
    string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

さらに、ユーザー ID と依存関係がある場合は、可能であれば、ID プロバイダーへの同じ登録を使用することが重要です。ユーザー ID は通常、使用されたアプリケーション登録に制限されるため、新しい登録を導入した場合、ユーザーとそのデータを照合する際に問題が生じる可能性があります。何かの理由で、アプリケーションで同じ ID プロバイダー登録を使用する必要がある場合は、次の手順を使用できます。

1. アプリケーションで使用している各プロバイダーのクライアント ID とクライアント シークレット接続情報をコピーします。
2. プロバイダーごとに、追加リダイレクト URI としてゲートウェイの /signin-* エンドポイントを追加します。 

>[AZURE.NOTE]Twitter、Microsoft アカウントなどの一部のプロバイダーでは、異なるドメインのリダイレクト URI を複数指定することはできません。アプリケーションでこれらプロバイダーのいずれかを使用しており、ユーザー ID と依存関係がある場合は、現時点では移行しないことをお勧めします。

##<a name="updating clients"></a>クライアントの更新
モバイル アプリ バックエンドを運用している場合は、クライアント アプリケーションを更新して新しいモバイル アプリを使用することができます。モバイル アプリには、新しいバージョンの Mobile Services クライアント SDK も含まれ、開発者は App Service の新しい機能を活用できるようになります。モバイル アプリ バージョンのバックエンドを使用している場合は、新しいバージョンの SDK を利用する新しいバージョンのクライアント アプリケーションをリリースすることができます。

クライアント コードに必要にな主な変更は、コンストラクターに関するものです。モバイル アプリ コード サイトの URL とアプリケーション キーのほかに、認証設定をホストする App Service ゲートウェイの URL を指定する必要があります。

    public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://contoso-code.azurewebsites.net", //URL of the Mobile App Code
        "https://contosogateway.azurewebsites.net", //URL of the App Service Gateway
        "983682c4-f043-483e-a75b-8a8545fc1846"
    );

これにより、クライアントは、モバイル アプリのコンポーネントに要求をルーティングすることができます。ターゲット プラットフォームごとの詳細については、該当する[モバイル アプリの作成]に関するトピックを参照してください。

同じ更新で、実行するプッシュ通知登録の呼び出しを調整する必要があります。次のように、登録プロセスを改善する新しい API がいくつかあります (例として、Windows を使用)。

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    await MobileService.GetPush().Register(channel.Uri); 

ターゲット プラットフォームごとの詳細については、[モバイル アプリへのプッシュ通知の追加]および[クロスプラットフォーム プッシュ通知の送信]に関するトピックを参照してください。

顧客がこれらの更新プログラムを受け取ったら、Mobile Services バージョンのアプリケーションを削除できます。この時点で、App Service Mobile App への移行が完了しました。

<!-- URLs. -->

[Azure の管理ポータルのプレビュー]: https://portal.azure.com/
[Azure の管理ポータル]: https://manage.windowsazure.com/
[モバイル アプリとは]: app-service-mobile-value-prop-preview.md
[Web サイトと Mobile Services を使用している場合に App Service を使用する利点]: /ja-jp/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services-preview
[モバイル アプリ サーバー SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[モバイル アプリの作成]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md
[モバイル アプリへのプッシュ通知の追加]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md
[モバイル アプリへの認証の追加]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md
[Azure Scheduler]: /ja-jp/documentation/services/scheduler/
[Web ジョブ]: ../app-service-web/websites-webjobs-resources.md
[クロスプラットフォーム プッシュ通知の送信]: app-service-mobile-dotnet-backend-xamarin-ios-push-notifications-to-user-preview.md

<!---HONumber=July15_HO4-->