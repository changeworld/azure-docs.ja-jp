---
title: 'チュートリアル: Azure Active Directory と SmartLPA の統合 | Microsoft Docs'
description: Azure Active Directory と SmartLPA の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ed5f6580-bee6-4f44-aceb-f8f7feda3fb8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2018
ms.author: jeedes
ms.openlocfilehash: eedec7de74f3bdd7ae43d1b1eb0decd5fe83ebf6
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075586"
---
# <a name="tutorial-azure-active-directory-integration-with-smartlpa"></a>チュートリアル: Azure Active Directory と SmartLPA の統合

このチュートリアルでは、SmartLPA と Azure Active Directory (Azure AD) を統合する方法について説明します。

SmartLPA と Azure AD の統合には、次の利点があります。

- SmartLPA にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に SmartLPA にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

SmartLPA と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SmartLPA のシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SmartLPA の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-smartlpa-from-the-gallery"></a>ギャラリーからの SmartLPA の追加
Azure AD への SmartLPA の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に SmartLPA を追加する必要があります。

**ギャラリーから SmartLPA を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**SmartLPA**」と入力し、結果ウィンドウで **SmartLPA** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の SmartLPA](./media/smartlpa-tutorial/tutorial_smartlpa_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SmartLPA で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SmartLPA ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SmartLPA の関連ユーザーの間で、リンク関係が確立されている必要があります。

SmartLPA で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SmartLPA のテスト ユーザーの作成](#create-a-smartlpa-test-user)** - SmartLPA で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、SmartLPA アプリケーションでシングル サインオンを構成します。

**SmartLPA で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SmartLPA** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/smartlpa-tutorial/tutorial_smartlpa_samlbase.png)

3. **[SmartLPA のドメインと URL]** セクションで、次の手順に従います。

    ![[SmartLPA のドメインと URL] のシングル サインオン情報](./media/smartlpa-tutorial/tutorial_smartlpa_url.png)

    a. **[サインオン URL]** ボックスに、`https://<TENANTNAME>.smartlpa.com/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<TENANTNAME>.smartlpa.com/<UNIQUE ID>` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[SmartLPA クライアント サポート チーム](mailto:support@smartlpa.com)に問い合わせてください。

4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/smartlpa-tutorial/tutorial_smartlpa_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/smartlpa-tutorial/tutorial_general_400.png)

6. **[SmartLPA 構成]** セクションで、**[SmartLPA の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![SmartLPA の構成](./media/smartlpa-tutorial/tutorial_smartlpa_configure.png) 

7. **SmartLPA** 側でシングル サインオンを構成するには、ダウンロードした**証明書 (Base64)**、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** を [SmartLPA サポート チーム](mailto:support@smartlpa.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/smartlpa-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/smartlpa-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/smartlpa-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/smartlpa-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-smartlpa-test-user"></a>SmartLPA テスト ユーザーを作成する

このセクションでは、SmartLPA で Britta Simon というユーザーを作成します。 [SmartLPA サポート チーム](mailto:support@smartlpa.com)と連携し、SmartLPA プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SmartLPA へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**SmartLPA に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[SmartLPA]** を選択します。

    ![アプリケーションの一覧の SmartLPA のリンク](./media/smartlpa-tutorial/tutorial_smartlpa_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SmartLPA] タイルをクリックすると、自動的に SmartLPA アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/smartlpa-tutorial/tutorial_general_01.png
[2]: ./media/smartlpa-tutorial/tutorial_general_02.png
[3]: ./media/smartlpa-tutorial/tutorial_general_03.png
[4]: ./media/smartlpa-tutorial/tutorial_general_04.png

[100]: ./media/smartlpa-tutorial/tutorial_general_100.png

[200]: ./media/smartlpa-tutorial/tutorial_general_200.png
[201]: ./media/smartlpa-tutorial/tutorial_general_201.png
[202]: ./media/smartlpa-tutorial/tutorial_general_202.png
[203]: ./media/smartlpa-tutorial/tutorial_general_203.png

