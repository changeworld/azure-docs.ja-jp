---
title: 'チュートリアル: Azure Active Directory と Grovo の統合 | Microsoft Docs'
description: Azure Active Directory と Grovo の間にシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 399cecc3-aa62-4914-8b6c-5a35289820c1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2018
ms.author: jeedes
ms.openlocfilehash: be49cbba53441124bd538a5d82e8c0e1d20d9e45
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433064"
---
# <a name="tutorial-azure-active-directory-integration-with-grovo"></a>チュートリアル: Azure Active Directory と Grovo の統合

このチュートリアルでは、Grovo と Azure Active Directory (Azure AD) を統合する方法について説明します。

Grovo と Azure AD の統合には、次の利点があります。

- Grovo にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Grovo に自動的にサインオン (シングル サインオン) できるようにすることができます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Grovo と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Grovo でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Grovo の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-grovo-from-the-gallery"></a>ギャラリーからの Grovo の追加
Azure AD への Grovo の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Grovo を追加する必要があります。

**ギャラリーから Grovo を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Grovo**」と入力し、結果パネルで **[Grovo]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Grovo](./media/grovo-tutorial/tutorial_grovo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Grovo で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Grovo ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Grovo の関連ユーザーの間で、リンク リレーションシップが確立されている必要があります。

Grovo で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク リレーションシップを確立します。

Grovo で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Grovo テスト ユーザーの作成](#create-a-grovo-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Grovo で作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Grovo アプリケーションでシングル サインオンを構成します。

**Grovo で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Grovo** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/grovo-tutorial/tutorial_grovo_samlbase.png)

1. **[Grovo のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[Grovo のドメインと URL] のシングル サインオン情報](./media/grovo-tutorial/tutorial_grovo_url.png)

    a. **[識別子]** ボックスに、`https://<subdomain>.grovo.com/sso/saml2/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.grovo.com/sso/saml2/saml-assertion` のパターンを使用して URL を入力します。

1. **[詳細な URL 設定の表示]** をオンにして、次の手順を実行します。

    ![[Grovo のドメインと URL] のシングル サインオン情報](./media/grovo-tutorial/tutorial_grovo_url1.png)

    a. **[リレー状態]** ボックスに、`https://<subdomain>.grovo.com` のパターンで URL を入力します。

    b. **SP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![[Grovo のドメインと URL] のシングル サインオン情報](./media/grovo-tutorial/tutorial_grovo_url2.png)
    
    **[サインオン URL]** ボックスに、`https://<subdomain>.grovo.com/sso/saml2/saml-assertion` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL、リレー状態でこれらの値を更新します。 これらの値を取得するには、[Grovo サポート チーム](https://www.grovo.com/contact-us)にお問い合わせください。
 
1. Grovo アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 **[ユーザー識別子]** を **[User.mail]** でマップし、その他の属性を次のスクリーンショットのように構成してください。
    
    ![シングル サインオンの構成の属性](./media/grovo-tutorial/tutorial_grovo_attribute.png)
    
1. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ------------------- | -------------------- |    
    | 名          | User.givenname |
    | 姓           | User.surname |
    | 電子メール アドレス       | User.mail    |
    | employeeID          | user.employeeid |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![シングル サインオンの構成の追加](./media/grovo-tutorial/tutorial_attribute_04.png)

    ![シングル サインオンの構成の属性の追加](./media/grovo-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[名前空間]** は空白のままにします。
    
    e. **[OK]** をクリックします。


1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/grovo-tutorial/tutorial_grovo_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/grovo-tutorial/tutorial_general_400.png)

1. **[Grovo 構成]** セクションで、**[Grovo の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![Grovo 構成](./media/grovo-tutorial/tutorial_grovo_configure.png) 

1. 別の Web ブラウザー ウィンドウで、Grovo に管理者としてログインします。

1. **[管理者]** > **[統合]** に移動します。
 
    ![Grovo 構成](./media/grovo-tutorial/tutorial_grovo_admin.png) 

1. **[SP Initiated SAML 2.0]\(SP によって開始された SAML 2.0\)** セクションで **[設定]** をクリックします。

    ![Grovo 構成](./media/grovo-tutorial/tutorial_grovo_setup.png)

1. **[SP Initiated SAML 2.0]\(SP によって開始された SAML 2.0\)** ポップアップ ウィンドウで、次の手順を実行します。

    ![Grovo 構成](./media/grovo-tutorial/tutorial_grovo_saml.png)

    a. **[エンティティ ID]** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    b. **[Single sign on service endpoint]\(シングル サインオン サービス エンドポイント\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    c. **[Single sign on service endpoint binding]\(シングル サインオン サービス エンドポイント バインディング\)** として `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect` を選択します。
    
    d. Azure Portal からダウンロードした **Base64 でエンコードされた証明書**をメモ帳で開き、**[公開キー]** ボックスに貼り付けます。

    e. **[次へ]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/grovo-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/grovo-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/grovo-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/grovo-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
  
### <a name="create-a-grovo-test-user"></a>Grovo テスト ユーザーの作成

このセクションの目的は、Grovo で Britta Simon というユーザーを作成することです。 Grovo では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Grovo ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。
>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Grovoサポート チーム](https://www.grovo.com/contact-us)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Grovo へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Grovo に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Grovo]** を選択します。

    ![アプリケーションの一覧の [Grovo] リンク](./media/grovo-tutorial/tutorial_grovo_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Grovo] タイルをクリックすると、自動的に Grovo アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/grovo-tutorial/tutorial_general_01.png
[2]: ./media/grovo-tutorial/tutorial_general_02.png
[3]: ./media/grovo-tutorial/tutorial_general_03.png
[4]: ./media/grovo-tutorial/tutorial_general_04.png

[100]: ./media/grovo-tutorial/tutorial_general_100.png

[200]: ./media/grovo-tutorial/tutorial_general_200.png
[201]: ./media/grovo-tutorial/tutorial_general_201.png
[202]: ./media/grovo-tutorial/tutorial_general_202.png
[203]: ./media/grovo-tutorial/tutorial_general_203.png

