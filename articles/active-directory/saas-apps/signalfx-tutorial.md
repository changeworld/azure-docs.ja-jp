---
title: 'チュートリアル: Azure Active Directory と SignalFx の統合 | Microsoft Docs'
description: Azure Active Directory と SignalFx の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 0d21a409669cc7d7fceeec9787efbe31d880597c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437850"
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>チュートリアル: Azure Active Directory と SignalFx の統合

このチュートリアルでは、SignalFx と Azure Active Directory (Azure AD) を統合する方法について説明します。

SignalFx と Azure AD の統合には、次の利点があります。

- SignalFx にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで SignalFx に自動的にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

SignalFx と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SignalFx でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから SignalFx を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-signalfx-from-the-gallery"></a>ギャラリーから SignalFx を追加する
Azure AD への SignalFx の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SignalFx を追加する必要があります。

**ギャラリーから SignalFx を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**SignalFx**」と入力し、結果パネルで **[SignalFx]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の SignalFx](./media/signalfx-tutorial/tutorial_signalfx_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SignalFx で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SignalFx ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SignalFx の関連ユーザーの間で、リンク関係が確立されている必要があります。

SignalFx で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[SignalFx のテスト ユーザーの作成](#create-a-signalfx-test-user)** - SignalFx で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、SignalFx アプリケーションでシングル サインオンを構成します。

**SignalFx で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SignalFx** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/signalfx-tutorial/tutorial_signalfx_samlbase.png)

1. **[SignalFx のドメインと URL]** セクションで、次の手順に従います。

    ![[SignalFx のドメインと URL] のシングル サインオン情報](./media/signalfx-tutorial/tutorial_signalfx_url.png)

    a. **[識別子]** ボックスに次の URL を入力します。`https://api.signalfx.com/v1/saml/metadata`

    b. **[応答 URL]** ボックスに、`https://api.signalfx.com/v1/saml/acs/<integration ID>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > 上記の値は、実際の値ではありません。 実際の応答 URL に値を置き換えます。実際の値については後で説明します。

1. SignalFx アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットはその例です。   

    ![Configure single sign-on](./media/signalfx-tutorial/tutorial_signalfx_attribute.png)

1. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ------------------- | -------------------- |    
    | User.FirstName          | User.givenname |
    | User.email          | User.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | User.surname    |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![シングル サインオンの構成の追加](./media/signalfx-tutorial/tutorial_attribute_04.png)

    ![シングル サインオンの構成の属性の追加](./media/signalfx-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[名前空間]** は空白のままにします。
    
    e. **[OK]** をクリックします。
 
1. **[SAML 署名証明書]** セクションで、次の手順を実行します。 

    ![証明書のダウンロードのリンク](./media/signalfx-tutorial/tutorial_signalfx_certificate.png)

    a. コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーし、メモ帳に貼り付けます。

    b. **[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/signalfx-tutorial/tutorial_general_400.png)

1. **[SignalFx 構成]** セクションで、**[SignalFx の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス] セクション**から **SAML エンティティ ID** をコピーします。

    ![SignalFx 構成](./media/signalfx-tutorial/tutorial_signalfx_configure.png) 

1. SignalFx 企業サイトに管理者としてサインオンします。

1. SignalFx で一番上に表示されている **[統合]** をクリックし、統合ページを開きます。

    ![SignalFx 統合](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

1. **[ログイン サービス]** セクションの **[Azure Active Directory]** タイルをクリックします。
 
    ![SignalFx saml](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

1. **[NEW INTEGRATION]\(新規統合\)** をクリックし、**[インストール]** タブで次の手順を実行します。
 
    ![SignalFx samlintgpage](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. **[名前]** テキストボックスに新しい統合名を入力します。たとえば、「**OurOrgName SAML SSO**」にします。

    b. **[統合 ID]** 値をコピーし、Azure Portal の **[SignalFx Domain and URLs]\(SignalFx のドメインと URL\)** セクションの **[応答 URL]** テキストボックスの**応答 URL** の末尾に追加します。たとえば、`https://api.signalfx.com/v1/saml/acs/<integration ID>` のようになります。

    c. **[ファイルのアップロード]** をクリックし、**[証明書]** テキストボックスで、Azure Portal からダウンロードした **Base64 エンコード証明書**をアップロードします。

    d. **[発行者の URL]** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    e. **[メタデータ URL]** テキスト ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** を貼り付けます。

    f. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/signalfx-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/signalfx-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/signalfx-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/signalfx-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
  
### <a name="create-a-signalfx-test-user"></a>SignalFx テスト ユーザーを作成する

このセクションの目的は、SignalFx で Britta Simon というユーザーを作成することです。 SignalFx では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない SignalFx ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

SAML SSO から SignalFx に初めてサインインするとき、[SignalFx サポート チーム](mailto:kmazzola@signalfx.com)からメールが届きます。そのメールに記載されているリンクから認証する必要があります。 これは初めてサインインするときにのみ行われます。その後のログインではメールからの検証は要求されません。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[SignalFx サポート チーム](mailto:kmazzola@signalfx.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SignalFx へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**SignalFx に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[SignalFx]** を選択します。

    ![アプリケーションの一覧の [SignalFx] リンク](./media/signalfx-tutorial/tutorial_signalfx_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SignalFx] タイルをクリックすると、自動的に SignalFx アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/signalfx-tutorial/tutorial_general_01.png
[2]: ./media/signalfx-tutorial/tutorial_general_02.png
[3]: ./media/signalfx-tutorial/tutorial_general_03.png
[4]: ./media/signalfx-tutorial/tutorial_general_04.png

[100]: ./media/signalfx-tutorial/tutorial_general_100.png

[200]: ./media/signalfx-tutorial/tutorial_general_200.png
[201]: ./media/signalfx-tutorial/tutorial_general_201.png
[202]: ./media/signalfx-tutorial/tutorial_general_202.png
[203]: ./media/signalfx-tutorial/tutorial_general_203.png

