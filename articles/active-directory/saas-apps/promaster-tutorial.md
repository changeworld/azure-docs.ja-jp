---
title: 'チュートリアル: Azure Active Directory と ProMaster (by Inlogik) の統合 | Microsoft Docs'
description: Azure Active Directory と ProMaster (by Inlogik) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 348dbd37-dc4f-49df-bb90-53d249d456b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: jeedes
ms.openlocfilehash: 13bb128836590fb43e0c6a2f7131f83a99a23eaf
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39507803"
---
# <a name="tutorial-azure-active-directory-integration-with-promaster-by-inlogik"></a>チュートリアル: Azure Active Directory と ProMaster (by Inlogik) の統合

このチュートリアルでは、ProMaster (by Inlogik) と Azure Active Directory (Azure AD) を統合する方法について説明します。

ProMaster (by Inlogik) と Azure AD の統合には、次の利点があります。

- ProMaster (by Inlogik) にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に ProMaster (by Inlogik) にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

ProMaster (by Inlogik) と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- ProMaster (by Inlogik) でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ProMaster (by Inlogik) の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-promaster-by-inlogik-from-the-gallery"></a>ギャラリーからの ProMaster (by Inlogik) の追加

Azure AD への ProMaster (by Inlogik) の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に ProMaster (by Inlogik) を追加する必要があります。

**ギャラリーから ProMaster (by Inlogik) を追加するには、次の手順を行います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**ProMaster (by Inlogik)**」と入力し、結果パネルで **[ProMaster (by Inlogik)]** を選び、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の ProMaster (by Inlogik)](./media/promaster-tutorial/tutorial_promaster_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、ProMaster (by Inlogik) で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する ProMaster (by Inlogik) ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと ProMaster (by Inlogik) の関連ユーザーの間で、リンク関係が確立されている必要があります。

ProMaster (by Inlogik) で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[ProMaster (by Inlogik) のテスト ユーザーの作成](#create-a-promaster-by-inlogik-test-user)** - ProMaster (by Inlogik) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、ProMaster (by Inlogik) アプリケーションでシングル サインオンを構成します。

**ProMaster (by Inlogik) で Azure AD シングル サインオンを構成するには、次の手順を行います。**

1. Azure portal の **ProMaster (by Inlogik)** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/promaster-tutorial/tutorial_promaster_samlbase.png)

3. **[ProMaster (by Inlogik) Domain and URLs]\(ProMaster (by Inlogik) のドメインと URL\)** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順を行います。

    ![[ProMaster (by Inlogik) のドメインと URL] のシングル サインオン情報](./media/promaster-tutorial/tutorial_promaster_url1.png)

    a. **[識別子]** ボックスでは、次の URL パターンのいずれかを使用します。
    | |
    | - |-|
    |  `https://secure.inlogik.com/<COMPANYNAME>`|
    | `https://<CUSTOMDOMAIN>/SAMLBASE`|
    | |

    b. **[応答 URL]** ボックスでは、次の URL パターンのいずれかを使用します。
    | |
    | - |-|
    | `https://secure.inlogik.com/<COMPANYNAME>/saml/acs`|
    | `https://<CUSTOMDOMAIN>/SAMLBASE/saml/acs`|
    | |

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[ProMaster (by Inlogik) のドメインと URL] のシングル サインオン情報](./media/promaster-tutorial/tutorial_promaster_url2.png)

    **[サインオン URL]** ボックスでは、次の URL パターンのいずれかを使用します。
    | |
    | - |-|
    | `https://secure.inlogik.com/<COMPANYNAME>/saml/acs `|
    | `https://<CUSTOMDOMAIN>/SAMLBASE/saml/acs`|
    | |

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得する場合は、[ProMaster (by Inlogik) クライアント サポート チーム](mailto:michael.boldiston@inlogik.com)にお問い合わせください。

5. **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーし、メモ帳に貼り付けます。

    ![証明書のダウンロードのリンク](./media/promaster-tutorial/tutorial_promaster_certificate.png)

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/promaster-tutorial/tutorial_general_400.png)

7. **ProMaster (by Inlogik)** 側でシングル サインオンを構成するには、**アプリのフェデレーション メタデータ URL** を [ProMaster (by Inlogik) サポート チーム](mailto:michael.boldiston@inlogik.com)に送る必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/promaster-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/promaster-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/promaster-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/promaster-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-a-promaster-by-inlogik-test-user"></a>ProMaster (by Inlogik) のテスト ユーザーの作成

このセクションでは、ProMaster (by Inlogik) で Britta Simon というユーザーを作成します。 [ProMaster (by Inlogik) サポート チーム](mailto:michael.boldiston@inlogik.com)と連携して、ProMaster (by Inlogik) プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に ProMaster (by Inlogik) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**ProMaster (by Inlogik) に Britta Simon を割り当てるには、次の手順を行います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[ProMaster (by Inlogik)]** を選択します。

    ![アプリケーションの一覧の ProMaster (by Inlogik) リンク](./media/promaster-tutorial/tutorial_promaster_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [ProMaster (by Inlogik)] タイルをクリックすると、自動的に ProMaster (by Inlogik) アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/promaster-tutorial/tutorial_general_01.png
[2]: ./media/promaster-tutorial/tutorial_general_02.png
[3]: ./media/promaster-tutorial/tutorial_general_03.png
[4]: ./media/promaster-tutorial/tutorial_general_04.png

[100]: ./media/promaster-tutorial/tutorial_general_100.png

[200]: ./media/promaster-tutorial/tutorial_general_200.png
[201]: ./media/promaster-tutorial/tutorial_general_201.png
[202]: ./media/promaster-tutorial/tutorial_general_202.png
[203]: ./media/promaster-tutorial/tutorial_general_203.png

