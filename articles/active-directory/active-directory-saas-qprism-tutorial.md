---
title: "チュートリアル: Azure Active Directory と QPrism の統合 | Microsoft Docs"
description: "Azure Active Directory と QPrism の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: jeedes
ms.openlocfilehash: a380e9c07710e0c79a44e036f9c5bd72a731fe40
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>チュートリアル: Azure Active Directory と QPrism の統合

このチュートリアルでは、QPrism と Azure Active Directory (Azure AD) を統合する方法について説明します。

QPrism と Azure AD の統合には、次の利点があります。

- QPrism にアクセスするユーザーを Azure AD で制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に QPrism にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

QPrism と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- QPrism でのシングル サインオンが有効なサブスクリプション

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの QPrism の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="add-qprism-from-the-gallery"></a>ギャラリーから QPrism を追加する
Azure AD への QPrism の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に QPrism を追加する必要があります。

**ギャラリーから QPrism を追加するには**

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**[Azure Active Directory]** を選択します。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**QPrism**」と入力し、結果パネルから **[QPrism]** を選択します。 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の QPrism](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、QPrism で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する QPrism ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと QPrism の関連ユーザーの間にリンク関係が存在する必要があります。

この関係を確立するには、QPrism で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当てます。

Clarizen で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了します。

1. [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)- ユーザーがこの機能を使用できるようにします。
2. [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)- Britta Simon で Azure AD のシングル サインオンをテストします。
3. [QPrism テスト ユーザーの作成](#create-a-qprism-test-user)- QPrism で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)- Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. [シングル サインオンのテスト](#test-single-sign-on) - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、QPrism アプリケーションでシングル サインオンを構成します。

1. Azure Portal の **QPrism** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログ ボックスで、**[モード]** として **[SAML ベースのサインオン]** を選択して、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_samlbase.png)

3. **[QPrism のドメインと URL]** セクションで、次の手順を実行します。

    ![[QPrism のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_url.png)

    a. **[サインオン URL]** ボックスに、`https://<customer domain>.qmyzone.com/login` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://<customer domain>.qmyzone.com/metadata.php` という形式で URL を入力します。
         
    > [!NOTE] 
    > これらは実際の値ではありません。 これらの値を実際の識別子とサインオン URL で更新してください。 これらの値を取得するには、[QPrism クライアント サポート チーム](mailto:qsupport-ce@quatrro.com)にお問い合わせください。 

4. **メタデータ URL** を生成するには、次の手順を実行します。

    a. **[アプリの登録]** を選択します。
    
    ![アプリの登録のシングル サインオンの構成](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appregistrations.png)
   
    b. **[エンドポイント]** を選択して **[エンドポイント]** ダイアログ ボックスを開きます。  
    
    ![シングル サインオン エンドポイントの構成](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpointicon.png)

    c. コピー ボタンを選択して、**フェデレーション メタデータ ドキュメント**の URL をコピーしてメモ帳に貼り付けます。
    
    ![シングル サインオン エンドポイントの構成](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpoint.png)
     
    d. 次に、**QPrism** のプロパティ ページに移動し、**[コピー]** を使用して **[アプリケーション ID]** をコピーして、 メモ帳に貼り付けます。
 
    ![アプリケーション ID のシングル サインオンの構成](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appid.png)

    e. 次のパターンを使用して**メタデータ URL** を生成します。`<FEDERATION METADATA DOCUMENT url>?appid=<application id>` 

5. **[保存]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-qprism-tutorial/tutorial_general_400.png)
    
6. **QPrism** 側でシングル サインオンを構成するために、**メタデータ URL** を [QPrism サポート チーム](mailto:qsupport-ce@quatrro.com)に送信します。 サポート チームによって、SAML シングル サインオン接続が両方の側で正しく設定されたことが保証されます。

> [!TIP]
> アプリのセットアップ中、[Azure ポータル](https://portal.azure.com)内で上記の手順の簡易版を確認できるようになりました。 **[Active Directory]** > **[エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブを選択し、一番下の **[構成]** セクションから組み込みドキュメントにアクセスします。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD にテスト ユーザーを作成するには**

1. Azure Portal の左側のウィンドウで、**[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](./media/active-directory-saas-qprism-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-qprism-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** を選択します。

    ![[追加] ボタン](./media/active-directory-saas-qprism-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-qprism-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **[作成]**を選択します。
 
### <a name="create-a-qprism-test-user"></a>QPrism テスト ユーザーの作成

このセクションでは、QPrism で Britta Simon というユーザーを作成します。 [QPrism サポート チーム](mailto:qsupport-ce@quatrro.com)と連携して、QPrism プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に QPrism へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を QPrism に割り当てるには**

1. Azure ポータルで、アプリケーション ビューを開き、ディレクトリ ビューに移動します。 **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[QPrism]** を選択します。

    ![アプリケーションの一覧の QPrism のリンク](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]**を選択します。 **[割り当ての追加]** で **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログ ボックスで、**[ユーザー]** の一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** を選択します。

7. **[割り当ての追加]**で **[割り当て]** を選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [QPrism] タイルをクリックすると、QPrism アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_203.png

