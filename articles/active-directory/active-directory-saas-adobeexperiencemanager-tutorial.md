---
title: "チュートリアル: Azure Active Directory と Adobe Experience Manager の統合 | Microsoft Docs"
description: "Azure Active Directory と Adobe Experience Manager の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: 4ba94f0f24b2791b3b32807aa60379aeadb79365
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>チュートリアル: Azure Active Directory と Adobe Experience Manager の統合

このチュートリアルでは、Adobe Experience Manager と Azure Active Directory (Azure AD) を統合する方法について説明します。

Adobe Experience Manager と Azure AD を統合すると、次の利点があります。

- Adobe Experience Manager にどの Azure AD ユーザーがアクセスできるかを管理できます。
- ユーザーが自分の Azure AD アカウントで自動的に Adobe Experience Manager にサインオン (シングル サインオン) できるように設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Adobe Experience Manager と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Adobe Experience Manager でのシングル サインオンが有効に設定されたサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Adobe Experience Manager を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>ギャラリーから Adobe Experience Manager を追加する
Azure AD と Adobe Experience Manager の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Adobe Experience Manager を追加する必要があります。

**ギャラリーから Adobe Experience Manager を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Adobe Experience Manager**」と入力し、結果ウィンドウで **[Adobe Experience Manager]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Adobe Experience Manager](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーを使用して、Adobe Experience Manager で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Adobe Experience Manager ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーとAdobe Experience Manager の関連ユーザーの間で、リンク関係が確立されている必要があります。

Adobe Experience Manager で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Adobe Experience Manager で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Adobe Experience Manager のテスト ユーザーの作成](#create-an-adobe-experience-manager-test-user)** - Adobe Experience Manager で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にして、Adobe Experience Manager アプリケーションでシングル サインオンを構成します。

**Adobe Experience Manager で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure ポータルの **Adobe Experience Manager** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. **[Adobe Experience Manager Domain and URL]\(Adobe Experience Manager のドメインと URL\)** セクションで、**IDP モード**でアプリケーションを構成する場合は、次の手順を実行します。

    ![Adobe Experience Manager のドメインと URL のシングル サインオン情報](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. **[識別子]** ボックスに、AEM のサーバーにも定義する一意の値を入力します。 

    b. **[応答 URL]** ボックスに、`https://<AEM Server Url>/saml_login` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 この値を取得するには、[Adobe Experience Manager サポート チーム](https://helpx.adobe.com/support/experience-manager.html)にお問い合わせください。
 
4. アプリケーションを **SP** 開始モードで構成する場合は、[詳細な URL 設定の表示] チェックボックスをオンにして次の手順を実行します。

    ![Adobe Experience Manager のドメインと URL のシングル サインオン情報](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    **[サインオン URL]** テキストボックスに、Adobe Experience Manager サーバーの URL を入力します。 

5. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. Adobe Experience Manager Configuration\(Adobe Experience Manager の構成\) セクションで、Configure Adobe Experience Manager\(Adobe Experience Manager を構成する\) をクリックして、サインオンの構成 ウィンドウを開きます。 [クイック リファレンス] セクションから、**[SAML シングル サインオン サービス URL]**、**[SAML エンティティ ID]**、および**[サインアウト URL]** をコピーします。

    ![構成セクションのリンク](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. 別のブラウザー ウィンドウで **Adobe Experience Manager** の管理ポータルを開きます。

9. **[設定]**  ->  **[セキュリティ]**  ->  **[ユーザー]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. **[管理者]** またはその他の関連するユーザーを選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. **[アカウント設定]**  ->  **[Create/Manage TrustStore]\(トラストストアの作成および管理\)** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. **[Add Certificate from CER file]\(CER ファイルから証明書を追加\)** から、**[証明書ファイルの選択]** ボタンをクリックします。 Azure ポータルからダウンロードした証明書ファイルを参照し選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. 証明書がトラストストアに追加されます。 証明書の別名に注意してください。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. **[ユーザー]** ページで、**[認証サービス]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. **[アカウント設定]**  ->  **[Create/Manage TrustStore]\(キーストアの作成および管理\)** を選択します。 パスワードを入力して、キーストアを作成します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. 管理画面に戻り、**[設定]**  ->  **[操作]**  ->  **[Web コンソール]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

17. [構成] ページが開きます。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

18. 「**Adobe Granite SAML 2.0 Authentication Handler**」で検索し、**[追加]** アイコンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. このページで、次の操作を実行します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. **[パス]** テキストボックスに、**/** と入力します。

    b. **[IDP URL]** テキストボックスに、Azure Portal からコピーした **SAML Sign-On Service URL** の値を貼り付けます。

    c. **[IDP Certificate Alias]\(IDP 証明書の別名\)** テキストボックスに、トラスト ストアに追加した**[Certificate Alias]\(証明書の別名\)** の値を入力します。

    d. **[Security Provided Entity ID]\(セキュリティ指定されたエンティティ ID\)** テキストボックスに、Azure Portal で構成した、**[SAML Entity ID]\(SAML エンティティ ID\)** の一意の値を入力します。

    e. **[Assertion Consumer Service URL]** テキストボックスに、Azure Portal で構成した、**[応答 URL]** の値を入力します。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[キーストアのパスワード]** テキストボックスに、キーストアに設定した **[パスワード]** を入力します。

    g. **[User Attribute ID]\(ユーザー属性 ID/)** テキストボックスに、**[名前 ID]** または関連するその他のユーザー ID を入力します。

    h. **[Autocreate CRX Users]\(CRX ユーザーの自動作成\)** を選択します。

    i. **[ログアウト URL]** テキストボックスに、Azure Portal からコピーした **[サインアウト URL]** の一意の値を入力します。

    j. **[保存]**

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Adobe Experience Manager テスト ユーザーの作成

このセクションでは、Adobe Experience Manager で Britta Simon というユーザーを作成します。 **[Autocreate CRX Users]\(CRX ユーザーの自動作成\)** オプションを選択した場合、認証成功後にユーザーが自動的に作成されます。 

ユーザーを手動で作成する場合は、[Adobe Experience Manager サポート チーム](https://helpx.adobe.com/support/experience-manager.html)に連絡して、Adobe Experience Manager プラットフォームにユーザーを追加してください。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Adobe Experience Manager へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Adobe Experience Manager に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Adobe Experience Manager]** を選択します。

    ![アプリケーションの一覧の [Adobe Experience Manager] リンク](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Adobe Experience Manager] タイルをクリックすると、自動的に Adobe Experience Manager アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_203.png

