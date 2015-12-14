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
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/25/2015"
	ms.author="mahender"/>

# 既存の Azure モバイル サービスを App Service に移行する

このトピックでは、Azure Mobile Services の既存のアプリケーションを新しい App Service モバイル アプリに移行する方法について説明します。既存のすべてのモバイル サービスは、新しい Mobile Apps バックエンドに簡単に移行できます。移行中も、既存のモバイル サービスは機能し続けます。

App Service に移行されたモバイル サービスは、App Service のすべての機能にアクセスでき、Mobile Services の価格ではなく [App Service の価格]に従って課金されます。

スケジュールされたジョブを、Microsoft 管理対象の Azure Scheduler プランから、ユーザー独自のサブスクリプションおよび制御下の Azure Scheduler プランに移動されることにも注意してください。App Service への移行の利点と同様に、これにより、Azure Scheduler の全機能を活用できます。

### <a name="why-host"></a>App Service でホストする理由

App Service は、アプリケーションのホスト環境として、より充実した機能を備えています。App Service でホストされたサービスから、ステージング スロット、カスタム ドメイン、Traffic Manager サポートなど、豊富な機能にアクセスできます。App Service への移行後にモバイル サービスを Mobile App バックエンドにアップグレードできますが、一部の顧客は SDK の更新をすぐに実行せずに、これらの機能をすぐに利用することもできます。

App Service の利点の詳細については、[Mobile Services とApp Service] に関する記述を参照してください。

## 移行とアップグレード

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

