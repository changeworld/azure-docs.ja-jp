---
title: 'チュートリアル: Azure Active Directory と Convene の統合 | Microsoft Docs'
description: Azure Active Directory と Convene の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2540ac46-1aea-496b-a8c5-575a2690f7db
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: jeedes
ms.openlocfilehash: 6ff502f46cfd96b767191d11e3fcc53eb1be81d2
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2018
ms.locfileid: "45607322"
---
# <a name="tutorial-azure-active-directory-integration-with-convene"></a>チュートリアル: Azure Active Directory と Convene の統合

このチュートリアルでは、Convene と Azure Active Directory (Azure AD) を統合する方法について説明します。

Convene と Azure AD の統合には、次の利点があります。

- Convene にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Convene にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Convene と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Convene シングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Convene の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-convene-from-the-gallery"></a>ギャラリーからの Convene の追加
Azure AD への Convene の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Convene を追加する必要があります。

**ギャラリーから Convene を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Convene**」と入力し、結果パネルで **Convene** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Convene](./media/convene-tutorial/tutorial_convene_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Convene で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Convene ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Convene の関連ユーザーの間で、リンク関係が確立されている必要があります。

Convene で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Convene テスト ユーザーの作成](#create-a-convene-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Convene で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Convene アプリケーションでシングル サインオンを構成します。

**Convene で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Convene** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/convene-tutorial/tutorial_convene_samlbase.png)

3. アプリケーションを **IDP** 開始モードで構成する場合は、**[Convene Domain and URLs]/(Convene のドメインと URL/)** セクションで次の手順を実行します。

    ![[Convene Domain and URLs]/(Convene のドメインと URL/) のシングル サインオン情報](./media/convene-tutorial/tutorial_convene_url.png)

    **[応答 URL]** ボックスに、`https://portal.convene.me.uk/saml/acs/<UID>` のパターンを使用して URL を入力します。

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Convene Domain and URLs]/(Convene のドメインと URL/) のシングル サインオン情報](./media/convene-tutorial/tutorial_convene_url1.png)

    **[サインオン URL]** ボックスに、「`https://portal.convene.me.uk/login`」と入力します。
     
    > [!NOTE] 
    > 応答 URL 値は、実際の値ではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[Convene クライアント サポート チーム](mailto:support@convene.me.uk)に問い合わせてください。

5. Convene アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![シングル サインオンの構成の属性](./media/convene-tutorial/tutorial_convene_attribute.png)

6. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/convene-tutorial/tutorial_convene_certificate.png) 

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/convene-tutorial/tutorial_general_400.png)
    
8. **[Convene Configuration]\(Convene 構成\)** セクションで、**[Configure Convene]\(Convene の構成\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Convene 構成](./media/convene-tutorial/tutorial_convene_configure.png) 

9. **Convene** 側でシングル サインオンを構成するには、ダウンロードした**証明書 (Base64)**、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** を [Convene サポート チーム](mailto:support@convene.me.uk)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/convene-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/convene-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/convene-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/convene-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-convene-test-user"></a>Convene テスト ユーザーの作成

このセクションの目的は、Convene で Britta Simon というユーザーを作成することです。 Convene では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Convene ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Convene サポート チーム](mailto:support@convene.me.uk)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Convene へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Convene に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Convene]** を選択します。

    ![アプリケーションの一覧の Convene のリンク](./media/convene-tutorial/tutorial_convene_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Convene] タイルをクリックすると、自動的に Convene アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/convene-tutorial/tutorial_general_01.png
[2]: ./media/convene-tutorial/tutorial_general_02.png
[3]: ./media/convene-tutorial/tutorial_general_03.png
[4]: ./media/convene-tutorial/tutorial_general_04.png

[100]: ./media/convene-tutorial/tutorial_general_100.png

[200]: ./media/convene-tutorial/tutorial_general_200.png
[201]: ./media/convene-tutorial/tutorial_general_201.png
[202]: ./media/convene-tutorial/tutorial_general_202.png
[203]: ./media/convene-tutorial/tutorial_general_203.png

