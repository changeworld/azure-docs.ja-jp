---
title: 'チュートリアル: Azure Active Directory と ArcGIS Online の統合 | Microsoft Docs'
description: Azure Active Directory と ArcGIS Online の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 12ab224481c519db36ae21dd11916649ff0bfbe3
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269036"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>チュートリアル: Azure Active Directory と ArcGIS Online の統合

このチュートリアルでは、ArcGIS Online と Azure Active Directory (Azure AD) を統合する方法について説明します。

ArcGIS Online と Azure AD の統合には、次の利点があります。

- Azure AD で誰が ArcGIS Online にアクセスできるかを制御できます。
- ユーザーが自分の Azure AD アカウントで ArcGIS Online に自動的にサインオンされる (シングル サインオン) ようにできます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と ArcGIS Online の統合を構成するには、次の項目が必要です。

- Azure AD サブスクリプション
- ArcGIS Online でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ArcGIS Online の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-arcgis-online-from-the-gallery"></a>ギャラリーからの ArcGIS Online の追加

ArcGIS Online の Azure AD への統合を構成するには、ArcGIS Online をギャラリーから管理対象 SaaS アプリの一覧に追加する必要があります。

**ギャラリーから ArcGIS Online を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![image](./media/arcgis-tutorial/selectazuread.png)

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![image](./media/arcgis-tutorial/a_select_app.png)
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![image](./media/arcgis-tutorial/a_new_app.png)

