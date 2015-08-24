<properties 
	pageTitle="発行設定ファイルを Visual Studio 2013 にインポートする | Microsoft Azure" 
	description="Visual Studio 2013 で Azure Mobile Services アプリケーションのサブスクリプション発行設定ファイルをインポートする方法について説明します。" 
	documentationCenter="" 
	services="mobile-services" 
	manager="dwrede" 
	editor="" 
	authors="ggailey777"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>

# サブスクリプション発行設定ファイルを Visual Studio 2013 にインポートする

モバイル サービスを作成する前に、Azure サブスクリプションから Visual Studio に発行設定ファイルをインポートする必要があります。これにより、Visual Studio は Azure に自動的に接続できます。

>[AZURE.NOTE]Visual Studio 2013 Update 2 から、発行設定ファイルを使用する必要はなくなりました。Visual Studio は、ユーザーが入力した資格情報を使用して直接 Azure 似接続できます。

1. Visual Studio 2013 で、ソリューション エクスプローラーを開き、プロジェクトを右クリックして、**[追加]**、**[接続済みサービス]** の順にクリックします。 

	![add connected service](./media/mobile-services-windows-how-to-import-publishsettings/mobile-add-connected-service.png)

2. [サービス マネージャー] ダイアログで [**サービスの作成**] をクリックし、[モバイル サービスの作成] ダイアログの [**サブスクリプション**] で [**インポート**] をクリックします。

	![create a new mobile service from VS 2013](./media/mobile-services-windows-how-to-import-publishsettings/mobile-create-service-from-vs2013.png)

3. [Azure サブスクリプションのインポート] で、**[サブスクリプション ファイルのダウンロード]** をクリックし、必要であれば Azure アカウントにログインします。ブラウザーがファイルの保存を要求した場合は、**[保存]** をクリックします。

	![download subscription file in VS](./media/mobile-services-windows-how-to-import-publishsettings/mobile-import-azure-subscription.png)

	> [AZURE.NOTE]ログイン ウィンドウはブラウザーに表示されます。このウィンドウは、Visual Studio のウィンドウの後ろに表示されている場合があります。ダウンロードした .publishsettings ファイルの保存場所を書き留めておいてください。プロジェクトが Azure サブスクリプションに既に接続している場合は、この手順をスキップできます。

4. **[参照]** をクリックし、.publishsettings ファイルを保存した場所に移動して、そのファイルを選択します。次に、**[開く]** をクリックして **[インポート]** をクリックします。

	![import subscription in VS](./media/mobile-services-windows-how-to-import-publishsettings/mobile-import-azure-subscription-2.png)

	Visual Studio によって、Azure サブスクリプションへの接続に必要なデータがインポートされます。サブスクリプションに 1 つ以上の既存のモバイル サービスが含まれている場合、サービス名が表示されます。

	> [AZURE.NOTE]発行設定をインポートした後、ダウンロードした .publishsettings ファイルには他のユーザーがアカウントにアクセスするために使用できる情報が含まれているので、削除することを検討してください。接続されている他のアプリケーション プロジェクトで使用するためにこのファイルを保持する場合は、セキュリティで保護してください。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Single sign-on for Windows Store apps by using Live Connect]: /develop/mobile/how-to-guides/register-for-single-sign-on/
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet/
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-users-js/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO7-->