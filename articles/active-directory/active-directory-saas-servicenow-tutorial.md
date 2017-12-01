---
title: "チュートリアル: Azure Active Directory と ServiceNow の統合 | Microsoft Docs"
description: "Azure Active Directory と ServiceNow の間のシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: 7b25e2184296182687d49b798e5e5a9a2d623c6e
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>チュートリアル: Azure Active Directory と ServiceNow の統合

このチュートリアルでは、ServiceNow と Azure Active Directory (Azure AD) を統合する方法について説明します。

ServiceNow と Azure AD の統合には、次の利点があります。

- ServiceNow にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで ServiceNow に自動的にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

ServiceNow と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- ServiceNow の場合は、Calgary バージョン以降の ServiceNow のインスタンスまたはテナント
- ServiceNow Express の場合は、Helsinki バージョン以降の ServiceNow Express のインスタンス
- ServiceNow のテナントで [Multiple Provider Single Sign On プラグイン](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0)が有効になっている必要があります。 このプラグインを有効にするには、[サービス要求を送信してください](https://hi.service-now.com)。

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ServiceNow の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-servicenow-from-the-gallery"></a>ギャラリーからの ServiceNow の追加
Azure AD への ServiceNow の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ServiceNow を追加する必要があります。

**ギャラリーから ServiceNow を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**ServiceNow**」と入力し、結果ウィンドウで **[ServiceNow]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の ServiceNow](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、ServiceNow で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する ServiceNow ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと ServiceNow の関連ユーザーの間で、リンク関係が確立されている必要があります。

ServiceNow で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

ServiceNow で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[ServiceNow 向け Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on-for-servicenow)** - ユーザーがこの機能を使用できるようにします。
2. **[ServiceNow Express 向け Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on-for-servicenow-express)** - ユーザーがこの機能を使用できるようにします。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[ServiceNow テスト ユーザーの作成](#create-a-servicenow-test-user)** - ServiceNow で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>ServiceNow 向け Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、ServiceNow アプリケーションでシングル サインオンを構成します。

**ServiceNow で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **ServiceNow** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. **[ServiceNow のドメインと URL]** セクションで、次の手順に従います。

    ![[ServiceNow のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_url.png)

    a. **[サインオン URL]** ボックスに、`https://<instance-name>.service-now.com/navpage.do` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<instance-name>.service-now.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 これらの値を、このチュートリアルの後半で説明する実際のサインオン URL と識別子に更新する必要があります。

4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-servicenow-tutorial/tutorial_general_400.png)

6. **メタデータ** URL を生成するには、次の手順を実行します。

    a. **[アプリの登録]** をクリックします。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-servicenow-tutorial/appregistrations.png)

    b. **[エンドポイント]** をクリックして **[エンドポイント]** ダイアログ ボックスを開きます。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-servicenow-tutorial/endpointicon.png)
    
    c. コピー ボタンをクリックして、**フェデレーション メタデータ ドキュメント**の URL をコピーしてノートパッドに貼り付けます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-servicenow-tutorial/endpoint.png)

    d. 次に、**ServiceNow** のプロパティに移動し、**[コピー]** ボタンで**アプリケーション ID** をコピーしてメモ帳に貼り付けます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-servicenow-tutorial/appid.png)

    e. 次のパターンを使用して**メタデータ URL** を生成します。`<FEDERATION METADATA DOCUMENT url>?appid=<application id>`  このメタデータ URL はチュートリアルの後半で使用するため、生成された値をメモ帳にコピーしてください。

7. SAML ベースの認証に対応するように ServiceNow を Azure AD で自動的に構成できるように、ServiceNow 用にワン クリック構成サービスが提供されています。 このサービスを有効にするには、**[ServiceNow 構成]** セクションに移動し、**[ServiceNow を構成する]** をクリックして [サインオンの構成] ウィンドウを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_configure.png)