4. 検索ボックスに「**ArcGIS Online**」と入力し、結果パネルで **[ArcGIS Online]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![image](./media/arcgis-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" という名前のテスト ユーザーに基づいて、ArcGIS Online で Azure AD のシングル サインオンを構成およびテストします。

シングル サインオンを機能させるには、Azure AD が Azure AD のユーザーに対応する ArcGIS Online のユーザーを認識している必要があります。 つまり、Azure AD ユーザーと ArcGIS Online の関連するユーザーの間のリンク関係が確立されている必要があります。

ArcGIS Online で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

ArcGIS Online で Azure AD のシングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[ArcGIS Online テスト ユーザーの作成](#create-an-arcgis-online-test-user)** - Azure AD でのユーザーにリンクされた、ArcGIS Online での Britta Simon の対応するユーザーを作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、ArcGIS Online アプリケーションでシングル サインオンを構成します。

**ArcGIS Online で Azure AD のシングル サインオンを構成するには、次の手順を実行します。**

1. [Azure portal](https://portal.azure.com/) の **[ArcGIS Online]** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![image](./media/arcgis-tutorial/b1_b2_select_sso.png)

2. 画面上部の **[シングル サインオン モードの変更]** をクリックして、**[SAML]** モードを選択します。

      ![image](./media/arcgis-tutorial/b1_b2_saml_ssso.png)

3. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![image](./media/arcgis-tutorial/b1_b2_saml_sso.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![image](./media/arcgis-tutorial/b1-domains_and_urlsedit.png)

5. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** テキスト ボックスに、`https://<companyname>.maps.arcgis.com` というパターンで URL を入力します。

    b. **[識別子]** ボックスに、`<companyname>.maps.arcgis.com` というパターンで URL を入力します。

    ![image](./media/arcgis-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[ArcGIS Online クライアント サポート チーム](http://support.esri.com/en/)に問い合わせてください。

6. **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**フェデレーション メタデータの XML** をダウンロードしてから、コンピューターに XML ファイルを保存します。

    ![image](./media/arcgis-tutorial/federationxml.png)

7. **ArcGIS Online** 内での構成を自動化するには、**[拡張機能のインストール]** をクリックして**マイアプリによるセキュリティで保護されたサインイン拡張機能** をインストールする必要があります。

    ![image](./media/arcgis-tutorial/install_extension.png)

8. ブラウザーに拡張機能を追加した後、**[setup ArcGIS Online]\(ArcGIS Online のセットアップ)** をクリックすると、ArcGIS Online アプリケーションに移動します。 そこから、管理者資格情報を提供して ArcGIS Online にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 9 ～ 13 が自動化されます。

9. ArcGIS Online を手動でセットアップしたい場合は、新しい Web ブラウザー ウィンドウを開き、管理者として ArcGIS Online 企業サイトにログインして、次の手順を実行します。

10. **[EDIT SETTINGS] \(設定の編集)** をクリックします。

    ![Edit Settings (設定の編集)](./media/arcgis-tutorial/ic784742.png "Edit Settings")

11. **[セキュリティ]** をクリックします。

    ![Security (セキュリティ)](./media/arcgis-tutorial/ic784743.png "Security")

12. **[Enterprise Logins] [エンタープライズ ログイン]** で、**[SET IDENTITY PROVIDER] \(ID プロバイダーの設定)** をクリックします。

    ![Enterprise Logins (エンタープライズ ログイン)](./media/arcgis-tutorial/ic784744.png "Enterprise Logins")

13. **[Set Identity Provider]** 構成ページで、次の手順に従います。

    ![Set Identity Provider (ID プロバイダーの設定)](./media/arcgis-tutorial/ic784745.png "Set Identity Provider")

    a. **[名前]** テキスト ボックスに組織の名前を入力します。

    b. **[Metadata for the Enterprise Identity Provider will be supplied using]** で、**[A File]** を選択します。

    c. ダウンロードしたメタデータ ファイルをアップロードするには、 **[Choose file]** をクリックします。

    d. **[SET IDENTITY PROVIDER] \(ID プロバイダーの設定)** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![image](./media/arcgis-tutorial/d_users_and_groups.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![image](./media/arcgis-tutorial/d_adduser.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![image](./media/arcgis-tutorial/d_userproperties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに、**brittasimon@yourcompanydomain.extension** と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。

### <a name="create-an-arcgis-online-test-user"></a>ArcGIS Online テスト ユーザーの作成

Azure AD ユーザーが ArcGIS Online にログインできるようにするには、そのユーザーを ArcGIS Online にプロビジョニングする必要があります。  
ArcGIS Online の場合、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **ArcGIS** テナントにログインします。

2. **[INVITE MEMBERS] \(メンバーの招待)** をクリックします。
   
    ![Invite Members (メンバーの招待)](./media/arcgis-tutorial/ic784747.png "Invite Members")

3. **[Add members automatically without sending an email] \(電子メールを送信せずにメンバーを自動的に追加する)** を選択し、**[NEXT] \(次へ)** をクリックします。
   
    ![メンバーの自動追加](./media/arcgis-tutorial/ic784748.png "Add Members Automatically")

4. **[Members]** ダイアログ ページで、次の手順に従います。
   
     ![追加とレビュー](./media/arcgis-tutorial/ic784749.png "Add and review")
    
     a. プロビジョニングする有効な AAD アカウントの **[電子メール]**、**[名]**、および **[姓]** を入力します。
  
     b. **[ADD AND REVIEW] \(追加とレビュー)** をクリックします。
5. 入力したデータを確認してから、**[ADD MEMBERS] \(メンバーの追加)** をクリックします。
   
    ![メンバーの追加](./media/arcgis-tutorial/ic784750.png "Add member")
        
    > [!NOTE]
    > Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、ArcGIS Online へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![image](./media/arcgis-tutorial/d_all_applications.png)

2. アプリケーションの一覧で、**[ArcGIS Online]** を選択します。

    ![image](./media/arcgis-tutorial/d_all_application.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![image](./media/arcgis-tutorial/d_leftpaneusers.png)

4. **[追加]** ボタンを選択し、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![image](./media/arcgis-tutorial/d_assign_user.png)

4. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

5. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [ArcGIS Online] タイルをクリックすると、ArcGIS Online アプリケーションに自動的にサインオンされます。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



