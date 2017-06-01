---
title: "チュートリアル: Azure Active Directory と LinkedIn Lookup の統合 | Microsoft Docs"
description: "Azure Active Directory と LinkedIn Lookup の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a2757a39-1ead-4a3e-91e4-270be3055683
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: a4160a89c6a06222a9239d996fcca822e1a50382
ms.contentlocale: ja-jp
ms.lasthandoff: 05/18/2017


---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-lookup"></a>チュートリアル: Azure Active Directory と LinkedIn Lookup の統合

このチュートリアルでは、LinkedIn Lookup と Azure Active Directory (Azure AD) を統合する方法について説明します。

LinkedIn Lookup と Azure AD の統合には、次の利点があります。

- LinkedIn Lookup にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に LinkedIn Lookup にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

LinkedIn Lookup と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- LinkedIn Lookup でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの LinkedIn Lookup の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-linkedin-lookup-from-the-gallery"></a>ギャラリーからの LinkedIn Lookup の追加
Azure AD への LinkedIn Lookup の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LinkedIn Lookup を追加する必要があります。

**ギャラリーから LinkedIn Lookup を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[新しいアプリケーション]** ボタンをクリックして新しいアプリケーションを追加します。

    ![アプリケーション][3]

4. 検索ボックスに、「**LinkedIn Lookup**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_search.png)

5. 結果ウィンドウで **[LinkedIn Lookup]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、LinkedIn Lookup で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する LinkedIn Lookup ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと LinkedIn Lookup の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を LinkedIn Lookup の **[Username (ユーザー名)]** の値として割り当てます。

LinkedIn Lookup で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Lookup テスト ユーザーの作成](#creating-an-linkedin-lookup-test-user)** - Lookup で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にして、LinkedIn Lookup アプリケーションでシングル サインオンを構成します。

**LinkedIn Lookup で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal で、**LinkedIn Lookup** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure Single Sign-On][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択して、シングル サインオンを有効にします。
 
    ![Configure Single Sign-On](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_samlbase.png)

3. 別の Web ブラウザーのウィンドウで、管理者として **LinkedIn Lookup** の Web サイトにサインオンします。

4. **[Account Center (アカウント センター)]** で、**[Settings (設定)]** の下の **[Global Settings (グローバル設定)]** をクリックします。 また、ドロップダウン リストから **[Lookup]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_LinkedIn_admin_011.png)

5. **[OR Click Here to load and copy individual fields from the form (または、ここをクリックしてフォームから個々のフィールドを読み込み、コピーする)]** をクリックし、**[Entity Id (エンティティ ID)]** と **[Assertion Consumer Access (ACS) Url (Assertion Consumer Access (ACS) URL)]** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_LinkedIn_admin_032.png)

6. アプリケーションを **IDP** 開始モードで構成する場合は、Azure Portal の **[LinkedIn Lookup のドメインと URL]** セクションで次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_url.png)

    a. **[識別子]** テキストボックスに、LinkedIn ポータルからコピーした**エンティティ ID** を入力します 

    b. **[応答 URL]** テキストボックスに、LinkedIn ポータルからコピーした **Assertion Consumer Access (ACS) URL** を入力します

7. **SP** 開始モードでアプリケーションを構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_url1.png)

    **[サインオン URL]** ボックスに、`https://www.linkedIn.com/checkpoint/enterprise/login/<AccountId>?application=lookup` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これは実際の値ではないので注意してください。 この値を実際のシングル サインオン URL に置き換える必要があります。 この値を取得するには、[LinkedIn Lookup クライアント サポート チーム](https://business.LinkedIn.com/lookup)に問い合わせてください。

8. **LinkedIn Lookup** アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットは、例を示しています。 **[ユーザー識別子]** の既定値は **user.userprincipalname** ですが、LinkedIn Lookup はこれがユーザーの電子メール アドレスにマップされることを想定します。 一覧の **user.mail** 属性を使用するか、組織構成に基づいて適切な属性値を使用できます。 

    ![シングル サインオンの構成](./media/active-directory-saas-LinkedInlookup-tutorial/updateusermail.png)
    
9. **[ユーザー属性]** セクションで、**[その他のすべてのユーザー属性を表示および編集する]** をクリックし、属性を設定します。 **department** という別の要求を追加する必要があり、この値が **user.department** にマップされます。

    | 属性名 | 属性値 |
    | --- | --- |    
    | department| user.department |

   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-LinkedInlookup-tutorial/userattribute.png)

   a. **[属性の追加]** をクリックして属性の詳細ページを開き、以下のように department 属性を追加します。

   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-LinkedInlookup-tutorial/adduserattribute.png)
   
   b. **[OK]** をクリックして属性を保存します。

10. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_certificate.png) 

11. **[保存]** ボタンをクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_400.png)

12. **[LinkedIn Admin Settings (LinkedIn 管理者設定)]** セクションに移動します。 **[Upload XML file (XML ファイルのアップロード)]** オプションをクリックして、Azure ポータルからダウンロードした XML ファイルをアップロードします。

    ![Configure Single Sign-On](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedIn_metadata_03.png)

13. **[ON (オン)]** をクリックして SSO を有効にします。 SSO の状態が **[Not Connected (未接続)]** から **[Connected (接続済み)]** に変更されます

    ![シングル サインオンの構成](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedIn_admin_05.png)

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_02.png) 

3. **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_04.png) 

    a. **[Name]** ボックスに「**Britta Simon**」と入力します。

    b. **[ユーザー名]** ボックスに、Britta Simon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。
 
### <a name="creating-an-linkedin-lookup-test-user"></a>LinkedIn Lookup テスト ユーザーの作成

Linked Lookup アプリケーションでは、ジャストインタイム (JIT) ユーザー プロビジョニングがサポートされ、認証後にユーザーがアプリケーション内に自動的に作成されます。 **[Automatically assign licenses (ライセンスを自動的に割り当てる)]** をアクティブ化して、ユーザーにライセンスを割り当てます。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedin_admin_license.png)

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Linked Lookup へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を LinkedIn Lookup に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[LinkedIn Lookup]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [LinkedIn Lookup] タイルをクリックすると、個人用の LinkedIn アカウントの詳細を指定した [Organizational (組織)] ページにリダイレクトします。 個人アカウントが LinkedIn ビジネス アカウントにリンクされます。 

アクセス パネルの詳細については、[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_203.png