8. ServiceNow のインスタンス名、管理者のユーザー名、および管理者パスワードを **[サインオンの構成]** フォームに入力し、**[今すぐ構成]** をクリックします。 ここで指定する管理者のユーザー名には、ServiceNow で **security_admin** ロールが割り当てられている必要があります。 これとは別に、ServiceNow を手動で構成して Azure AD を SAML ID プロバイダーとして使用させる方法があります。**[シングル サインオンを手動で構成する]** をクリックして、[クイック リファレンス] セクションから**サインアウト URL、SAML エンティティ ID、および SAML シングル サインオン サービス URL** をコピーします。

    ![アプリケーション URL の構成](./media/active-directory-saas-servicenow-tutorial/configure.png "Configure app URL")

9. ServiceNow アプリケーションに管理者としてサインオンします。

10. **[Integration - Multiple Provider Single Sign-On Installer (統合 - 複数プロバイダーのシングル サインオン インストーラー)]** プラグインをアクティブ化するには、次の手順に従います。

    a. 左側のナビゲーション ウィンドウの検索バーで **}[System Definition]\(システム定義\)** セクションを検索し、**[Plugins]\(プラグイン\)** をクリックします。

    ![プラグインをアクティブにする](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "プラグインをアクティブにする")

     b. "**Integration - Multiple Provider Single Sign-On Installer**" を検索します。

     ![プラグインをアクティブにする](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "プラグインをアクティブにする")

    c. プラグインを選択します。 右クリックして **[Activate/Upgrade]** を選択します。

    d. **[Activate (アクティブ化)]** ボタンをクリックします。

11. 左側のナビゲーション ウィンドウの検索バーで **[Multi-Provider SSO]** セクションを検索し、**[Properties]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "Configure app URL")

12. **[Multiple Provider SSO Properties]** ダイアログで、次の手順を実行します。

    ![アプリケーション URL の構成](./media/active-directory-saas-servicenow-tutorial/ic7694981.png "Configure app URL")

    a. **[Enable multiple provider SSO]** で **[Yes]** を選択します。

    b. **[Enable Auto Importing of users from all identity providers into the user table]** で、**[Yes]** を選択します。

    c. **[Enable debug logging for the multiple provider SSO integration]** で **[Yes]** を選択します。

    d. **[The field on the user table that...]** ボックスに、「**user_name**」と入力します。

    e. **[Save]** をクリックします。

13. 左側のナビゲーション ウィンドウの検索バーで **[Multi-Provider SSO]** セクションを検索し、**[x509 Certificates]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Configure single sign-on")

14. **[x509 Certificates]** ダイアログで、**[New]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/ic7694974.png "Configure single sign-on")

15. **[X.509 Certificates]** ダイアログで、次の手順を実行します。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/ic7694975.png "Configure single sign-on")

    a. **[Name]** ボックスに、構成の名前を入力します (例: **TestSAML2.0**)。

    b. **[アクティブ]**を選択します。

    c. **[Format]** で **[PEM]** を選択します。

    d. **[Type]** で **[Trust Store Cert]** を選択します。

    e. Azure からダウンロードした Base64 エンコードの証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[PEM Certificate]** ボックスに貼り付けます。

     f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[Submit]**をクリックします。

16. 左側のナビゲーション ウィンドウで、 **[Identity Providers]**をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Configure single sign-on")

17. **[Identity Providers]\(ID プロバイダー\)** ダイアログ ボックスで、**[New]\(新規\)** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/ic7694977.png "Configure single sign-on")

18. **[Identity Providers]\(ID プロバイダー\)** ダイアログ ボックスで、**[SAML2 Update1?]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/ic7694978.png "Configure single sign-on")

19. [SAML2 Update1 Properties] ダイアログで、次の手順を実行します。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/idp.png "Configure single sign-on")

    a. **[Import Identity Provider Metadata]** ダイアログ ボックスで **[URL]** オプションを選択します。

    b. Azure Portal で生成された**メタデータ URL** を入力します。

    c. **[インポート]**をクリックします。

