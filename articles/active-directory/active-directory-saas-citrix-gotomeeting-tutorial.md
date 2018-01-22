---
title: "チュートリアル: Azure Active Directory と GoToMeeting の統合 | Microsoft Docs"
description: "Azure Active Directory と GoToMeeting の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bcaf19f2-5809-4e1c-acbc-21a8d3498ccf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: jeedes
ms.openlocfilehash: 4826dee82e62ffac70d7ca3d6dcfe005129de764
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="tutorial-azure-active-directory-integration-with-gotomeeting"></a>チュートリアル: Azure Active Directory と GoToMeeting の統合

このチュートリアルでは、GoToMeeting と Azure Active Directory (Azure AD) を統合する方法について説明します。

GoToMeeting と Azure AD の統合には、次の利点があります。

- GoToMeeting にアクセスできるユーザーを Azure AD で制御できます。
- ユーザーが Azure AD アカウントで自動的に GoToMeeting にサインオンできるようにします (シングル サインオン)。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と GoToMeeting の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- GoToMeeting でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの GoToMeeting の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-gotomeeting-from-the-gallery"></a>ギャラリーからの GoToMeeting の追加
Azure AD への GoToMeeting の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に GoToMeeting を追加する必要があります。

**ギャラリーから GoToMeeting を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**GoToMeeting**」と入力し、結果ウィンドウで **[GoToMeeting]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の GoToMeeting](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、GoToMeeting で Azure AD シングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する GoToMeeting ユーザーが Azure AD で認識されている必要があります。 つまり、Azure AD ユーザーと GoToMeeting の関連ユーザーの間で、リンク関係が確立されている必要があります。

GoToMeeting で、Azure AD の**ユーザー名**の値を **Username** の値として割り当ててリンク関係を確立します。

GoToMeeting で Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[GoToMeeting テスト ユーザーの作成](#create-a-gotomeeting-test-user)** - GoToMeeting で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、GoToMeeting アプリケーションでシングル サインオンを構成します。

**GoToMeeting で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **GoToMeeting** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_samlbase.png)

3. **[GoToMeeting のドメインと URL]** セクションで、次の手順を実行します。

    ![[GoToMeeting Domain and URLs]\(GoToMeeting のドメインと URL\) のシングル サインオン情報](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_url.png)

    **[識別子]** ボックスに次の URL を入力します。`https://login.citrixonline.com/saml/sp`

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_400.png)

6. **メタデータ** URL を生成するには、次の手順を実行します。

    a. **[アプリの登録]** をクリックします。
    
    ![[Configure Single Sign-On]](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_appregistrations.png)
   
    b. **[エンドポイント]** をクリックして **[エンドポイント]** ダイアログ ボックスを開きます。  
    
    ![[Configure Single Sign-On]](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_endpointicon.png)

    c. コピー ボタンをクリックして、**フェデレーション メタデータ ドキュメント**の URL をコピーしてノートパッドに貼り付けます。
    
    ![[Configure Single Sign-On]](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_endpoint.png)
     
    d. ここで、**GoToMeeting** のプロパティ ページに移動し、**[コピー]** ボタンを使用して**アプリケーション ID** をコピーしてノートパッドに貼り付けます。
 
    ![[Configure Single Sign-On]](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_appid.png)

    e. 次のパターンを使用して**メタデータ URL** を生成します。`<FEDERATION METADATA DOCUMENT url>?appid=<application id>`   

7. **[GoToMeeting Configuration]\(GoToMeeting の構成\)** セクションで、**[Configure GoToMeeting]\(GoToMeeting の構成\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![GoToMeeting の構成](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_configure.png) 

8. 別のブラウザー ウィンドウで、[GoToMeeting Organization Center](https://organization.logmeininc.com/) にログインします。

9. **[identity provider]\(ID プロバイダー\)** タブで、生成された**メタデータ URL**、ダウンロードした**メタデータ ファイル**または**手動**を指定して、Azure 設定を構成できます。

10. **メタデータ URL** の場合は、次の手順を実行します。

    ![GoToMeeting の構成](./media/active-directory-saas-gotomeeting-tutorial/config1.png)

    a. **[How would you like to configure your SAML IDP?]\(SAML IDP を構成する方法\)** のドロップダウン リストから **[Automatic]\(自動\)** を選択します。

    b. **メタデータ URL** (前の手順で生成したもの) を **[メタデータ URL]** テキスト ボックスに貼り付けます。

    c. **[Save]** をクリックします。

11. **メタデータ ファイル**の場合は、次の手順を実行します。

    ![GoToMeeting の構成](./media/active-directory-saas-gotomeeting-tutorial/config2.png)

    a. **[How would you like to configure your SAML IDP?]\(SAML IDP を構成する方法\)** のドロップダウン リストから **[Upload SAML metadata file]\(SAML メタデータ ファイルをアップロードする\)** を選択します。

    b. ダウンロードしたメタデータ ファイルをアップロードするには、**[Upload metadata file]\(メタデータ ファイルのアップロード\)** をクリックします。

    c. **[Save]** をクリックします。

12. **手動**の場合は、次の手順を実行します。

    ![GoToMeeting の構成](./media/active-directory-saas-gotomeeting-tutorial/config3.png)

    a.  **[Sign-in page URL]\(サインイン ページ URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    b.  **[Sign-out page URL]\(サインアウト ページ URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。

    c.  **[Identity Provider Entity ID]\(ID プロバイダー エンティティ ID\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    d. ダウンロードしたメタデータ ファイルから X509Certificate を抽出し、**[Upload certificate]\(証明書のアップロード\)** をクリックしてこの証明書をアップロードします。

    e. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-gotomeeting-test-user"></a>GoToMeeting のテスト ユーザーを作成する

このセクションでは、Britta Simon というユーザーを GoToMeeting に作成します。 GoToMeeting では、Just-In-Time プロビジョニングがサポートされています。これは既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ GoToMeeting に存在しない場合は、GoToMeeting にアクセスしようとしたときに新しいユーザーが作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[GoToMeeting サポート チーム](https://support.logmeininc.com/gotomeeting)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に GoToMeeting へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を GoToMeeting に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[GoToMeeting]** を選択します。

    ![アプリケーションの一覧の GoToMeeting のリンク](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [GoToMeeting] タイルをクリックすると、自動的に GoToMeeting アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [[ユーザー プロビジョニングの構成]](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrixgotomeeting-provisioning-tutorial)


<!--Image references-->

[1]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_203.png

