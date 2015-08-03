<properties 
	pageTitle="App Services アプリケーションに Azure Active Directory 認証を構成する方法" 
	description="App Services アプリケーションに Azure Active Directory 認証を構成する方法について説明します。" 
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

# Azure Active Directory ログインを使用するようにアプリケーションを構成する方法

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

このトピックでは、認証プロバイダーとして Azure Active Directory を使用するように Azure App Services を構成する方法を示します。

## <a name="register"> </a>Azure Active Directory にアプリケーションを登録する

1. [プレビュー段階の Azure の管理ポータル]にログオンし、App Service ゲートウェイに移動します。

2. **[設定]** で、**[ID]**、**[Azure Active Directory]** の順に選択します。**[APP URL]** をコピーします。HTTPS スキームを使用していることを確認します。

    ![][1]

3. [Azure の管理ポータル]にサインインして、**Active Directory** に移動します。

    ![][2]

4. ディレクトリを選択し、上部の **[アプリケーション]** タブを選択します。下部の **[追加]** をクリックして、新しいアプリの登録を作成します。

5. **[組織で開発中のアプリケーションを追加]** をクリックします。

6. アプリケーションの追加ウィザードで、アプリケーションの**名前**を入力し、種類として **[Web アプリケーションや Web API]** をクリックします。その後、クリックして続行します。

7. **[サインオン URL]** ボックスで、ゲートウェイの Active Directory ID プロバイダーの設定からコピーしたアプリ ID を貼り付けます。**[アプリケーション ID/URI]** ボックスに同じ一意のリソース識別子を入力します。その後、クリックして続行します。

8. アプリケーションが追加されたら **[構成]** タブをクリックします。**[シングル サインオン]** の **[応答 URL]** を編集して、ゲートウェイの URL の末尾にパス _/signin-aad_ を追加します。たとえば、「`https://contosogateway.azurewebsites.net/signin-aad`」のように入力します。HTTPS スキームを使用していることを確認します。

    ![][3]

9. **[保存]** をクリックします。その後、アプリケーションの**クライアント ID** をコピーします。

## <a name="secrets"> </a>Azure Active Directory の情報をモバイル アプリに追加する

10. プレビュー管理ポータルに戻り、ゲートウェイの **[ユーザー認証]** ブレードに移動します。Azure Active Directory ID プロバイダーの **[クライアント ID]** 設定に貼り付けます。
  
11. **[許可されたテナント]** リストで、アプリケーションを登録したディレクトリのドメインを追加する必要があります (contoso.onmicrosoft.com など)。既定のドメイン名は、Azure Active Directory テナントで **[ドメイン]** タブをクリックすると見つかります。ドメイン名を **[許可されたテナント]** リストに追加して **[保存]** をクリックします。

これで、アプリケーションで認証に Azure Active Directory を使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

Azure Active Directory シングル サインオンでモバイル アプリのユーザーを認証する: [iOS][ios-adal]

<!-- Images. -->

[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-aad-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-aad-app-configure.png

<!-- URLs. -->

[プレビュー段階の Azure の管理ポータル]: https://portal.azure.com/
[Azure の管理ポータル]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-dotnet-backend-xamarin-ios-aad-sso-preview.md
 

<!---HONumber=July15_HO4-->