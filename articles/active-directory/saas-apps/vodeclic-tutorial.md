---
title: 'チュートリアル: Azure Active Directory と Vodeclic の統合 | Microsoft Docs'
description: Azure Active Directory と Vodeclic の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: fb985b389139bfd8d54e6c54d101bbfa8a68a6d4
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444625"
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>チュートリアル: Azure Active Directory と Vodeclic の統合

このチュートリアルでは、Vodeclic と Azure Active Directory (Azure AD) を統合する方法について説明します。

Vodeclic と Azure AD の統合には、次の利点があります。

- Vodeclic にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Vodeclic にシングル サインオン (SSO) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Vodeclic と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Vodeclic SSO が有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使わないでください。
- Azure AD の評価環境がない場合は、[1 か月の無料試用版を入手します](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Vodeclic の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="add-vodeclic-from-the-gallery"></a>ギャラリーからの Vodeclic の追加
Azure AD への Vodeclic の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Vodeclic を追加する必要があります。

**ギャラリーから Vodeclic を追加するには、次の手順を実行します。**

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** アイコンを選択します。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに、「**Vodeclic**」と入力します。 結果パネルから **[Vodeclic]** を選択し、**[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果一覧の Vodeclic](./media/vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Vodeclic で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Vodeclic ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Vodeclic の関連ユーザーの間で、リンクが確立されている必要があります。

Vodeclic で、**Username** の値を、Azure AD の**ユーザー名**と同じ値に指定します。 これで、2 人のユーザー間にリンクが確立されました。

Vodeclic で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了します。

1. [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
1. [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
1. [Vodeclic テスト ユーザーの作成](#create-a-vodeclic-test-user) - Vodeclic で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. [シングル サインオンのテスト](#test-single-sign-on) - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Vodeclic アプリケーションでシングル サインオンを構成します。

**Vodeclic で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Vodeclic** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログ ボックスの **[シングル サインオン モード]** で、**[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

1. **IDP** 開始モードでアプリケーションを構成する場合は、**[Vodeclic のドメインと URL]** セクションで、次の手順を実行します。

    ![[Vodeclic のドメインと URL] のシングル サインオン情報](./media/vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. **[識別子]** ボックスに、`https://<companyname>.lms.vodeclic.net/auth/saml` というパターンで URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<companyname>.lms.vodeclic.net/auth/saml/callback` の形式で URL を入力します。

1. **SP** 開始モードでアプリケーションを構成する場合は、**[詳細な URL 設定の表示]** チェック ボックスをオンにして、次の手順を実行します。

    ![[Vodeclic のドメインと URL] のシングル サインオン情報](./media/vodeclic-tutorial/tutorial_vodeclic_url1.png)

    **[サインオン URL]** ボックスに、`https://<companyname>.lms.vodeclic.net/auth/saml` というパターンで URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Vodeclic クライアント サポート チーム](mailto:hotline@vodeclic.com)に問い合わせてください。

1. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** を選択します。 次に、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

1. **[保存]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/vodeclic-tutorial/tutorial_general_400.png)
    
1. **Vodeclic** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [Vodeclic サポート チーム](mailto:hotline@vodeclic.com)に送信します。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。 **[Active Directory]**  >  **[エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブを選択し、一番下の **[構成]** セクションから組み込みドキュメントにアクセスします。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンを選択します。

    ![Azure Active Directory のボタン](./media/vodeclic-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動します。 次に、**[すべてのユーザー]** を選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/vodeclic-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** を選択します。

    ![[追加] ボタン](./media/vodeclic-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/vodeclic-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
 
### <a name="create-a-vodeclic-test-user"></a>Vodeclic テスト ユーザーの作成

このセクションでは、Vodeclic で Britta Simon というユーザーを作成します。 [Vodeclic サポート チーム](mailto:hotline@vodeclic.com)と連携して、Vodeclic プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

> [!NOTE]
> アプリケーションの要件によっては、マシンを許可リストに登録しなければならない場合があります。 そのためには、パブリック IP アドレスを [Vodeclic サポート チーム](mailto:hotline@vodeclic.com)と共有する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Vodeclic へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Vodeclic に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。 次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Vodeclic]** を選択します。

    ![アプリケーションの一覧の Vodeclic のリンク](./media/vodeclic-tutorial/tutorial_vodeclic_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンを選びます。 次に、**[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログ ボックスで、**[ユーザー]** 一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** ボタンを選択します。

1. **[割り当ての追加]** ダイアログ ボックスで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Vodeclic のタイルを選択すると、自動的に Vodeclic アプリケーションにサインインします。

アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/vodeclic-tutorial/tutorial_general_01.png
[2]: ./media/vodeclic-tutorial/tutorial_general_02.png
[3]: ./media/vodeclic-tutorial/tutorial_general_03.png
[4]: ./media/vodeclic-tutorial/tutorial_general_04.png

[100]: ./media/vodeclic-tutorial/tutorial_general_100.png

[200]: ./media/vodeclic-tutorial/tutorial_general_200.png
[201]: ./media/vodeclic-tutorial/tutorial_general_201.png
[202]: ./media/vodeclic-tutorial/tutorial_general_202.png
[203]: ./media/vodeclic-tutorial/tutorial_general_203.png

