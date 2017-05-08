---
title: "チュートリアル: Azure Active Directory と EasyTerritory の統合 | Microsoft Docs"
description: "Azure Active Directory と EasyTerritory の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d29b362d-e986-4f67-8ff2-e158e49353aa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 64131f85cceb463bdd91cec40c2f272ece773691
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-easyterritory"></a>チュートリアル: Azure Active Directory と EasyTerritory の統合

このチュートリアルでは、EasyTerritory と Azure Active Directory (Azure AD) を統合する方法について説明します。

EasyTerritory と Azure AD の統合には、次の利点があります。

- EasyTerritory にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが各自の Azure AD アカウントで EasyTerritory に自動的にシングル サインオン (SSO) できるようにすることが可能です。
- 1 つの中央サイト (Microsoft Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

EasyTerritory と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- EasyTerritory でのシングル サインオン (SSO) が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
>
>

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月](https://azure.microsoft.com/pricing/free-trial/)の試用版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD SSO をテストします。 

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの EasyTerritory の追加
2. Azure AD SSO の構成とテスト

## <a name="adding-easyterritory-from-the-gallery"></a>ギャラリーからの EasyTerritory の追加
Azure AD への EasyTerritory の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に EasyTerritory を追加する必要があります。

**ギャラリーから EasyTerritory を追加するには、次の手順を実行します。**

1. **[Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**EasyTerritory**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_001.png)

5. 結果ウィンドウで **[EasyTerritory]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_0001.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、EasyTerritory で Azure AD の SSO を構成し、テストします。

SSO を機能させるには、Azure AD ユーザーに対応する EasyTerritory ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと EasyTerritory の関連ユーザーの間でリンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を EasyTerritory の **[Username]** の値として割り当てます。

EasyTerritory で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[EasyTerritory テスト ユーザーの作成](#creating-an-easyterritory-test-user)** - EasyTerritory で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure 管理ポータルで Azure AD のシングル サインオンを有効にし、EasyTerritory アプリケーションで SSO を構成します。

**EasyTerritory で Azure AD の SSO を構成するには、次の手順を実行します。**

1. Azure 管理ポータルの **EasyTerritory** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、SSO を有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_01.png)

3. **[EasyTerritory のドメインと URL]** セクションで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_02.png)
   1. **[識別子]** ボックスに、`https://apps.easyterritory.com/<tenant id>/DEV/` の形式で URL を入力します。
   2. **[応答 URL]** ボックスに、`https://apps.easyterritory.com/<tenant id>/DEV/AuthServices/Acs` のパターンを使用して URL を入力します。
    
4. **SP 開始モード**でアプリケーションを構成する場合は、**[EasyTerritory のドメインと URL]** セクションで次の手順を実行します。
    
    ![Configure Single Sign-On](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_03.png)
  1. **[詳細な URL 設定の表示]** オプションをクリックします。
  2. **[サインオン URL]** ボックスに、`https://<company name>.easyterritory.com/` のパターンを使用して URL を入力します。

    >[!NOTE] 
    >これらは実際の値ではありません。 実際のサインオン URL、識別子、応答 URL で値を更新する必要があります。[EasyTerritory サポート チーム](mailto:sales@easyterritory.com)に連絡してこれらの値を入手してください。

5. **[SAML 署名証明書]** セクションで、**[新しい証明書の作成]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_04.png)     

6. **[新しい証明書の作成]** ダイアログで、カレンダー アイコンをクリックし、**期限日**を選択します。 **[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-easyterritory-tutorial/tutorial_general_300.png)

7. **[SAML 署名証明書]** セクションで、**[Make new certificate active (新しい証明書を有効にする)]** をクリックし、**[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_05.png)

8. ポップアップ表示される **[Rollover certificate (ロール オーバー証明書)]** ウィンドウで、**[OK]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-easyterritory-tutorial/tutorial_general_400.png)

9. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_06.png) 

10. アプリケーション用に構成された SSO を入手するには、[EasyTerritory サポート チーム](mailto:sales@easyterritory.com)に連絡し、ダウンロードした**メタデータ**を提供してください。 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Microsoft Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_04.png) 
 1. **[名前]** ボックスに「**BrittaSimon**」と入力します。
 2. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。
 3. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。
 4. **[作成]**をクリックします。 

### <a name="create-an-easyterritory-test-user"></a>EasyTerritory テスト ユーザーの作成

このセクションでは、EasyTerritory で Britta Simon というユーザーを作成します。 [EasyTerritory サポート チーム](mailto:sales@easyterritory.com)と連携して、EasyTerritory プラットフォームにユーザーを追加してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に EasyTerritory へのアクセスを許可することで、このユーザーが Azure SSO を使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を EasyTerritory に割り当てるには、次の手順を実行します。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[EasyTerritory]** を選択します。

    ![Configure Single Sign-On](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_50.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD の SSO 構成をテストします。

アクセス パネルで [EasyTerritory] タイルをクリックすると、自動的に EasyTerritory アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_203.png