20. IdP メタデータ URL が読み取られ、すべてのフィールド情報が設定されます。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/ic7694982.png "Configure single sign-on")

    a. **[Name]** ボックスに、構成の名前を入力します (例: **SAML 2.0**)。

    b. ServiceNow のデプロイでユーザーを一意に識別するためのフィールドに応じて、**[User Field]** ボックスに「**email**」または「**user_id**」と入力します。

    > [!NOTE]
    > SAML トークンの一意の識別子として Azure AD ユーザー ID (ユーザーのプリンシパル名) か電子メール アドレスを出力するように Azure AD を構成できます。そのためには、Azure Portal で **[ServiceNow]、[属性]、[シングル サインオン]** セクションの順に移動し、目的のフィールドを **nameidentifier** 属性にマッピングします。 Azure AD に格納される選択した属性 (ユーザー プリンシパル名など) の値と、ServiceNow に格納される入力したフィールド (user_name など) の値が一致している必要があります。

    c. **[ServiceNow Homepage]** の値をコピーして、Azure Portal の **[ServiceNow のドメインと URL]** セクションにある **[サインオン URL]** ボックスに貼り付けます。

    > [!NOTE]
    > ServiceNow インスタンス ホームページは、**ServiceNow テナント URL** と **/navpage.do** を連結した形式です (例: `https://fabrikam.service-now.com/navpage.do`)。

    d. **[Entity ID / Issuer]** の値をコピーして、Azure Portal の **[ServiceNow のドメインと URL]** セクションにある **[識別子]** ボックスに貼り付けます。

     e. **[x509 Certificate]** で、前の手順で作成した証明書を一覧表示します。

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>ServiceNow Express 向け Azure AD シングル サインオンの構成

1. Azure Portal の **ServiceNow** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. **[ServiceNow のドメインと URL]** セクションで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_url.png)

    a. **[サインオン URL]** ボックスに、`https://<instance-name>.service-now.com/navpage.do` のパターンを使用して値を入力します。

    b. **[識別子]** ボックスに、`https://<instance-name>.service-now.com` の形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[ServiceNow クライアント サポート チーム](https://www.servicenow.com/support/contact-support.html)に問い合わせてください。

4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_certificate.png)

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-servicenow-tutorial/tutorial_general_400.png)

6. SAML ベースの認証に対応するため、Azure AD による ServiceNow の自動構成を行う ServiceNow 用のワン クリック構成サービスが提供されています。 このサービスを有効にするには、**[ServiceNow 構成]** セクションに移動し、**[ServiceNow を構成する]** をクリックして [サインオンの構成] ウィンドウを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_configure.png)

7. ServiceNow のインスタンス名、管理者のユーザー名、および管理者パスワードを **[サインオンの構成]** フォームに入力し、**[今すぐ構成]** をクリックします。 ここで指定する管理者のユーザー名には、ServiceNow で **security_admin** ロールが割り当てられている必要があります。 これとは別に、ServiceNow を手動で構成して Azure AD を SAML ID プロバイダーとして使用させる方法があります。**[シングル サインオンを手動で構成する]** をクリックして、[クイック リファレンス] セクションから**サインアウト URL、SAML エンティティ ID、および SAML シングル サインオン サービス URL** をコピーします。

    ![アプリケーション URL の構成](./media/active-directory-saas-servicenow-tutorial/configure.png "Configure app URL")

8. ServiceNow Express アプリケーションに管理者としてサインオンします。

9. 左側にあるナビゲーション ウィンドウで、**[Single Sign-On (シングル サインオン)]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "Configure app URL")

10. **[Single Sign-On (シングル サインオン)]** ダイアログで右上の構成アイコンをクリックし、次のプロパティを設定します。

    ![アプリケーション URL の構成](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "Configure app URL")

    a. **[Enable multiple provider SSO (複数プロバイダー SSO を有効にする)]** を右に切り替えます。
    
    b. **[Enable debug logging for the multiple provider SSO integration (複数プロバイダー SSO 統合でのデバッグ ログの有効化)]** を右に切り替えます。
    
    c. **[The field on the user table that...]** ボックスに、「**user_name**」と入力します。

11. **[Single Sign-On (シングル サインオン)]** ダイアログで、**[Add New Certificate (新しい証明書の追加)]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Configure single sign-on")

12. **[X.509 Certificates]** ダイアログで、次の手順を実行します。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/ic7694975.png "Configure single sign-on")

    a. **[Name]** ボックスに、構成の名前を入力します (例: **TestSAML2.0**)。

    b. **[アクティブ]**を選択します。

    c. **[Format]** で **[PEM]** を選択します。

    d. **[Type]** で **[Trust Store Cert]** を選択します。

    e. Azure Portal からダウンロードした Base64 エンコードの証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[PEM Certificate]** ボックスに貼り付けます。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[Update]\(更新\)** をクリックします。

