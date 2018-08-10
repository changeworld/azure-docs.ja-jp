---
title: 'チュートリアル: Azure Active Directory と Infogix Data3Sixty Govern の統合 | Microsoft Docs'
description: Azure Active Directory と Infogix Data3Sixty Govern の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: aa3109b8-bdbe-45ae-933a-2eb4dc03855c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 3e54ade44828bf1e26c310a14ae401fe8ae33229
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437664"
---
# <a name="tutorial-azure-active-directory-integration-with-infogix-data3sixty-govern"></a>チュートリアル: Azure Active Directory と Infogix Data3Sixty Govern の統合

このチュートリアルでは、Infogix Data3Sixty Govern と Azure Active Directory (Azure AD) を統合する方法について説明します。

Infogix Data3Sixty Govern と Azure AD の統合には、次の利点があります。

- Infogix Data3Sixty Govern にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Infogix Data3Sixty Govern にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Infogix Data3Sixty Govern と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Infogix Data3Sixty Govern でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Infogix Data3Sixty Govern の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-infogix-data3sixty-govern-from-the-gallery"></a>ギャラリーからの Infogix Data3Sixty Govern の追加
Azure AD への Infogix Data3Sixty Govern の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Infogix Data3Sixty Govern を追加する必要があります。

**ギャラリーから Infogix Data3Sixty Govern を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Infogix Data3Sixty Govern**」と入力し、結果パネルで **[Infogix Data3Sixty Govern]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Infogix Data3Sixty Govern](./media/infogix-tutorial/tutorial_infogix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Infogix Data3Sixty Govern で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Infogix Data3Sixty Govern ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Infogix Data3Sixty Govern の関連ユーザーの間で、リンク関係が確立されている必要があります。

Infogix Data3Sixty Govern で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Infogix Data3Sixty Govern テスト ユーザーの作成](#create-an-infogix-data3sixty-govern-test-user)** - Infogix Data3Sixty Govern で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Infogix Data3Sixty Govern アプリケーションでシングル サインオンを構成します。

**Infogix Data3Sixty Govern で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Infogix Data3Sixty Govern** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/infogix-tutorial/tutorial_infogix_samlbase.png)

1. **[Infogix Data3Sixty Govern Domain and URLs]\(Infogix Data3Sixty Govern のドメインと URL)** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[Infogix Data3Sixty Govern Domain and URLs]\(Infogix Data3Sixty Govern のドメインと URL) のシングル サインオン情報](./media/infogix-tutorial/tutorial_infogix_url.png)

    a. **[識別子]** ボックスに次の URL を入力します。`https://data3sixty.com/ui`

    b. **[応答 URL]** ボックスに、`https://<subdomain>.data3sixty.com/sso/acs` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Infogix Data3Sixty Govern Domain and URLs]\(Infogix Data3Sixty Govern のドメインと URL) のシングル サインオン情報](./media/infogix-tutorial/tutorial_infogix_url1.png)

    **[サインオン URL]** ボックスに、`https://<subdomain>.data3sixty.com` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の応答 URLとサインオン URL でこれらの値を更新します。 これらの値を取得するには、[Infogix Data3Sixty Govern クライアント サポート チーム](mailto:data3sixtysupport@infogix.com)にお問い合わせください。

1. Infogix Data3Sixty Govern アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![シングル サインオンの構成の属性](./media/infogix-tutorial/tutorial_infogix_attribute.png)
    
1. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ------------------- | -------------------- |    
    | firstname           | User.givenname |
    | lastname        | User.surname |
    | username       | User.mail    |
    
    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![シングル サインオンの構成の追加](./media/infogix-tutorial/tutorial_attribute_04.png)

    ![シングル サインオンの構成の属性の追加](./media/infogix-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[名前空間]** は空白のままにします。
    
    e. **[OK]** をクリックします。

1. **[SAML 署名証明書]** セクションで、**[Certificate (Raw) (証明書 (Raw))]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/infogix-tutorial/tutorial_infogix_certificate.png)

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/infogix-tutorial/tutorial_general_400.png)
    
1. **[Infogix Data3Sixty Govern Configuration]\(Infogix Data3Sixty Govern の構成)** セクションで、**[Configure Infogix Data3Sixty Govern]\(Infogix Data3Sixty Govern の構成)** をクリックして **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Infogix Data3Sixty Govern の構成](./media/infogix-tutorial/tutorial_infogix_configure.png) 

1. **Infogix Data3Sixty Govern** 側にシングル サインオンを構成するには、ダウンロードした**証明書 (未加工)、サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** を [Infogix Data3Sixty Govern サポート チーム](mailto:data3sixtysupport@infogix.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/infogix-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/infogix-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/infogix-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/infogix-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-infogix-data3sixty-govern-test-user"></a>Infogix Data3Sixty 制御テスト ユーザーを作成する


このセクションの目的は、Infogix Data3Sixty Govern で Britta Simon というユーザーを作成することです。 Infogix Data3Sixty Govern では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Infogix Data3Sixty Govern にアクセスしようとすると、ユーザーがまだ存在しない場合は新しいユーザーが作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Infogix Data3Sixty Govern サポート チーム](mailto:data3sixtysupport@infogix.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Infogix Data3Sixty Govern へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Infogix Data3Sixty Govern に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Infogix Data3Sixty Govern]** を選択します。

    ![アプリケーションの一覧の Infogix Data3Sixty Govern のリンク](./media/infogix-tutorial/tutorial_infogix_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Infogix Data3Sixty Govern のタイルをクリックすると、自動的に Infogix Data3Sixty Govern アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/infogix-tutorial/tutorial_general_01.png
[2]: ./media/infogix-tutorial/tutorial_general_02.png
[3]: ./media/infogix-tutorial/tutorial_general_03.png
[4]: ./media/infogix-tutorial/tutorial_general_04.png

[100]: ./media/infogix-tutorial/tutorial_general_100.png

[200]: ./media/infogix-tutorial/tutorial_general_200.png
[201]: ./media/infogix-tutorial/tutorial_general_201.png
[202]: ./media/infogix-tutorial/tutorial_general_202.png
[203]: ./media/infogix-tutorial/tutorial_general_203.png

