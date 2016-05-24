<properties 
   pageTitle="Visual Studio の接続済みサービスを利用して Azure Active Directory を追加する | Microsoft Azure"
   description="Visual Studio の [接続済みサービスの追加] ダイアログ ボックスを使用してアプリに Azure Active Directory を追加する"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="visual-studio-online"
   ms.date="05/08/2016"
   ms.author="tarcher" />

# Visual Studio の接続済みサービスを利用して Azure Active Directory を追加する 

##概要
Azure Active Directory (Azure AD) を使用し、ASP.NET MVC Web アプリケーションまたは Web API サービスの AD 認証のためのシングル サインオン (SSO) をサポートできます。Azure AD 認証を利用すれば、Azure AD のアカウントで Web アプリケーションに接続できます。Web API で Azure AD 認証を利用する利点には、Web アプリケーションから API を公開するときにデータのセキュリティが強化されることが含まれます。Azure AD では、独自のアカウントとユーザー管理で別個の認証システムを管理する必要がありません。

## サポートされているプロジェクトの種類

[接続済みサービス] ダイアログを使用し、次の種類のプロジェクトで Azure AD に接続できます。

- ASP.NET MVC プロジェクト

- ASP.NET Web API プロジェクト


### [接続済みサービス] ダイアログを使用して Azure AD に接続する

1. Azure アカウントがあることを確認します。Azure アカウントを持っていない場合、[無料試用版](http://go.microsoft.com/fwlink/?LinkId=518146)でサインアップできます。

1. Visual Studio で、プロジェクトの **[参照]** ノードのショートカット メニューを開き、**[接続済みサービスの追加]** を選択します。
1. **[Azure AD 認証]** を選択し、**[構成]** を選択します。

    ![Add Azure AD 認証を選択する](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. **[Azure AD 認証の構成]** の最初のページで、**[Azure AD を利用してシングル サインオンを構成する]** にチェックを入れます。

    プロジェクトが別の認証構成で構成されるとき、続行すると前の構成が無効になることがウィザードにより警告されます。

    ![ウィザードで Azure AD を構成する](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1.  2 番目のページで、**[ドメイン]** ドロップダウン リストからドメインを選択します。ドメインのリストには、[アカウント設定] ダイアログの一覧にあるアカウントでアクセスできるすべてのドメインが含まれます。探しているドメインが見つからない場合、代替として、「mydomain.onmicrosoft.com」のようなドメイン名を入力できます。新しい Azure AD アプリを作成したり、既存の Azure AD アプリの設定を利用したりできます。

    ![ウィザードで Azure AD を構成する](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)


1. ウィザードの 3 番目のページで、**[ディレクトリ データの読み取り]** にチェックが入っていることを確認します。ウィザードは **[クライアント シークレット]** に入力します。

    ![ウィザードで Azure AD を構成する](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. **[完了]** ボタンを選択します。ダイアログにより必要な構成コードと参照が追加され、プロジェクトの Azure AD 認証を有効にします。AD ドメインは [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)で確認できます。

1. ブラウザーに表示される [作業の開始] ページで次のステップの内容について確認し、[変更内容] ページでプロジェクトがどのように変更されたか確認します。すべて問題なく機能したことを確認するには、変更された構成ファイルの 1 つを開き、[変更内容] ページで説明している設定があることを確認します。たとえば、ASP.NET MVC プロジェクトのメインの web.config にこれらの設定が追加されます。

        <appSettings> 
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:AADInstance" value="https://login.windows.net/" />
            <add key="ida:Domain" value="Your selected domain" />
            <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
            <add key="ida:PostLogoutRedirectUri" value="The default redirect URI from the project" />
        </appSettings>

## プロジェクトを変更する方法

ウィザードを実行すると、Visual Studio により Azure AD と関連参照がプロジェクトに追加されます。プロジェクトの構成ファイルとコード ファイルも変更され、Azure AD のサポートが追加されます。Visual Studio による特定の変更はプロジェクトの種類によって異なります。ASP.NET MVC プロジェクトを変更する方法の詳細については、[変更内容 – MVC プロジェクト](http://go.microsoft.com/fwlink/p/?LinkID=513809)に関するページを参照してください。Web API プロジェクトの場合、[変更内容 - Web API プロジェクト](http://go.microsoft.com/fwlink/p/?LinkId=513810)に関するページを参照してください。

##次のステップ

質問してヘルプを表示します。

 - [MSDN フォーラム: Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)

 - [Azure AD ドキュメント](https://azure.microsoft.com/documentation/services/active-directory/)

 - [ブログの投稿: Azure AD の概要](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

<!---HONumber=AcomDC_0511_2016-->