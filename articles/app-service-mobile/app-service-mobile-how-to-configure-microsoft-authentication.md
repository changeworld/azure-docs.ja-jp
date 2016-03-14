<properties
	pageTitle="App Services アプリケーションに Microsoft アカウント認証を構成する方法"
	description="App Services アプリケーションに Microsoft アカウント認証を構成する方法について説明します。"
	authors="mattchenderson"
	services="app-service"
	documentationCenter=""
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/24/2016"
	ms.author="mahender"/>

# Microsoft アカウント ログインを使用するように App Service アプリケーションを構成する方法

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

このトピックでは、認証プロバイダーとして Microsoft アカウントを使用するように Azure App Services を構成する方法を示します。

## <a name="register-windows-dev-center"> </a>Windows デベロッパー センターで Windows アプリを登録する

Windows デベロッパー センターを通じてユニバーサル Windows アプリと Windows ストア アプリを登録する必要があります。これにより、アプリのプッシュ通知を今後より簡単に構成できます。

>[AZURE.NOTE]Windows Phone 8、Windows Phone 8.1 Silverlight、およびその他すべての Windows アプリについては、このセクションをスキップします。Windows アプリに対してプッシュ通知を構成済みの場合は、このセクションもスキップできます。

1. [Azure ポータル]にログオンし、アプリケーションに移動します。**[URL]** をコピーします。この URL は、Microsoft アカウントでアプリを構成するのに使用します。

2. アプリケーションをまだ登録していない場合は、[Windows デベロッパー センター](https://dev.windows.com/dashboard/Application/New)に移動して Microsoft アカウントでログインし、アプリの名前を入力し、可用性を確認して **[アプリ名の予約]** をクリックします。

3. Visual Studio で Windows アプリ プロジェクトを開き、ソリューション エクスプローラーで Windows Store アプリ プロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

  	![](./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-windows-store-association.png)

4. ウィザードで、**[サインイン]** をクリックしてから Microsoft アカウントでサインインし、予約したアプリケーションを選択して **[次へ]**、**[関連付け]** の順にクリックします。ユニバーサル Windows 8.1 アプリケーションの場合、Windows Phone ストア プロジェクトの手順 4 および 5 を繰り返す必要があります。

6. 新しいアプリケーションの Windows デベロッパー センター ページに戻り、**[サービス]**、**[プッシュ通知]** の順にクリックします。

7. **[プッシュ通知]** ページで、**[Windows プッシュ通知サービス (WNS) と Microsoft Azure Mobile Services]** の下にある **[ライブ サービス サイト]** をクリックします。

次に、Windows アプリに Microsoft アカウント認証を構成します。


## <a name="register-microsoft-account"> </a>Microsoft アカウントにアプリを登録する

前のセクションで、Windows アプリが既に登録されている場合は、手順 4 にスキップできます。

1. [Azure ポータル]にログオンし、アプリケーションに移動します。**[URL]** をコピーします。この URL は、Microsoft アカウントでアプリを構成するのに使用します。

2. Microsoft アカウント デベロッパー センターの [マイ アプリケーション] ページに移動し、必要に応じて、Microsoft アカウントでログオンします。

3. **[アプリケーションの作成]** をクリックし、**[アプリケーション名]** に名前を入力して、**[同意する]** をクリックします。

4. **[API 設定]** をクリックし、**[モバイル クライアント アプリ/デスクトップ クライアント アプリ]** で **[はい]** を選択し、 アプリケーションで **[リダイレクト URL]** を指定してから、 **[保存]** をクリックします。
 
	![][0]

	>[AZURE.NOTE] リダイレクト URI は、アプリケーションの URL にパス _/.auth/login/microsoftaccount/callback_ を追加したものです。たとえば、「`https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`」のように入力します。HTTPS スキームを使用していることを確認します。

6. **[アプリ設定]** をクリックして、**[クライアント ID]** と **[クライアント シークレット]** の値を書き留めます。

    > [AZURE.IMPORTANT] クライアント シークレットは、重要なセキュリティ資格情報です。クライアント シークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。

## <a name="secrets"> </a>Microsoft アカウントの情報を App Service アプリケーションに追加する

1. [Azure ポータル] に戻り、 アプリケーションに移動して、 **[設定]**、**[認証/承認]** の順にクリックします。

2. [認証/承認] 機能が有効になっていない場合は、スイッチを **[オン]** に切り替えます。

3. **[Microsoft アカウント]** をクリックします。前の手順で取得した App ID と App Secret の値を貼り付けます。アプリケーションで必要なスコープを有効にします (省略可能)。次に、 **[OK]** をクリックします

    ![][1]

	App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。アプリケーション コードでユーザーを承認する必要があります。

4. (省略可能) Microsoft によって認証されたユーザーしかサイトにアクセスできないように制限するには、**[要求が認証されていないときに実行するアクション]** を **[Microsoft アカウント]** に設定します。この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Microsoft アカウントにリダイレクトされます。

5. **[保存]** をクリックします。

これで、アプリケーションで認証に Microsoft アカウントを使用する準備ができました。

## <a name="related-content"></a>関連コンテンツ

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure ポータル]: https://portal.azure.com/

<!---HONumber=AcomDC_0302_2016-->