---
title: 'チュートリアル: Azure Active Directory と Adobe Experience Manager の統合 | Microsoft Docs'
description: Azure Active Directory と Adobe Experience Manager の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: 56b392e57809cea0ae93800df39bb9dacd164ce2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054184"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>チュートリアル: Azure Active Directory と Adobe Experience Manager の統合

このチュートリアルでは、Adobe Experience Manager と Azure Active Directory (Azure AD) を統合する方法について説明します。

Adobe Experience Manager と Azure AD を統合すると、次の利点があります。

- Adobe Experience Manager にどの Azure AD ユーザーがアクセスできるかを管理できます。
- ユーザーが自分の Azure AD アカウントで自動的に Adobe Experience Manager にサインオンできるように設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Adobe Experience Manager と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Adobe Experience Manager でのシングル サインオンが有効に設定されたサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使わないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使わないでください。
- Azure AD の評価環境がない場合は、[1 か月の無料試用版を入手します](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Adobe Experience Manager を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="add-adobe-experience-manager-from-the-gallery"></a>ギャラリーから Adobe Experience Manager を追加する
Azure AD と Adobe Experience Manager の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Adobe Experience Manager を追加する必要があります。

**ギャラリーから Adobe Experience Manager を追加するには、次の手順を実行します。**

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** アイコンを選択します。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン][3]

