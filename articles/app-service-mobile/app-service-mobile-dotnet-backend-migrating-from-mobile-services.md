<properties 
	pageTitle="Mobile Services から App Service モバイル アプリへの移行" 
	description="Mobile Services アプリケーションを App Service モバイル アプリに簡単に移行する方法について説明します。" 
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
	ms.date="11/04/2015" 
	ms.author="mahender"/>

# 既存の Azure Mobile Service を App Service に移行する 

このトピックでは、Azure Mobile Services の既存のアプリケーションを新しい App Service モバイル アプリに移行する方法について説明します。既存のすべての Mobile Services アプリは、新しい App Service モバイル アプリに簡単に移行できます。移行中も、既存の Mobile Services アプリケーションの動作を続行できます。

>[AZURE.NOTE]現時点では、.NET バックエンドの Mobile Services は手動での移行を使用して App Service に移行できます。Node.js と .NET 両方のターンキー移行エクスペリエンスが間もなく公開されます。ただし、手動移行を実行する場合は、既存の **service.azure-mobile.net** URL を保持*できません*。

Azure App Service に移行されたアプリは、App Service のすべての機能にアクセスでき、Mobile Services の価格ではなく [App Service の価格]に従って課金されます。

### <a name="why-host"></a>App Service でホストする理由

App Service は、アプリケーションのホスト環境として、より充実した機能を備えています。App Service でホストされたサービスから、ステージング スロット、カスタム ドメイン、Traffic Manager サポートなど、豊富な機能にアクセスできます。[既存のモバイル サービスを App Service 上のモバイル アプリに移行]できますが、SDK の更新はまだ実行せずに、これらの機能をすぐに利用することもできます。

App Service でホストする場合の主な制限事項として、Mobile Services のスケジュールされたジョブが統合されないため、カスタム API にアクセスするように Azure Scheduler を設定するか、Web ジョブ機能を有効にする必要があります。

App Service の利点の詳細については、「[Mobile Services と App Service]」を参照してください。

