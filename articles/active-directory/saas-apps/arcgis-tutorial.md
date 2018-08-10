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
ms.date: 12/13/2017
ms.author: jeedes
ms.openlocfilehash: 24a82bbaf47153791da2f21a0b68c2f81c0670e7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446358"
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
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ArcGIS Online の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-arcgis-online-from-the-gallery"></a>ギャラリーからの ArcGIS Online の追加
ArcGIS Online の Azure AD への統合を構成するには、ArcGIS Online をギャラリーから管理対象 SaaS アプリの一覧に追加する必要があります。

**ギャラリーから ArcGIS Online を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**ArcGIS Online**」と入力し、結果パネルで **[ArcGIS Online]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の ArcGIS Online](./media/arcgis-tutorial/tutorial_arcgisonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" という名前のテスト ユーザーに基づいて、ArcGIS Online で Azure AD のシングル サインオンを構成およびテストします。

シングル サインオンを機能させるには、Azure AD が Azure AD のユーザーに対応する ArcGIS Online のユーザーを認識している必要があります。 つまり、Azure AD ユーザーと ArcGIS Online の関連するユーザーの間のリンク関係が確立されている必要があります。

ArcGIS Online で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

ArcGIS Online で Azure AD のシングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[ArcGIS Online テスト ユーザーの作成](#create-a-arcgis-online-test-user)** - Azure AD でのユーザーにリンクされた、ArcGIS Online での Britta Simon の対応するユーザーを作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、ArcGIS Online アプリケーションでシングル サインオンを構成します。

**ArcGIS Online で Azure AD のシングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **[ArcGIS Online]** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/arcgis-tutorial/tutorial_arcgisonline_samlbase.png)

1. **[ArcGIS Online Domain and URLs] \(ArcGIS Online のドメインと URL\)** セクションで、次の手順を実行します。

    ![[ArcGIS Online Domain and URLs] \(ArcGIS Online のドメインと URL\) のシングル サインオン情報](./media/arcgis-tutorial/tutorial_arcgisonline_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.maps.arcgis.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`<companyname>.maps.arcgis.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[ArcGIS Online クライアント サポート チーム](http://support.esri.com/en/)に問い合わせてください。 
 


1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/arcgis-tutorial/tutorial_arcgisonline_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/arcgis-tutorial/tutorial_general_400.png)

1. 別の Web ブラウザーのウィンドウで、管理者として ArcGIS 企業サイトにログインします。

1. **[EDIT SETTINGS] \(設定の編集)** をクリックします。

    ![Edit Settings (設定の編集)](./media/arcgis-tutorial/ic784742.png "Edit Settings")

1. **[セキュリティ]** をクリックします。

    ![Security (セキュリティ)](./media/arcgis-tutorial/ic784743.png "Security")

1. **[Enterprise Logins] [エンタープライズ ログイン]** で、**[SET IDENTITY PROVIDER] \(ID プロバイダーの設定)** をクリックします。

    ![Enterprise Logins (エンタープライズ ログイン)](./media/arcgis-tutorial/ic784744.png "Enterprise Logins")

1. **[Set Identity Provider]** 構成ページで、次の手順に従います。
   
    ![Set Identity Provider (ID プロバイダーの設定)](./media/arcgis-tutorial/ic784745.png "Set Identity Provider")
   
    a. **[名前]** テキスト ボックスに組織の名前を入力します。

    b. **[Metadata for the Enterprise Identity Provider will be supplied using]** で、**[A File]** を選択します。

    c. ダウンロードしたメタデータ ファイルをアップロードするには、 **[Choose file]** をクリックします。

    d. **[SET IDENTITY PROVIDER] \(ID プロバイダーの設定)** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/arcgis-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/arcgis-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/arcgis-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/arcgis-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-arcgis-online-test-user"></a>ArcGIS Online テスト ユーザーの作成

Azure AD ユーザーが ArcGIS Online にログインできるようにするには、そのユーザーを ArcGIS Online にプロビジョニングする必要があります。  
ArcGIS Online の場合、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **ArcGIS** テナントにログインします。

1. **[INVITE MEMBERS] \(メンバーの招待)** をクリックします。
   
    ![Invite Members (メンバーの招待)](./media/arcgis-tutorial/ic784747.png "Invite Members")

1. **[Add members automatically without sending an email] \(電子メールを送信せずにメンバーを自動的に追加する)** を選択し、**[NEXT] \(次へ)** をクリックします。
   
    ![メンバーの自動追加](./media/arcgis-tutorial/ic784748.png "Add Members Automatically")

1. **[Members]** ダイアログ ページで、次の手順に従います。
   
     ![追加とレビュー](./media/arcgis-tutorial/ic784749.png "Add and review")
    
     a. プロビジョニングする有効な AAD アカウントの **[電子メール]**、**[名]**、および **[姓]** を入力します。
  
     b. **[ADD AND REVIEW] \(追加とレビュー)** をクリックします。
1. 入力したデータを確認してから、**[ADD MEMBERS] \(メンバーの追加)** をクリックします。
   
    ![メンバーの追加](./media/arcgis-tutorial/ic784750.png "Add member")
        
    > [!NOTE]
    > Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、ArcGIS Online へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を ArcGIS Online に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で、**[ArcGIS Online]** を選択します。

    ![アプリケーションの一覧の ArcGIS Online のリンク](./media/arcgis-tutorial/tutorial_arcgisonline_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [ArcGIS Online] タイルをクリックすると、ArcGIS Online アプリケーションに自動的にサインオンされます。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/arcgis-tutorial/tutorial_general_01.png
[2]: ./media/arcgis-tutorial/tutorial_general_02.png
[3]: ./media/arcgis-tutorial/tutorial_general_03.png
[4]: ./media/arcgis-tutorial/tutorial_general_04.png

[100]: ./media/arcgis-tutorial/tutorial_general_100.png

[200]: ./media/arcgis-tutorial/tutorial_general_200.png
[201]: ./media/arcgis-tutorial/tutorial_general_201.png
[202]: ./media/arcgis-tutorial/tutorial_general_202.png
[203]: ./media/arcgis-tutorial/tutorial_general_203.png