- Node.js ベースのサーバー プロジェクトの場合、新しい [Mobile Apps Node.js SDK](https://github.com/Azure/azure-mobile-apps-node) は多くの新機能を提供します。たとえば、ローカルで開発やデバッグを行い、0.10 より後のバージョンの Node.js を使用し、Express.js ミドルウェアでカスタマイズを行うことができます。

- .NET ベースのサーバー プロジェクトの場合、新しい [Mobile Apps SDK NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)は、NuGet の依存関係に関してより高い柔軟性を備え、新しい App Service 認証機能をサポートし、MVC を含むすべての ASP.NET プロジェクトを作成します。アップグレードの詳細については、「[既存の .NET Mobile Service の App Service へのアップグレード](app-service-mobile-net-upgrading-from-mobile-services.md)」を参照してください。


## <a name="understand"></a>App Service Mobile Apps について

App Service の Mobile Apps は、Azure を使用してアプリケーションを構築する新しい方法です。Mobile Apps の詳細については、「[Mobile Apps とは]」を参照してください。

Mobile Apps に移行しても、既存のすべてのアプリ機能 (ビジネス ロジックを含む) を維持できます。さらに、新しい機能をアプリケーションで利用できます。Mobile Apps モデルでは、App Service の Web アプリ (新しいバージョンの Azure Web サイト) で、コードが実際に実行されます。Web アプリとその動作を完全に制御できます。さらに、Traffic Manager や開発スロットなど、以前は Mobile Services の顧客は使用できなかった Web アプリの機能が使用できるようになりました。

アップグレードの主な制限事項として、Mobile Services のスケジュールされたジョブが統合されないため、カスタム API を呼び出すように Azure Scheduler を設定するか、Web ジョブ機能を有効にする必要があります。

## [App Service に移行する] ウィザードを使用する Mobile Service の移行

モバイル サービスを App Service に移行する最も簡単で推奨される方法は、Azure クラシック ポータルで使用可能な [App Service に移行する] ウィザードを使用することです。[Azure クラシック ポータル]に移動し、モバイル サービスを参照して移行するモバイル サービスを選択します。画面の下部には、モバイル サービスの移行プロセス手順を示す **[App Service に移行する]** ボタンが表示されます。

### <a name="what-gets-migrated"></a>移行内容

移行ウィザードでは、モバイル サービスをホストするサーバー ファームを、Mobile Services による管理から App Service による管理に変更します。また、Mobile Services が管理していた Scheduler プランを、Microsoft 管理対象サブスクリプションからユーザーのサブスクリプションに移動します。サーバー ファームが App Service の管理制御に転送されると、サーバー ファームに関連付けられていたすべての Mobile Services が同時に移動されます。

Mobile Services は、リージョンおよび SKU (無料、基本、標準) に基づいて、サーバー ファームにモバイル サービスを編成します。リージョン内のすべての無料サービスは同じサーバー ファーム上にあり、一緒に移行されます。リージョン内のいくつかの基本サービスは同じファームで一緒にホストされますが、基本サービスが多数ある場合は、別のプランに移行されます。標準サービスはそれぞれ独自のサーバー ファーム上にあります。常に単一のサービスを移行するか、サービスが他のサイトと共に移動されないようにする場合は、基本にアップグレードできます。そうすることで、サービスは独自の独立したプランに移行されるようになります。

スケジュールされたジョブを使用していた場合、Azure Scheduler プランはサブスクリプションにも追加されます。サブスクリプションごとに許可される無料のスケジュールされたジョブ数には制限があるため、投稿サブスクリプションを確認する必要がある、これに関連付けられている一部のコストが発生する場合があります。

###  移行ウィザードの使用

1. [Azure クラシック ポータル]に移動し、[Mobile Services] をクリックします。 

2. 移行するモバイル サービスを選択してから、下部にあるメニュー バーで [App Service に移行する] をクリックします。これで、移行される Mobile Services のリストがポップアップに表示されます。表示されるサービスとその理由の詳細については、[前のセクション](#what-gets-migrated)を参照してください。

3. モバイル サービスの名前を入力し、移行を確認してから、チェックマークをクリックして続行します。移行対象として複数のモバイル サービスが含まれている場合も、選択された元のサービスの名前を入力する必要があります。

4. 移行が開始されたら、[Azure クラシック ポータル]で Mobile Services リストから現在の状態を表示することができます。現在移行中のサービスはすべて "移行中" と示されます。すべての移行が完了済みと示された場合は、[Azure ポータル]の Mobile Apps の下に表示されます。移行中および移行後も、モバイル サービスは引き続き機能し、URL は変わりません。

## Mobile Services .NET バックエンドの手動移行

>[AZURE.NOTE]前のセクションで説明されているとおり、モバイル サービスを移行する場合の推奨方法は、[App Service に移行する] ウィザードを使用することであることに注意してください。手動による移行は、ウィザードを使用できない場合にのみ使用する必要があります。

このセクションでは、Azure App Service 内での .NET Mobile Services プロジェクトのホスト方法を示します。この方法でホストされるアプリでは *Mobile Services* .NET ランタイムのチュートリアルをすべて使用できますが、azure-mobile.net ドメインを使用する URL をすべて App Service インスタンスのドメインで置き換える必要があります。

Mobile Services プロジェクトは、[App Service Environment ]でホストすることもできます。その場合も、このトピックの内容はすべて当てはまりますが、サイトの形式は `contoso.azurewebsites.net` ではなく `contoso.contosoase.p.azurewebsites.net` になります。

>[AZURE.NOTE]手動による移行を実行する場合は、既存の **service.azure-mobile.net** URL を保持することは*できません*。この URL に接続しているモバイル クライアントがある場合は、自動移行オプションを使用するか、すべてのモバイル クライアントが新しい URL にアップグレードされるまでモバイル サービスを実行しておく必要があります。


### <a name="app-settings"></a>アプリケーションの設定
Mobile Services では、いくつかのアプリケーション設定を環境内で使用できる必要があります。このセクションではそれについて説明します。

Mobile App バックエンドでアプリケーションの設定を行うには、まず、[Azure ポータル]にサインインします。Mobile App バックエンドとして使用する App Service アプリに移動し、**[設定]**、**[アプリケーション設定]** の順にクリックしてから、**[アプリケーション設定]** まで下へスクロールします。ここで、以下の必要なキーと値のペアを設定できます。

+ **MS\_MobileServiceName** は、アプリの名前に設定する必要があります。たとえば、バックエンドの URL が `contoso.azurewebsites.net` の場合は、*contoso* が適切な値です。

+ **MS\_MobileServiceDomainSuffix** は、web アプリの名前に設定する必要があります。たとえば、バックエンドの URL が `contoso.azurewebsites.net` の場合は、*azurewebsites.net* が適切な値です。

+ **MS\_ApplicationKey** は任意の値に設定できますが、クライアント SDK で使用されるのと同じ値である必要があります。GUID を使用することをお勧めします。

+ **MS\_MasterKey** は任意の値に設定できますが、管理 API/クライアントで使用されるのと同じ値である必要があります。GUID を使用することをお勧めします。

モバイル サービスを移行する場合は、マスター キーとアプリケーション キーの両方を、[Azure クラシック ポータル]の [Mobile Services] セクションにある **[構成]** タブで取得できます。下部にある **[キーの管理]** を選択し、キーをコピーします。


### <a name="client-sdk"></a>方法: クライアント SDK を変更する

クライアント アプリ プロジェクトで、新しいアプリの URL (例: `https://contoso.azurewebsites.net`) と、前に構成したアプリケーション キーを受け入れるように、Mobile Services クライアント オブジェクトのコンストラクターを変更します。クライアント SDK のバージョンは **Mobile Services** のバージョンで、アップグレードされて**いない**ものである必要があります。iOS クライアントと Android クライアントの場合は 2.x バージョンを使用し、Windows/Xamarin の場合は 1.3.2 を使用します。Javascript クライアントは 1.2.7 を使用する必要があります。

### <a name="data"></a>方法: データ機能を有効にする

Mobile Services で既定の Entity Framework クラスを使用するには、さらに 2 つのアプリケーション設定が必要です。

**[アプリケーション設定]** セクションのすぐ下にある [アプリケーション設定] ブレードの **[接続文字列]** セクションに、接続文字列が格納されています。使用するデータベースの接続文字列を **MS\_TableConnectionString** キーに設定する必要があります。既存のモバイル サービスを App Service に移行する場合は、[Azure クラシック ポータル](https://manage.windowsazure.com/)のモバイル サービスの **[構成]** タブにある **[接続文字列]** セクションに移動します。**[接続文字列の表示]** をクリックして、値をコピーします。

既定では、使用されるスキーマが **MS\_MobileServiceName** になっていますが、これは **MS\_TableSchema** 設定で上書きできます。**[アプリケーション設定]** に戻り、使用するスキーマの名前を **MS\_TableSchema** に設定します。既存の Mobile Services アプリケーションを移動する場合は、スキーマが既に Entity Framework を使用して作成されています。この名前は、現在コードをホストする App Service インスタンスではなく、モバイル サービスになっています。

### <a name="push"></a>方法: プッシュ機能を有効にする

プッシュを機能させるには、Web アプリが通知ハブに関する情報を把握する必要もあります。

**[接続文字列]** で、**MS\_NotificationHubConnectionString** に、通知ハブの DefaultFullSharedAccessSignature 接続文字列を設定します。既存のモバイル サービスを移行する場合は、[Azure クラシック ポータル](https://manage.windowsazure.com/)のモバイル サービスの **[構成]** タブにある **[接続文字列]** セクションに移動します。**[接続文字列の表示]** をクリックして、値をコピーします。

**MS\_NotificationHubName** アプリ設定は、ハブの名前に設定する必要があります。既存のモバイル サービスを移動する場合は、[Azure クラシック ポータル](https://manage.windowsazure.com/)のモバイル サービスの **[プッシュ]** タブでこの値を取得できます。このタブの他のフィールドは、ハブそのものにリンクされているため、他の場所にコピーする必要はありません。

### <a name="auth"></a>方法: 認証機能を有効にする

ID 機能には、プロバイダーごとにアプリケーション設定の要件があります。既存のモバイル サービスから移動する場合、Azure クラシック ポータルの **[ID]** タブ内の各フィールドに、対応するアプリケーション設定があります。

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

* **MS\_AadTenants** - 注: **MS\_AadTenants** はテナント ドメインのコンマ区切りリストとして格納されています (Azure クラシック ポータルの **[許可されているテナント]** フィールド)。

### <a name="publish"></a>方法: モバイル サービス プロジェクトを発行する

1. [Azure ポータル]で、アプリに移動し、コマンド バーで **[発行プロファイルの取得]** をクリックして、ダウンロードしたプロファイルをローカル コンピューターに保存します。
2. Visual Studio で、Mobile Services サーバー プロジェクトを右クリックしてから、**[発行]** をクリックします。 
3. [プロファイル] タブで、**[インポート]** を選択し、ダウンロードしたプロファイルを参照します。
3. **[発行]** をクリックして、App Service にコードをデプロイします。

標準の App Service ログ機能によってログが処理されます。ログの詳細については、「[Azure App Service の Web アプリの診断ログの有効化]」を参照してください。



<!-- URLs. -->

[Azure ポータル]: https://portal.azure.com/
[Azure クラシック ポータル]: https://manage.windowsazure.com/
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
[App Service Environment ]: app-service-app-service-environment-intro.md
[Mobile Services とApp Service]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[migrate a mobile service to a mobile app on App Service]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md

<!---HONumber=AcomDC_1203_2015-->