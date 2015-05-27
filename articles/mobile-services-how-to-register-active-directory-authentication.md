<properties 
	pageTitle="Azure Active Directory 認証用の登録 - Mobile Services" 
	description="Mobile Services アプリケーションで Azure Active Directory 認証用に登録する方法について説明します。" 
	authors="wesmc7777" 
	services="mobile-services" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="wesmc"/>

# アプリケーションを登録して Azure Active Directory アカウント ログインを使用する
##概要


このトピックでは、Azure モバイル サービスの認証プロバイダーとして Azure Active Directory を使用できるようにアプリケーションを登録する方法について説明します。

##アプリを登録する

>[AZURE.NOTE]このトピックで説明する手順は、アプリケーションで[サービス主導型ログイン操作](http://msdn.microsoft.com/library/azure/dn283952.aspx)を使用する場合にチュートリアル「[Mobile Services アプリへの認証の追加](mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md)」と併せて使用するように作られています。アプリケーションの要件として Azure Active Directory および .NET バックエンド モバイル サービスに対して[クライアント主導型ログイン操作](http://msdn.microsoft.com/library/azure/jj710106.aspx)が必要な場合は、「[Active Directory 認証ライブラリのシングル サインオンによるアプリケーションの認証](mobile-services-windows-store-dotnet-adal-sso-authentication.md)」チュートリアルに従ってください。


1. [Azure 管理ポータル]にログオンし、**[Mobile Services]** をクリックして、目的のモバイル サービスをクリックします。

    ![][1]

2. モバイル サービスの **[ID]** タブをクリックします。

    ![][2]

3. **Azure Active Directory** ID プロバイダーのセクションまで下にスクロールし、そこに表示されている**アプリケーション URL** をコピーします。

    ![][3]

4. 管理ポータルの **[Active Directory]** に移動し、目的のディレクトリをクリックします。

    ![][4]

5. 上部にある **[アプリケーション]** タブをクリックし、アプリケーションの **[追加]** をクリックします。

    ![][10]

6. **[組織で開発中のアプリケーションを追加]** をクリックします。

7. アプリケーションの追加ウィザードで、アプリケーションの**名前**を入力し、種類として **[Web アプリケーションや Web API]** をクリックします。その後、クリックして続行します。

    ![][5]

8. **[サインオン URL]** ボックスで、モバイル サービスの Active Directory ID プロバイダーの設定からコピーしたアプリケーション ID を貼り付けます。**[アプリケーション ID/URI]** ボックスに同じ一意のリソース識別子を入力します。その後、クリックして続行します。
 
    ![][6]


9. アプリケーションが追加されたら **[構成]** タブをクリックします。その後、アプリケーションの**クライアント ID** をコピーします。

    モバイル サービスに .Net バックエンドを使用するモバイル サービスを作成した場合、**[シングル サインオン]** の **[応答 URL]** を編集して、モバイル サービスの URL の末尾のパスが _signin-aad_ になるようにします。たとえば、`https://todolist.azure-mobile.net/signin-aad` のように指定します。

    ![][8]


10. モバイル サービスの **[ID]** タブに戻ります。1 番下で、Azure Active Directory ID プロバイダーの **[クライアント ID]** 設定に貼り付けます。

  
11. **[許可されたテナント]** リストで、アプリケーションを登録したディレクトリのドメインを追加する必要があります (contoso.onmicrosoft.com など)。デフォルトのドメイン名は Active Directory の **[ドメイン]** タブをクリックして検索できます。

    ![][11]
 
    ドメイン名を **[許可されたテナント]** リストに追加して **[保存]** をクリックします。


    ![][9]



これで、アプリケーションで認証に Azure Active Directory を使用する準備ができました。



<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
[2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
[3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
[4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
[5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
[6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
[7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-3-waad-auth.png
[8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
[9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
[10]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png
[11]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-default-domain.png

<!-- URLs. -->
[Azure 管理ポータル]: https://manage.windowsazure.com/


<!--HONumber=54-->