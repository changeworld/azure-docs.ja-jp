---
title: 'チュートリアル: Azure Active Directory と Clever の統合 | Microsoft Docs'
description: Azure Active Directory と Clever の間でシングル サインオンを構成する方法について確認します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 069ff13a-310e-4366-a147-d6ec5cca12a5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 483d03fcc72e0a93111d10b0221164459de27d12
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431863"
---
# <a name="tutorial-azure-active-directory-integration-with-clever"></a>チュートリアル: Azure Active Directory と Clever の統合

このチュートリアルでは、Clever と Azure Active Directory (Azure AD) を統合する方法について説明します。

Clever と Azure AD の統合には、次の利点があります。

- Clever にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Clever にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Clever の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Clever でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Clever の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-clever-from-the-gallery"></a>ギャラリーからの Clever の追加
Azure AD への Clever の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Clever を追加する必要があります。

**ギャラリーから Clever を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Clever**」と入力し、結果ウィンドウで **Clever** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Clever](./media/clever-tutorial/tutorial_clever_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Clever で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Clever ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Clever の関連ユーザーの間で、リンク関係が確立されている必要があります。

Clever で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Clever で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Clever のテスト ユーザーの作成](#create-a-clever-test-user)** - Clever で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Clever アプリケーションでシングル サインオンを構成します。

**Clever で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Clever** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/clever-tutorial/tutorial_clever_samlbase.png)

1. **[Clever のドメインと URL]** セクションで、次の手順を実行します。

    ![[Clever のドメインと URL] のシングル サインオン情報](./media/clever-tutorial/tutorial_clever_url.png)

    a. **[サインオン URL]** ボックスに、`https://clever.com/in/<companyname>` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに次の URL を入力します。`https://clever.com/oauth/saml/metadata.xml`

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Clever クライアント サポート チーム](https://clever.com/about/contact/)にお問い合わせください。

1. **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーし、メモ帳に貼り付けます。
    
    ![Configure single sign-on](./media/clever-tutorial/tutorial_metadataurl.png)

1. Clever アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを **SAML トークン属性** の構成に追加する必要があります。

    次のスクリーンショットはその例です。

    ![Configure single sign-on](./media/clever-tutorial/tutorial_clever_07.png)

1. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名  | 属性値 |
    | --------------- | -------------------- |
    | clever.teacher.credentials.district_username|user.userprincipalname|
    | clever.student.credentials.district_username| user.userprincipalname |
    | Firstname  | User.givenname |
    | Lastname  | User.surname |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/clever-tutorial/tutorial_attribute_04.png)
    
    ![Configure single sign-on](./media/clever-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[Namespace]\(名前空間\)** ボックスは空白のままにします。
    
    d. **[OK]** をクリックします。
    
1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/clever-tutorial/tutorial_general_400.png)

1. 別の Web ブラウザー ウィンドウで、Clever 企業サイトに管理者としてログインします。

1. ツールバーで、 **[インスタント ログイン]** をクリックします。

    ![Instant Login](./media/clever-tutorial/ic798984.png "Instant Login")

    > [!NOTE]
    > シングル サインオンをテストする前に、[Clever Client サポート チーム](https://clever.com/about/contact/)に連絡して、バック エンドで Office 365 SSO を有効にする必要があります。

1. **[インスタント ログイン]** ページで、次の手順を実行します。
    
      ![Instant Login](./media/clever-tutorial/ic798985.png "Instant Login")
    
      a. **ログイン URL**を入力します。
    
      >[!NOTE]
      >**ログイン URL** はカスタム値です。 この値を取得するには、[Clever クライアント サポート チーム](https://clever.com/about/contact/)にお問い合わせください。
    
      b. **[ID システム]** として、**[ADFS]** を選択します。

      c. **[メタデータ URL]** テキスト ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** 値を貼り付けます。
    
      d. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/clever-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/clever-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/clever-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/clever-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-a-clever-test-user"></a>Clever テスト ユーザーの作成

Azure AD ユーザーが Clever にログインできるようにするには、そのユーザーを Clever にプロビジョニングする必要があります。

Clever の場合は、[Clever クライアント サポート チーム](https://clever.com/about/contact/)と協力して、Clever プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

>[!NOTE]
>Clever から提供されている他の Clever ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Clever へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Clever に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

1. アプリケーションの一覧で **[Clever]** を選択します。

    ![アプリケーションの一覧の Clever のリンク](./media/clever-tutorial/tutorial_clever_app.png)

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Clever のタイルをクリックすると、Clever アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/clever-tutorial/tutorial_general_01.png
[2]: ./media/clever-tutorial/tutorial_general_02.png
[3]: ./media/clever-tutorial/tutorial_general_03.png
[4]: ./media/clever-tutorial/tutorial_general_04.png

[100]: ./media/clever-tutorial/tutorial_general_100.png

[200]: ./media/clever-tutorial/tutorial_general_200.png
[201]: ./media/clever-tutorial/tutorial_general_201.png
[202]: ./media/clever-tutorial/tutorial_general_202.png
[203]: ./media/clever-tutorial/tutorial_general_203.png