##移行とアップグレード

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

  - Node.js ベースのサーバー プロジェクトの場合、新しい [Mobile Apps Node.js SDK](https://github.com/Azure/azure-mobile-apps-node) は多くの新機能を提供します。たとえば、ローカルで開発やデバッグを行い、0.10 より後のバージョンの Node.js を使用し、Express.js ミドルウェアでカスタマイズを行うことができます。

  - .NET ベースのサーバー プロジェクトの場合、新しい [Mobile Apps SDK NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)は、NuGet の依存関係に関してこれまでより高い柔軟性を備え、新しい App Service 認証機能をサポートし、MVC を含むすべての ASP.NET プロジェクトを作成します。アップグレードの詳細については、「[既存の .NET Mobile Service の App Service へのアップグレード](app-service-mobile-net-upgrading-from-mobile-services.md)」を参照してください。


##<a name="understand"></a>App Service Mobile について 

App Service Mobile は、Microsoft Azure を使用してモバイル アプリケーションを構築する新しい方法です。Mobile Apps の詳細については、「[Mobile Apps とは]」を参照してください。

Mobile Apps に移行しても、すべての既存のアプリ機能 (およびコード) を維持できます。さらに、新しい機能をアプリケーションで利用できます。Mobile Apps モデルでは、Web アプリ (新しいバージョンの Azure Web サイト) で、コードが実際に実行されます。Web アプリとその動作を完全に制御できます。さらに、Traffic Manager や開発スロットなど、以前は Mobile Services の顧客は使用できなかった Web Apps の機能が使用できるようになりました。


##Mobile Services .NET バックエンドの手動移行

このセクションでは、Azure App Service 内で .NET Mobile Services プロジェクトをホストする方法について説明します。この方法でホストされたアプリに対しては、*Mobile Services* .NET ランタイムに関するすべてのチュートリアルが使用できますが、URL に azure-mobile.net ドメインが使用されている場合は、いずれも App Service インスタンスのドメインに置き換える必要があります。

Mobile Services プロジェクトは [App Service 環境]でホストすることもできます。その場合も、このトピックの内容はすべて当てはまりますが、サイトの形式は contoso.azurewebsites.net ではなく contoso.contosoase.p.azurewebsites.net になります。

>[AZURE.NOTE]手動移行を実行する場合は、既存の **service.azure-mobile.net** URL を保持*できません*。この URL に接続しているモバイル クライアントがある場合は、自動移行オプションを使用するか、またはすべてのモバイル クライアントが新しい URL にアップグレードされるまでモバイル サービスを実行しておく必要があります。


### <a name="app-settings"></a>アプリケーションの設定
Mobile Services では、いくつかのアプリケーション設定を環境内で使用できる必要があります。このセクションではそれについて説明します。

Web アプリでアプリケーションの設定を行うために、まず、[Microsoft Azure 管理ポータルのプレビュー]にサインインします。使用する Web アプリ、モバイル アプリ、または API アプリに移動し、[設定]、[アプリケーション設定] の順にクリックします。 [アプリケーション設定] という名前のセクションまで下へスクロールします。 このセクションで、必要なキーと値のペアを設定できます。
 
**MS\_MobileServiceName** にはアプリの名前を設定します。たとえば、URL が contoso.azurewebsites.net であるアプリを実行する場合は、"contoso" が適切な値です。
 
**MS\_MobileServiceDomainSuffix** には Web アプリの名前を設定します。たとえば、URL が contoso.azurewebsites.net であるアプリを実行する場合は、"azurewebsites.net" が適切な値です。
 
**MS\_ApplicationKey** には任意の値を設定できますが、クライアント SDK で使用されるのと同じ値を設定する必要があります。GUID を使用することをお勧めします。
 
**MS\_MasterKey** には任意の値を設定できますが、管理 API/クライアントで使用されるのと同じ値を設定する必要があります。GUID を使用することをお勧めします。
 
既存の Mobile Services アプリケーションを移行する場合は、マスター キーとアプリケーション キーの両方を [Microsoft Azure 管理ポータル]の Mobile Services セクションにある [構成] タブで取得できます。下部にある [キーの管理] アクションを選択し、キーをすべてコピーします。


### <a name="client-sdk"></a>方法: クライアント SDK に変更を加える

クライアント アプリ プロジェクトで、新しいアプリの URL (例: `https://contoso.azurewebsites.net`) と、前に構成したアプリケーション キーを受け入れるように、Mobile Services クライアント オブジェクトのコンストラクターを変更します。クライアント SDK のバージョンは、**Mobile Services** と同じバージョンで、アップグレードされて**いない**ものである必要があります。iOS クライアントと Android クライアントの場合は 2.x バージョンを使用し、Windows/Xamarin の場合は 1.3.2 を使用します。Javascript クライアントは 1.2.7 を使用する必要があります。

### <a name="data"></a>方法: データ機能を有効にする

Mobile Services で既定の Entity Framework クラスを使用するには、さらに 2 つのアプリケーション設定が必要です。
 
**[アプリケーション設定]** セクションのすぐ下にある [アプリケーション設定] ブレードの [接続文字列] セクションに、接続文字列が格納されています。使用するデータベースの接続文字列を **MS\_TableConnectionString** キーに設定する必要があります。既存の Mobile Services アプリケーションを移行する場合は、Mobile Services ポータルの [構成] タブにある [接続文字列] セクションに移動し、[接続文字列の表示] をクリックして、値をコピーします。
 
既定では、使用されるスキーマが **MS\_MobileServiceName** になっていますが、これは **MS\_TableSchema** 設定で上書きできます。**[アプリケーション設定]** に戻り、使用するスキーマの名前を **MS\_TableSchema** に設定します。既存の Mobile Services アプリケーションを移行する場合は、スキーマが既に、Entity Framework を使用して作成されています。この名前は、コードをホストする App Service インスタンスではなく、Mobile Services になっています。

### <a name="push"></a>方法: プッシュ機能を有効にする

プッシュを有効にするには、上記に加えて、通知ハブに関する情報を Web アプリに指定する必要があります。
 
[接続文字列] で、**MS\_NotificationHubConnectionString** に、通知ハブの DefaultFullSharedAccessSignature 接続文字列を設定します。既存の Mobile Services アプリケーションを移行する場合は、Mobile Services ポータルの [構成] タブにある [接続文字列] セクションに移動し、[接続文字列の表示] をクリックして、値をコピーします。

**MS\_NotificationHubName** アプリ設定には、ハブの名前を設定する必要があります。既存の Mobile Services アプリを移行する場合は、Mobile Services ポータルの [プッシュ] タブでこの値を取得できます。このタブの他のフィールドは、ハブそのものにリンクされているため、他の場所にコピーする必要はありません。
 
### <a name="auth"></a>方法: 認証機能を有効にする

ID 機能には、プロバイダーごとにアプリケーション設定の要件があります。既存の Mobile Services アプリを移行する場合は、Mobile Services ポータルの [ID] タブ内の各フィールドに、対応するアプリケーション設定があります。
 
Microsoft アカウント

* **MS\_MicrosoftClientID**

* **MS\_MicrosoftClientSecret**

* **MS\_MicrosoftPackageSID**
 
Facebook

* **MS\_FacebookAppID**

* **MS\_FacebookAppSecret**
 
Twitter

* **MS\_TwitterConsumerKey**

* **MS\_TwitterConsumerSecret**
 
Google

* **MS\_GoogleClientID**

* **MS\_GoogleClientSecret**
 
AAD

* **MS\_AadClientID**

* **MS\_AadTenants** - 注: **MS\_AadTenants** はテナント ドメインのコンマ区切りリストとして格納されています (Mobile Services ポータルの [許可されているテナント] フィールド)。

### <a name="publish"></a>方法: Mobile Services プロジェクトを発行する

1. プレビュー ポータルで、アプリに移動し、コマンド バーから [発行プロファイルの取得] を選択します。ダウンロードしたプロファイルをローカル コンピューターに保存します。
2. Visual Studio で、Mobile Services サーバー プロジェクトを右クリックし、[発行] を選択します。 [プロファイル] タブで、[インポート] を選択し、ダウンロードしたプロファイルを参照します。
3. [発行] をクリックして、App Service にコードをデプロイします。

標準の App Service ログ機能によってログが処理されます。ログ機能の詳細については、「[Azure App Service の Web アプリの診断ログの有効化]」を参照してください。



<!-- URLs. -->

[Microsoft Azure 管理ポータルのプレビュー]: https://portal.azure.com/
[Microsoft Azure 管理ポータル]: https://manage.windowsazure.com/
[Mobile Apps とは]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /ja-JP/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /ja-JP/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[Send cross-platform push notifications]: app-service-mobile-xamarin-ios-push-notifications-to-user.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md


[Azure App Service の Web アプリの診断ログの有効化]: web-sites-enable-diagnostic-log.md
[App Service の価格]: https://azure.microsoft.com/ja-JP/pricing/details/app-service/
[App Service 環境]: app-service-app-service-environment-intro.md
[Mobile Services と App Service]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[既存のモバイル サービスを App Service 上のモバイル アプリに移行]: app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md

<!---HONumber=Nov15_HO3-->