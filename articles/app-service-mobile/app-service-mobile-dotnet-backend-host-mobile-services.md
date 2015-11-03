<properties
	pageTitle="App Service で Mobile Services プロジェクトをホストする | Microsoft Azure"
	description="App Service 内で Mobile Services プロジェクトを実行する方法について説明します。"
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor="na"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.date="10/08/2015"
	ms.author="mahender"/>

# App Service で .NET Mobile Services プロジェクトをホストする方法

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

このトピックでは、Azure App Service 内で .NET Mobile Services プロジェクトをホストする方法について説明します。この方法で実行するアプリは、App Service のすべての機能にアクセスでき、Mobile Services の価格ではなく [App Service の価格]に従って課金されます。

この方法でホストされたアプリに対しては、Mobile Services .NET ランタイムに関するすべてのチュートリアルが使用できますが、URL に azure-mobile.net ドメインが使用されている場合は、いずれも App Service インスタンスのドメインに置き換える必要があります。

Mobile Services プロジェクトは [App Service 環境]でホストすることもできます。その場合も、このトピックの内容はすべて当てはまりますが、サイトの形式は contoso.azurewebsites.net ではなく contoso.contosoase.p.azurewebsites.net になります。

## <a name="app-settings"></a>App Service でホストする理由

App Service は、アプリケーションのホスト環境として、より充実した機能を備えています。App Service でホストされたサービスから、ステージング スロット、カスタム ドメイン、Traffic Manager サポートなど、豊富な機能にアクセスできます。[既存のモバイル サービスを App Service 上のモバイル アプリに移行]できますが、SDK の更新はまだ実行せずに、これらの機能をすぐに利用することもできます。

App Service でホストする場合の主な制限事項として、Mobile Services のスケジュールされたジョブが統合されないため、カスタム API にアクセスするように Azure Scheduler を設定するか、Web ジョブ機能を有効にする必要があります。

App Service の利点の詳細については、[Mobile Services と App Service の比較]に関するトピックを参照してください。

## <a name="app-settings"></a>アプリケーションの設定
Mobile Services では、いくつかのアプリケーション設定を環境内で使用できる必要があります。このセクションでは、これらの各設定を追加する方法について説明します。Web アプリでアプリケーションの設定を行うために、まず、[Microsoft Azure 管理ポータルのプレビュー]にサインインします。使用する Web アプリ、モバイル アプリ、または API アプリに移動し、[設定]、[アプリケーション設定] の順にクリックします。 [アプリケーション設定] という名前のセクションまで下へスクロールします。 このセクションで、必要なキーと値のペアを設定できます。
 
**MS\_MobileServiceName** にはアプリの名前を設定します。たとえば、URL が contoso.azurewebsites.net であるアプリを実行する場合は、"contoso" が適切な値です。
 
**MS\_MobileServiceDomainSuffix** には Web アプリの名前を設定します。たとえば、URL が contoso.azurewebsites.net であるアプリを実行する場合は、"azurewebsites.net" が適切な値です。
 
**MS\_ApplicationKey** には任意の値を設定できますが、クライアント SDK で使用されるのと同じ値を設定する必要があります。GUID を使用することをお勧めします。
 
**MS\_MasterKey** には任意の値を設定できますが、管理 API/クライアントで使用されるのと同じ値を設定する必要があります。GUID を使用することをお勧めします。
 
既存の Mobile Services アプリケーションを移行する場合は、マスター キーとアプリケーション キーの両方を [Azure 管理ポータル]の Mobile Services セクションにある [構成] タブで取得できます。下部にある [キーの管理] アクションを選択し、キーをすべてコピーします。


## <a name="client-sdk"></a>方法: クライアント SDK に変更を加える

クライアント アプリ プロジェクトで、アプリの URL (contoso.azurewebsites.net など) と、前に構成したアプリケーション キーを受け入れるように、Mobile Services クライアント オブジェクトのコンストラクターに変更を加えます。クライアント SDK のバージョンは、Mobile Services と同じバージョンで、アップグレードされて**いない**ものである必要があります。iOS クライアントと Android クライアントの場合は 2.x バージョンを使用し、Windows/Xamarin の場合は 1.3.2 を使用します。Javascript クライアントは 1.2.7 を使用する必要があります。

