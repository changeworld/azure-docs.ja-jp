<properties linkid="develop-mobile-how-to-guides-register-for-active-directory-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Mobile Services application." title="Register your account to use an Azure Active Directory account login" authors="wesmc" services="mobile-services" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# アプリケーションを登録して Azure Active Directory アカウント ログインを使用する

このトピックでは、Azure モバイル サービスの認証プロバイダーとして Azure Active Directory を使用できるようにアプリケーションを登録する方法について説明します。

> [WACOM.NOTE] Azure Active Directory のシングル サインオン (SSO) でクライアントに基づく認証を提供する場合は、[Active Directory 認証ライブラリのシングル サインオンによるアプリケーションの認証][Active Directory 認証ライブラリのシングル サインオンによるアプリケーションの認証]チュートリアルを参照してください。

1.  [Azure の管理ポータル][Azure の管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、目的のモバイル サービスをクリックします。

    ![][0]

2.  モバイル サービスの **[ID]** タブをクリックします。

    ![][1]

3.  **Azure Active Directory** ID プロバイダーのセクションまで下にスクロールし、そこに表示されている**アプリケーション URL** をコピーします。

    ![][2]

4.  管理ポータルの **[Active Directory]** に移動し、目的のディレクトリをクリックします。

    ![][3]

5.  上部にある **[アプリケーション]** タブをクリックし、アプリケーションの **[追加]** をクリックします。

    ![][4]

6.  **[組織で開発中のアプリケーションを追加]** をクリックします。

7.  アプリケーションの追加ウィザードで、アプリケーションの**名前**を入力し、種類として **[Web アプリケーションや Web API]** をクリックします。その後、クリックして続行します。

    ![][5]

8.  **[サインオン URL]** ボックスで、モバイル サービスの Active Directory ID プロバイダーの設定からコピーしたアプリケーション ID を貼り付けます。**[アプリケーション ID/URI]** ボックスに同じ一意のリソース識別子を入力します。その後、クリックして続行します。

    ![][6]

9.  アプリケーションが追加されたら **[構成]** タブをクリックします。その後、アプリケーションの**クライアント ID** をコピーします。

    モバイル サービスに .Net バックエンドを使用するモバイル サービスを作成した場合、**[シングル サインオン]** の **[応答 URL]** を編集して、モバイル サービスの URL の末尾のパスが *signin-aad* になるようにします。たとえば、`https://todolist.azure-mobile.net/signin-aad` のように指定します。

    ![][7]

10. モバイル サービスの **[ID]** タブに戻ります。1 番下で、Azure Active Directory ID プロバイダーの **[クライアント ID]** 設定に貼り付けます。

11. **[許可されたテナント]** リストで、アプリケーションを登録したディレクトリのドメインを追加する必要があります (contoso.onmicrosoft.com など)。デフォルトのドメイン名は Active Directory の **[ドメイン]** タブをクリックして検索できます。

    ![][8]

    ドメイン名を **[許可されたテナント]** リストに追加して **[保存]** をクリックします。

    ![][9]

これで、アプリケーションで認証に Azure Active Directory を使用する準備ができました。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Active Directory 認証ライブラリのシングル サインオンによるアプリケーションの認証]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
  [1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
  [2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
  [3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
  [4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png
  [5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
  [6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
  [7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
  [8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-default-domain.png
  [9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