13. **[Single Sign-On (シングル サインオン)]** ダイアログで、**[Add New IdP (新しい IdP の追加)]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Configure single sign-on")

14. **[Add New Identity Provider (新しい ID プロバイダーの追加)]** ダイアログの **[Configure Identity Provider (ID プロバイダーの構成)]** で、次の手順に従います。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Configure single sign-on")

    a. **[Name]** ボックスに、構成の名前を入力します (例: **SAML 2.0**)。

    b. **[Identity Provider URL]\(ID プロバイダー URL\)** フィールドに、Azure Portal からコピーした **ID プロバイダー ID** の値を貼り付けます。
    
    c. **[Identity Provider's AuthnRequest]\(ID プロバイダーの AuthnRequest\)** フィールドに、Azure Portal からコピーした**認証要求 URL** の値を貼り付けます。

    d. **[Identity Provider's SingleLogoutRequest]\(ID プロバイダーの SingleLogoutRequest\)** フィールドに、Azure Portal からコピーした**シングル サインアウト サービス URL** の値を貼り付けます。

    e. **[Identity Provider Certificate (ID プロバイダー証明書)]** で、前の手順で作成した証明書を選択します。

15. **[Advanced Settings (詳細設定)]** をクリックし、**[Additional Identity Provider Properties (追加の ID プロバイダーのプロパティ)]** で、次の手順に従います。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Configure single sign-on")

    a. **[Protocol Binding for the IDP's SingleLogoutRequest]** ボックスに、「**urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**」と入力します。

    b. **[NameID Policy (NameID ポリシー)]** ボックスに「**urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**」と入力します。

    c. **[AuthnContextClassRef Method (AuthnContextClassRef メソッド)]** に「`http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`」と入力します。

    d. **[Create an AuthnContextClass]** をオフにします。

16. **[Additional Service Provider Properties (追加のサービス プロバイダーのプロパティ)]** で、次の手順に従います。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Configure single sign-on")

    a. **[ServiceNow Homepage]** ボックスに ServiceNow インスタンス ホームページの URL を入力します。

    > [!NOTE]
    > ServiceNow インスタンス ホームページは、**ServiceNow テナント URL** と **/navpage.do** を連結した形式です (例: `https://fabrikam.service-now.com/navpage.do`)。

    b. **[Entity ID / Issuer]** ボックスに、ServiceNow テナントの URL を入力します。

    c. **[Audience URI (対象ユーザー URI)]** ボックスに、ServiceNow テナントの URL を入力します。

    d. **[Clock Skew]** ボックスに「**60**」と入力します。

    e. ServiceNow のデプロイでユーザーを一意に識別するためのフィールドに応じて、**[User Field]** ボックスに「**email**」または「**user_id**」と入力します。

    > [!NOTE]
    > SAML トークンの一意の識別子として Azure AD ユーザー ID (ユーザーのプリンシパル名) か電子メール アドレスを出力するように Azure AD を構成できます。そのためには、Azure Portal で **[ServiceNow]、[属性]、[シングル サインオン]** セクションの順に移動し、目的のフィールドを **nameidentifier** 属性にマッピングします。 Azure AD に格納される選択した属性 (ユーザー プリンシパル名など) の値と、ServiceNow に格納される入力したフィールド (user_name など) の値が一致している必要があります。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-servicenow-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-servicenow-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-servicenow-test-user"></a>ServiceNow テスト ユーザーの作成

このセクションでは、ServiceNow で Britta Simon というユーザーを作成します。 ServiceNow または ServiceNow Express アカウントにユーザーを追加する方法がわからない場合は、[ServiceNow クライアント サポート チーム](https://www.servicenow.com/support/contact-support.html)に問い合わせてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に ServiceNow へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を ServiceNow に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[ServiceNow]** を選択します。

    ![アプリケーションの一覧の ServiceNow リンク](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [ServiceNow] タイルをクリックすると、自動的に ServiceNow アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png