## <a name="data"></a>方法: データ機能を有効にする

Mobile Services で既定の Entity Framework クラスを使用するには、さらに 2 つのアプリケーション設定が必要です。
 
[アプリケーション設定] セクションのすぐ下にある [アプリケーション設定] ブレードの [接続文字列] セクションに、接続文字列が格納されています。使用するデータベースの接続文字列を **MS\_TableConnectionString** キーに設定する必要があります。既存の Mobile Services アプリケーションを移行する場合は、Mobile Services ポータルの [構成] タブにある [接続文字列] セクションに移動し、[接続文字列の表示] をクリックして、値をコピーします。
 
既定では、使用されるスキーマが **MS\_MobileServiceName** になっていますが、これは **MS\_TableSchema** 設定で上書きできます。[アプリケーション設定] に戻り、使用するスキーマの名前を **MS\_TableSchema** に設定します。既存の Mobile Services アプリケーションを移行する場合は、スキーマが既に、Entity Framework を使用して作成されています。この名前は、コードをホストする App Service インスタンスではなく、Mobile Services になっています。

## <a name="push"></a>方法: プッシュ機能を有効にする

プッシュを有効にするには、上記に加えて、通知ハブに関する情報を Web アプリに指定する必要があります。
 
[接続文字列] で、**MS\_NotificationHubConnectionString** に、通知ハブの DefaultFullSharedAccessSignature 接続文字列を設定します。既存の Mobile Services アプリケーションを移行する場合は、Mobile Services ポータルの [構成] タブにある [接続文字列] セクションに移動し、[接続文字列の表示] をクリックして、値をコピーします。

**MS\_NotificationHubName** アプリ設定には、ハブの名前を設定する必要があります。既存の Mobile Services アプリを移行する場合は、Mobile Services ポータルの [プッシュ] タブでこの値を取得できます。このタブの他のフィールドは、ハブそのものにリンクされているため、他の場所にコピーする必要はありません。
 
## <a name="auth"></a>方法: 認証機能を有効にする

ID 機能には、プロバイダーごとにアプリケーション設定の要件があります。既存の Mobile Services アプリを移行する場合は、Mobile Services ポータルの [ID] タブ内の各フィールドに、対応するアプリケーション設定があります。
 
Microsoft アカウント: * **MS\_MicrosoftClientID** * **MS\_MicrosoftClientSecret** * **MS\_MicrosoftPackageSID**
 
Facebook: * **MS\_FacebookAppID** * **MS\_FacebookAppSecret**
 
Twitter * **MS\_TwitterConsumerKey** * **MS\_TwitterConsumerSecret**
 
Google * **MS\_GoogleClientID** * **MS\_GoogleClientSecret**
 
AAD * **MS\_AadClientID** * **MS\_AadTenants** - 注 **MS\_AadTenants** はテナント ドメインのコンマ区切りリストとして格納されています (Mobile Services ポータルの [許可されているテナント] フィールド)。

## <a name="publish"></a>方法: Mobile Services プロジェクトを発行する

1. プレビュー ポータルで、アプリに移動し、コマンド バーから [発行プロファイルの取得] を選択します。ダウンロードしたプロファイルをローカル コンピューターに保存します。
2. Visual Studio で、Mobile Services サーバー プロジェクトを右クリックし、[発行] を選択します。 [プロファイル] タブで、[インポート] を選択し、ダウンロードしたプロファイルを参照します。
3. [発行] をクリックして、App Service にコードをデプロイします。

標準の App Service ログ機能によってログが処理されます。ログ機能の詳細については、[Azure App Service の診断ログの有効化]に関するページを参照してください。

<!-- URLs. -->

[Microsoft Azure 管理ポータルのプレビュー]: https://portal.azure.com/
[Azure 管理ポータル]: https://manage.windowsazure.com/
[Azure App Service の診断ログの有効化]: web-sites-enable-diagnostic-log.md
[App Service の価格]: https://azure.microsoft.com/ja-JP/pricing/details/app-service/
[App Service 環境]: app-service-app-service-environment-intro.md
[Mobile Services と App Service の比較]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[既存のモバイル サービスを App Service 上のモバイル アプリに移行]: app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md

<!---HONumber=Nov15_HO1-->