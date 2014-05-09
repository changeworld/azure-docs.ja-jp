<properties linkid="develop-mobile-how-to-guides-import-publish-settings" urlDisplayName="サブスクリプション発行設定ファイルを Visual Studio 2013 にインポートする" pageTitle="発行設定ファイルを Visual Studio 2013 にインポートする | モバイル サービス" metaKeywords="Azure の発行設定のインポート, モバイル サービス" description="Azure モバイル サービス アプリケーションのサブスクリプション発行設定ファイルを Visual Studio 2013 にインポートする方法について説明します。" title="サブスクリプション発行設定ファイルを Visual Studio 2013 にインポートする" documentationCenter="Mobile" services="" solutions="" manager="" editor="" videoId="" scriptId="" authors="" />

# サブスクリプション発行設定ファイルを Visual Studio 2013 にインポートする

モバイル サービスを作成する前に、Azure サブスクリプションから Visual Studio に発行設定ファイルをインポートする必要があります。これにより、Visual Studio は Azure に自動的に接続できます。


1. Visual Studio 2013 で、ソリューション エクスプローラーを開き、プロジェクトを右クリックして、**[追加]**、**[接続済みサービス]** の順にクリックします。

	![接続済みサービスの追加](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2. [サービス マネージャー] ダイアログで **[サービスの作成]** をクリックし、[モバイル サービスの作成] ダイアログの **[サブスクリプション]** で [**&lt;インポート&gt;**] をクリックします。

	![VS 2013 からの新しいモバイル サービスの作成](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3. [Azure サブスクリプションのインポート] で、**[サブスクリプション ファイルのダウンロード]** をクリックし、必要であれば Azure アカウントにログインします。ブラウザーがファイルの保存を要求した場合は、**[保存]** をクリックします。

	![VS でのサブスクリプション ファイルのダウンロード](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)

	<div class="dev-callout"><strong>注</strong> <p>ログイン ウィンドウはブラウザーに表示されます。このウィンドウは、Visual Studio のウィンドウの後ろに表示されている場合があります。ダウンロードした .publishsettings ファイルの保存場所を書き留めておいてください。プロジェクトが Azure サブスクリプションに既に接続している場合は、この手順をスキップできます。</p></div>

4. **[参照]** をクリックし、.publishsettings ファイルを保存した場所に移動して、そのファイルを選択します。次に、**[開く]** をクリックして **[インポート]** をクリックします。

	![VS でのサブスクリプションのインポート](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)

	Visual Studio によって、Azure サブスクリプションへの接続に必要なデータがインポートされます。サブスクリプションに 1 つ以上の既存のモバイル サービスが含まれている場合、サービス名が表示されます。

	<div class="dev-callout"><strong>セキュリティ上の注意</strong> <p>発行設定をインポートした後、ダウンロードした .publishsettings ファイルには他のユーザーがアカウントにアクセスするために使用できる情報が含まれているので、削除することを検討してください。接続されている他のアプリケーション プロジェクトで使用するためにこのファイルを保持する場合は、セキュリティで保護してください。</p></div>

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Live Connect を使用した Windows ストア アプリへのシングル サインオン]: /ja-jp/develop/mobile/how-to-guides/register-for-single-sign-on/
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet/
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet/
[スクリプトを使用したユーザーの認証]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js/

[Azure 管理ポータル]: https://manage.windowsazure.com/

