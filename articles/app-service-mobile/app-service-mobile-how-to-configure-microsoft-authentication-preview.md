<properties
	pageTitle="App Services アプリケーションに Microsoft アカウント認証を構成する方法"
	description="App Services アプリケーションに Microsoft アカウント認証を構成する方法について説明します。"
	authors="mattchenderson" 
	services="app-service\mobile"
	documentationCenter=""
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="05/26/2015"
	ms.author="mahender"/>

# Microsoft アカウント ログインを使用するようにアプリケーションを構成する方法

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

このトピックでは、認証プロバイダーとして Microsoft アカウントを使用するように Azure App Services を構成する方法を示します。

## <a name="register"> </a>Microsoft アカウントにアプリケーションを登録する

1. Microsoft アカウント デベロッパー センターの [[マイ アプリケーション]] ページに移動し、必要に応じて、Microsoft アカウントでログオンします。

2. **[アプリケーションの作成]** をクリックし、**[アプリケーション名]** に名前を入力して、**[同意する]** をクリックします。

3. **[API 設定]** をクリックします。**[モバイル クライアント アプリ/デスクトップ クライアント アプリ]** に対して **[はい]** を選択します。**[リダイレクト URL]** フィールドに、パス _/signin-microsoft_ が末尾に追加されたゲートウェイの URL を入力します。たとえば、「`https://contosogateway.azurewebsites.net/signin-microsoft`」のように入力します。HTTPS スキームを使用していることを確認します。リダイレクト URL を入力したら、**[保存]** をクリックします。

	![][0]

	>[AZURE.NOTE]既存の Microsoft アカウント アプリケーションの登録には、まず **[強化されたリダイレクト セキュリティ]** を有効にする必要があります。

4. **[アプリ設定]** をクリックして、**[クライアント ID]** と **[クライアント シークレット]** の値を書き留めます。

    > [AZURE.NOTE]クライアント シークレットは、重要なセキュリティ資格情報です。クライアント シークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。


## <a name="secrets"> </a>Microsoft アカウントの情報をモバイル アプリに追加する

5. [Azure の管理ポータル]にログオンし、App Services ゲートウェイに移動します。

6. **[設定]** で、**[ID]**、**[Microsoft アカウント]** の順に選択します。以前に入手したアプリ ID とアプリ シークレットの値を貼り付けます。その後、**[保存]** をクリックします。

    ![][1]

これで、アプリケーションで認証に Microsoft アカウントを使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication-preview/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication-preview/app-service-microsoftaccount-settings.png

<!-- URLs. -->

[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure の管理ポータル]: https://portal.azure.com/
 

<!------HONumber=August15_HO6-->