---
title: 'チュートリアル: Azure Active Directory と Amplitude の統合 | Microsoft Docs'
description: Azure Active Directory と Amplitude の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 496c9ffa-c833-41fa-8d17-2dc3044954d1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeedes
ms.openlocfilehash: a2815b60799f98071915a0f06908fd92ff3fb2f2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39043933"
---
# <a name="tutorial-azure-active-directory-integration-with-amplitude"></a>チュートリアル: Azure Active Directory と Amplitude の統合

このチュートリアルでは、Amplitude と Azure Active Directory (Azure AD) を統合する方法について説明します。

Amplitude と Azure AD の統合には、次の利点があります。

- Amplitude にアクセスするユーザーを Azure AD で管理できます。
- ユーザーが自分の Azure AD アカウントで自動的に Amplitude にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Amplitude と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Amplitude でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Amplitude を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-amplitude-from-the-gallery"></a>ギャラリーから Amplitude を追加する
Azure AD への Amplitude の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Amplitude を追加する必要があります。

**ギャラリーから Amplitude を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Amplitude**」と入力し、結果ウィンドウで **[Amplitude]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Amplitude](./media/amplitude-tutorial/tutorial_amplitude_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Amplitude で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Amplitude ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Amplitude の関連ユーザーの間で、リンク関係が確立されている必要があります。

Amplitude で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Amplitude のテスト ユーザーの作成](#create-an-amplitude-test-user)** - Amplitude で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Amplitude アプリケーションでシングル サインオンを構成します。

**Amplitude で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Amplitude** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/amplitude-tutorial/tutorial_amplitude_samlbase.png)

3. アプリケーションを **IDP** 開始モードで構成する場合は、**[Amplitude のドメインと URL]** セクションで次の手順を実行します。

    ![[Amplitude のドメインと URL] のシングル サインオン情報](./media/amplitude-tutorial/tutorial_amplitude_url.png)

    a. **[識別子]** ボックスに次の URL を入力します。`https://amplitude.com/saml/sso/metadata`

    b. **[応答 URL]** ボックスに、`https://analytics.amplitude.com/saml/sso/<uniqueid>` のパターンを使用して URL を入力します。

    > [!NOTE]
    > 応答 URL 値は、実際の値ではありません。 応答 URL はこのチュートリアルの後半で取得します。

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Amplitude のドメインと URL] のシングル サインオン情報](./media/amplitude-tutorial/tutorial_amplitude_url1.png)

    **[サインオン URL]** ボックスに、URL として「`https://analytics.amplitude.com/sso`」を入力します。

5. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/amplitude-tutorial/tutorial_amplitude_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/amplitude-tutorial/tutorial_general_400.png)
    
7. Amplitude 企業サイトに管理者としてサインオンします。

8. 左側のナビゲーション バーから **[Plan Admin]\(プラン管理者\)** をクリックします。

    ![Configure single sign-on](./media/amplitude-tutorial/configure1.png)

9. **[SSO Integration]\(SSO の統合\)** から **[Microsoft Azure Active Directory Metadata]\(Microsoft Azure Active Directory のメタデータ\)** を選択します。

    ![Configure single sign-on](./media/amplitude-tutorial/configure2.png)

10. **[Setup Single Sign-On]\(シングル サインオンの設定\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/amplitude-tutorial/configure3.png)

    a. Azure Portal からダウンロードした**メタデータ XML** をメモ帳で開き、内容を **[Microsoft Azure Active Directory Metadata]\(Microsoft Azure Active Directory のメタデータ\)** テキストボックスに貼り付けます。

    b. **[Reply URL (ACS)]\(応答 URL (ACS)\)** の値をコピーして Azure Portal の [Amplitude のドメインと URL] セクションの **[応答 URL]** ボックスに貼り付けます。

    c. **[保存]**

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/amplitude-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/amplitude-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/amplitude-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/amplitude-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-amplitude-test-user"></a>Amplitude テスト ユーザーを作成する

このセクションの目的は、Amplitude で Britta Simon というユーザーを作成することです。 Amplitude では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Amplitude にアクセスしようとすると、ユーザーがまだ存在しない場合は新しいユーザーが作成されます。
>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Amplitude サポート チーム](https://amplitude.zendesk.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Amplitude へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Amplitude に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Amplitude]** を選択します。

    ![アプリケーションの一覧の Amplitude リンク](./media/amplitude-tutorial/tutorial_amplitude_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Amplitude] タイルをクリックすると、自動的に Amplitude アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/amplitude-tutorial/tutorial_general_01.png
[2]: ./media/amplitude-tutorial/tutorial_general_02.png
[3]: ./media/amplitude-tutorial/tutorial_general_03.png
[4]: ./media/amplitude-tutorial/tutorial_general_04.png

[100]: ./media/amplitude-tutorial/tutorial_general_100.png

[200]: ./media/amplitude-tutorial/tutorial_general_200.png
[201]: ./media/amplitude-tutorial/tutorial_general_201.png
[202]: ./media/amplitude-tutorial/tutorial_general_202.png
[203]: ./media/amplitude-tutorial/tutorial_general_203.png