4. [検索] ボックスに、「**Adobe Experience Manager**」と入力します。 結果パネルから **[Adobe Experience Manager]** を選択し、**[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果一覧の Adobe Experience Manager](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーを使用して、Adobe Experience Manager で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Adobe Experience Manager ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Adobe Experience Manager の関連ユーザーの間で、リンクが確立されている必要があります。

Adobe Experience Manager で、**ユーザー名**の値に、Azure AD の**ユーザー名**と同じ値を指定します。 これで、2 人のユーザー間にリンクが確立されました。 

Adobe Experience Manager で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了します。

1. [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
2. [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
3. [Adobe Experience Manager のテスト ユーザーの作成](#create-an-adobe-experience-manager-test-user) - Adobe Experience Manager で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
4. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. [シングル サインオンのテスト](#test-single-sign-on) - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にして、Adobe Experience Manager アプリケーションでシングル サインオンを構成します。

**Adobe Experience Manager で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Adobe Experience Manager** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク][4]

2. シングル サインオンを有効にするには、**[シングル サインオン]** ダイアログ ボックスの **[モード]** ドロップダウン メニューで **[SAML ベースのサインオン]** を選択します。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. **[Adobe Experience Manager Domain and URL]\(Adobe Experience Manager のドメインと URL\)** セクションで、**IDP モード**でアプリケーションを構成する場合は、次の手順を実行します。

    ![Adobe Experience Manager のドメインと URL のシングル サインオン情報](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. **[識別子]** ボックスに、AEM のサーバーにも定義する一意の値を入力します。 

    b. **[応答 URL]** ボックスに、`https://<AEM Server Url>/saml_login` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 この値を取得するには、[Adobe Experience Manager サポート チーム](https://helpx.adobe.com/support/experience-manager.html)にお問い合わせください。
 
4. **[詳細な URL 設定の表示]** をクリックします。 **SP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![Adobe Experience Manager のドメインと URL のシングル サインオン情報](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    **[サインオン URL]** ボックスに、Adobe Experience Manager サーバーの URL を入力します。 

5. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** を選択します。 コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. [Adobe Experience Manager Configuration]\([Adobe Experience Manager の構成]\) セクションで構成のサインオン ウィンドウを開くには **[Configure Adobe Experience Manager]\([Adobe Experience Manager を構成する]\)** をクリックします。 [クイック リファレンス] セクションから、**[SAML シングル サインオン サービス URL]**、**[SAML Entity ID]\(SAML エンティティ ID\)**、および **[Sign-Out ID]\(サインアウト ID\)** をコピーします。

    ![構成セクションのリンク](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. **[保存]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. 別のブラウザー ウィンドウで **Adobe Experience Manager** の管理ポータルを開きます。

9. **[設定]**  >  **[セキュリティ]**  >  **[ユーザー]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. **[管理者]** またはその他の関連するユーザーを選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. **[アカウント設定]** > **[Manage TrustStore]\(トラストストアの管理\)** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. **[Add Certificate from CER file]\(CER ファイルから証明書を追加\)** で、**[証明書ファイルの選択]** をクリックします。 Azure Portal から既にダウンロードしている証明書ファイルを参照し選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. 証明書がトラストストアに追加されます。 証明書の別名に注意してください。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. **[ユーザー]** ページで、**[認証サービス]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. **[アカウント設定]**  >  **[Create/Manage TrustStore]\(キーストアの作成および管理\)** を選択します。 パスワードを入力して、キーストアを作成します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. 管理画面に戻ります。 **[設定]** > **[Operations]\(操作\)** > **[Web Console]\(Web コンソール\)** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    [構成] ページが開きます。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

17. 「**Adobe Granite SAML 2.0 Authentication Handler**」で検索します。 次に、**[追加]** アイコンを選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. このページで、次の操作を実行します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. **[パス]** ボックスに、「**/**」と入力します。

    b. **[IDP URL]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を入力します。

    c. **[IDP Certificate Alias]\(IDP 証明書の別名\)** ボックスに、トラストストアに追加した**証明書の別名**の値を入力します。

    d. **[Security Provided Entity ID]\(セキュリティ指定されたエンティティ ID\)** ボックスに、Azure Portal で構成した、**[SAML Entity ID]\(SAML エンティティ ID\)**  の一意の値を入力します。

    e. **[Assertion Consumer Service URL]** ボックスに、Azure Portal で構成した **[応答 URL]** の値を入力します。

    f. **[キーストアのパスワード]** ボックスに、キーストアに設定した **[パスワード]** を入力します。

    g. **[User Attribute ID]\(ユーザー属性 ID\)** ボックスに、**[名前 ID]** または関連するその他のユーザー ID を入力します。

    h. **[Autocreate CRX Users]\(CRX ユーザーの自動作成\)** を選択します。

    i. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure Portal から取得した**サインアウト URL** の値を入力します。

    j. **[保存]** を選択します。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。 このアプリを追加した後、**[Active Directory]** > **[エンタープライズ アプリケーション]** セクションで、**[シングル サインオン]** タブを選びます。次に、下部の **[構成]** セクションから組み込みドキュメントにアクセスします。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンを選択します。

    ![Azure Active Directory のボタン](./media/adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** を選びます。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** を選択します。

    ![[追加] ボタン](./media/adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスを選択します。 **[パスワード]** ボックスに表示された値を書き留めます。

    d. **[作成]** を選択します。
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Adobe Experience Manager テスト ユーザーの作成

このセクションでは、Adobe Experience Manager で Britta Simon というユーザーを作成します。 **[Autocreate CRX Users]\(CRX ユーザーの自動作成\)** オプションを選択した場合、認証成功後にユーザーが自動的に作成されます。 

ユーザーを手動で作成する場合は、[Adobe Experience Manager サポート チーム](https://helpx.adobe.com/support/experience-manager.html)に連絡して、Adobe Experience Manager プラットフォームにユーザーを追加してください。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Adobe Experience Manager へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Adobe Experience Manager に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure ポータルで、アプリケーション ビューを開きます。 ディレクトリ ビューに移動し、**[エンタープライズ アプリケーション]** を選択した後、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Adobe Experience Manager]** を選択します。

    ![アプリケーションの一覧の [Adobe Experience Manager] リンク](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンを選びます。 次に、**[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** ボタンをクリックします。

7. **[割り当ての追加]** ダイアログ ボックスで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Adobe Experience Manager] タイルを選択すると、自動的に Adobe Experience Manager アプリケーションにサインインします。

アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/adobeexperiencemanager-tutorial/tutorial_general_203.png

