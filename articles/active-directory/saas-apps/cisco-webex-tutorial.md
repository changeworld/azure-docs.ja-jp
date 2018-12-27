---
title: 'チュートリアル: Azure Active Directory と Cisco Webex の統合 | Microsoft Docs'
description: Azure Active Directory と Cisco Webex の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.openlocfilehash: 73e20afdcacec76482f8ebf01bf2cef2105912a6
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005523"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>チュートリアル: Azure Active Directory と Cisco Webex の統合

このチュートリアルでは、Cisco Webex と Azure Active Directory (Azure AD) を統合する方法について説明します。

Cisco Webex と Azure AD の統合には、次の利点があります。

- Cisco Webex にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Cisco Webex にサインオンできるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Cisco Webex と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Cisco Webex でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使わないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使わないでください。
- Azure AD の評価環境がない場合は、[1 か月の無料試用版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Cisco Webex の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="add-cisco-webex-from-the-gallery"></a>ギャラリーからの Cisco Webex の追加
Azure AD への Cisco Webex の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Cisco Webex を追加する必要があります。

**ギャラリーから Cisco Webex を追加するには、次の手順を実行します。**

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** アイコンを選択します。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに、「**Cisco Webex**」と入力します。 

5. 結果パネルから、**Cisco Webex** を選択します。 次に、**[追加]** ボタンを選択してアプリケーションを追加します。

    ![結果リストの Cisco Webex](./media/cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Cisco Webex で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Cisco Webex ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Cisco Webex の関連ユーザーの間で、リンクが確立されている必要があります。

Cisco Webex で、**Username** の値を、Azure AD の**ユーザー名**と同じ値に指定します。 これで、2 人のユーザー間にリンクが確立されました。 

Cisco Webex で Azure AD のシングル サインオンを構成してテストするには、次の要素を完了します。

1. [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
2. [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
3. [Cisco Webex のテスト ユーザーの作成](#create-a-cisco-webex-test-user) - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Cisco Webex で作成します。
4. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. [シングル サインオンのテスト](#test-single-sign-on)。構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Cisco Webex アプリケーションでシングル サインオンを構成します。

**Cisco Webex で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Cisco Webex** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク][4]

2. シングル サインオンを有効にするには、**[シングル サインオン]** ダイアログ ボックスの **[モード]** ドロップダウン リストで **[SAML ベースのサインオン]** を選択します。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. 別の Web ブラウザー ウィンドウで、Cisco Webex 企業サイトに管理者としてサインインします。

4. メニューの左側の **[Settings]\(設定\)** をクリックします。

    ![Configure single sign-on](./media/cisco-webex-tutorial/tutorial_cisco_webex_10.png)

5. [Settings]\(設定\) ページで、**[Authentication]\(認証\)** セクションまでスクロールし、**[Modify]\(変更\)** をクリックします。

    ![Configure single sign-on](./media/cisco-webex-tutorial/tutorial_cisco_webex_14.png)

6. **[Integrate a 3rd-party identity provider.(Advanced)] \(サード パーティの ID プロバイダーを統合する。(詳細))** を選択して、次の画面に移動します。

    ![Configure single sign-on](./media/cisco-webex-tutorial/tutorial_cisco_webex_15.png)

7. **[Export Directory Metadata]\(ディレクトリ メタデータのエクスポート\)** ページで、**[Download Metadata File]\(メタデータ ファイルのダウンロード\)** をクリックしてメタデータ ファイルをダウンロードします。

    ![Configure single sign-on](./media/cisco-webex-tutorial/tutorial_cisco_webex_16.png)

8. Azure Portal で、**[Cisco Webex のドメインと URL]** セクションで、ダウンロードした**サービス プロバイダー メタデータ ファイル**をアップロードし、次の手順を実行してアプリケーションを構成します。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![[Cisco Webex のドメインと URL] のシングル サインオン情報](./media/cisco-webex-tutorial/tutorial_ciscowebex_upload.png)

    b. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、**[アップロード]** をクリックします。

    ![[Cisco Webex のドメインと URL] のシングル サインオン情報](./media/cisco-webex-tutorial/tutorial_ciscowebex_uploadconfig.png)

    c. **サービス プロバイダー メタデータ ファイル**のアップロードが正常に完了すると、次のように、**識別子**と**応答 URL** の値が **[Cisco Webex のドメインと URL]** セクションのテキスト ボックスに自動的に入力されます。

    ![[Cisco Webex のドメインと URL] のシングル サインオン情報](./media/cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    d. **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.webex.com/` というパターンで URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 この値を実際のシングルサインオン URL に置き換えてください。 これらの値を取得するには、[Cisco Webex クライアント サポート チーム](https://www.webex.co.in/support/support-overview.html)に問い合わせてください。

9. Cisco Webex アプリケーションは SAML アサーションを使用し、特定の属性を含みます。 このアプリケーションには、次の属性を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![Configure single sign-on](./media/cisco-webex-tutorial/tutorial_ciscowebex_07.png) 

10. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    |  属性名  | 属性値 |
    | --------------- | -------------------- |    
    |   firstname    | User.givenname |
    |   lastname    | User.surname |
    |   uid    | User.mail |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/cisco-webex-tutorial/tutorial_attribute_04.png)

    ![Configure single sign-on](./media/cisco-webex-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]** をクリックします。

11. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** を選択し、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

12. **[保存]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/cisco-webex-tutorial/tutorial_general_400.png)
    
13. Cisco Webex 企業サイトの管理者ページで、ファイル ブラウザー オプションを使用して Azure AD メタデータファイルを探してアップロードします。 次に、**[Require certificate signed by a certificate authority in Metadata (more secure)]\(証明機関の署名付き証明書がメタデータに必要 (高セキュリティ)\)** を選択し、次の画面に進みます。 

    ![Configure single sign-on](./media/cisco-webex-tutorial/tutorial_cisco_webex_11.png)

14. **[Test SSO Connection (SSO 接続のテスト)]** を選択し、ブラウザーの新しいタブが開いたら、サインインして Azure AD で認証します。

15. ブラウザーの**Cisco Cloud Collaboration Management** タブに戻ります。テストが成功した場合は、**This test was successful.Enable Single Sign-On option\(このテストは正常に完了しました。シングル サインオン オプションを有効にします\)** を選択し、**Save\(保存\)** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンを選択します。

    ![Azure Active Directory のボタン](./media/cisco-webex-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** を選びます。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/cisco-webex-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** を選択します。

    ![[追加] ボタン](./media/cisco-webex-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/cisco-webex-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
 
### <a name="create-a-cisco-webex-test-user"></a>Cisco Webex テスト ユーザーの作成

このセクションの目的は、Cisco Webex で Britta Simon というユーザーを作成することです。 Cisco Webex は、ジャストイン タイム プロビジョニングと自動ユーザー プロビジョニングをサポートします (これらは既定で有効です)。 自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)をご覧ください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon というユーザーに Cisco Webex へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Cisco Webex に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure ポータルで、アプリケーション ビューを開きます。 次に、ディレクトリ ビューに移動し、**[エンタープライズ アプリケーション]** に移動します。  

2. **[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201] 

3. アプリケーションの一覧で **[Cisco Webex]** を選択します。

    ![アプリケーションの一覧の [Cisco Webex] リンク](./media/cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンを選びます。 次に、**[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログ ボックスで、**[ユーザー]** 一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** ボタンをクリックします。

7. **[割り当ての追加]** ダイアログ ボックスで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Cisco Webex] タイルを選ぶと、自動的に Cisco Webex アプリケーションにサインオンします。

アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/cisco-webex-tutorial/tutorial_general_203.png

