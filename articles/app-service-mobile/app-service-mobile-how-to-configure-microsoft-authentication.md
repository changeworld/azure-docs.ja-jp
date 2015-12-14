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
	ms.date="11/20/2015"
	ms.author="mahender"/>

# Microsoft アカウント ログインを使用するように App Service アプリケーションを構成する方法

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

このトピックでは、認証プロバイダーとして Microsoft アカウントを使用するように Azure App Services を構成する方法を示します。


> [AZURE.NOTE]このトピックでは、App Service 認証/承認の使用例を示します。ほとんどのアプリケーションで、これが App Service ゲートウェイに取って代わっています。ゲートウェイを使用する場合との相違点は、トピック全体を通してメモに記載しています。


## <a name="register"> </a>Microsoft アカウントにアプリケーションを登録する

1. [Azure ポータル]にログオンし、アプリケーションに移動します。**[URL]** をコピーします。この URL は、Microsoft アカウント アプリを構成するのに使用します。

2. Microsoft アカウント デベロッパー センターの [[マイ アプリケーション]] ページに移動し、必要に応じて、Microsoft アカウントでログオンします。

4. **[アプリケーションの作成]** をクリックし、**[アプリケーション名]** に名前を入力して、**[同意する]** をクリックします。

5. **[API 設定]** をクリックします。**[モバイル クライアント アプリ/デスクトップ クライアント アプリ]** に対して **[はい]** を選択します。**[リダイレクト URL]** フィールドに、アプリケーションの**リダイレクト URL** を入力し、**[保存]** をクリックします。リダイレクト URI は、アプリケーションの URL にパス _/.auth/login/microsoftaccount/callback_ を追加したものです。たとえば、「`https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`」のように入力します。HTTPS スキームを使用していることを確認します。

	![][0]


	> [AZURE.NOTE]App Service の認証/承認機能ではなく、App Service ゲートウェイを使用している場合、リダイレクト URL では、ゲートウェイ URL とパス _/signin-microsoft_ を使用します。


6. **[アプリ設定]** をクリックして、**[クライアント ID]** と **[クライアント シークレット]** の値を書き留めます。


    > [AZURE.NOTE]クライアント シークレットは、重要なセキュリティ資格情報です。クライアント シークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。
	

## <a name="secrets"> </a>Microsoft アカウントの情報をアプリケーションに追加する

> [AZURE.NOTE]App Service ゲートウェイを使用する場合は、このセクションの内容は無視し、ポータル内で目的のゲートウェイに移動します。**[設定]**、**[ID]**、**[Microsoft アカウント]** の順に選択します。先に取得した値を貼り付けて、**[保存]** をクリックします。


7. [Azure ポータル]に戻り、アプリケーションに移動します。**[設定]**、**[認証/承認]** の順にクリックします。

8. [認証/承認] 機能が有効になっていない場合は、スイッチを **[オン]** に切り替えます。

9. **[Microsoft アカウント]** をクリックします。前の手順で取得した App ID と App Secret の値を貼り付けます。アプリケーションで必要なスコープを有効にします (省略可能)。次に、 **[OK]** をクリックします

    ![][1]
	
	App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。アプリケーション コードでユーザーを承認する必要があります。

17. (省略可能) Microsoft によって認証されたユーザーしかサイトにアクセスできないように制限するには、**[要求が認証されていないときに実行するアクション]** を **[Microsoft アカウント]** に設定します。この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Microsoft アカウントにリダイレクトされます。

11. **[保存]** をクリックします。


これで、アプリケーションで認証に Microsoft アカウントを使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[[マイ アプリケーション]]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure ポータル]: https://portal.azure.com/

<!---HONumber=AcomDC_1203_2015-->