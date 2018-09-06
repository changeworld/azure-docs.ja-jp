---
title: 'チュートリアル: Azure Active Directory と Nuclino の統合 | Microsoft Docs'
description: Azure Active Directory と Nuclino の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 74bbab82-5581-4dcf-8806-78f77c746968
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: jeedes
ms.openlocfilehash: 1a5346b98de48b1a2f8928c3c2bf30730588e9c1
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2018
ms.locfileid: "43145390"
---
# <a name="tutorial-azure-active-directory-integration-with-nuclino"></a>チュートリアル: Azure Active Directory と Nuclino の統合

このチュートリアルでは、Nuclino と Azure Active Directory (Azure AD) を統合する方法について説明します。

Nuclino と Azure AD の統合には、次の利点があります。

- Nuclino にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Nuclino にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Nuclino と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Nuclino でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Nuclino の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-nuclino-from-the-gallery"></a>ギャラリーからの Nuclino の追加

Azure AD への Nuclino の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Nuclino を追加する必要があります。

**ギャラリーから Nuclino を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Nuclino**」と入力し、結果パネルで **[Nuclino]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Nuclino](./media/nuclino-tutorial/tutorial_nuclino_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Nuclino で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Nuclino ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Nuclino の関連ユーザーの間で、リンク関係が確立されている必要があります。

Nuclino で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Nuclino のテスト ユーザーの作成](#create-a-nuclino-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Nuclino で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Nuclino アプリケーションでシングル サインオンを構成します。

**Nuclino で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **Nuclino** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/nuclino-tutorial/tutorial_nuclino_samlbase.png)

3. **[Nuclino のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[Nuclino のドメインと URL] のシングル サインオン情報](./media/nuclino-tutorial/tutorial_nuclino_url1.png)

    a. **[識別子]** ボックスに、`https://api.nuclino.com/api/sso/<UNIQUE-ID>/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://api.nuclino.com/api/sso/<UNIQUE-ID>/acs` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 **[認証]** セクションの実際の識別子と応答 URL にこれらの値を置き換えます。実際の値については後で説明します。

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Nuclino のドメインと URL] のシングル サインオン情報](./media/nuclino-tutorial/tutorial_nuclino_url2.png)

    **[サインオン URL]** ボックスに、`https://app.nuclino.com/<UNIQUE-ID>/login` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これは実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Nuclino クライアント サポート チーム](mailto:contact@nuclino.com)にお問い合わせください。

5. Nuclino アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 次のスクリーンショットはその例です。

    ![Configure single sign-on](./media/Nuclino-tutorial/tutorial_attribute.png)

6. **[ユーザー属性]** セクションの **[その他のすべてのユーザー属性を表示および編集する]** をクリックし、属性を展開します。 表示される各属性について、次の手順を実行します。

    | 属性名 | 属性値 |
    | ---------------| --------------- |
    | first_name | User.givenname |
    | last_name | User.surname |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/nuclino-tutorial/tutorial_attribute_04.png)

    ![Configure single sign-on](./media/nuclino-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される**属性名**を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[OK]** をクリックします。

7. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/nuclino-tutorial/tutorial_nuclino_certificate.png)

8. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/nuclino-tutorial/tutorial_general_400.png)

9. **[Nuclino 構成]** セクションで、**[Nuclino の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![Nuclino 構成](./media/nuclino-tutorial/tutorial_nuclino_configure.png)

10. 別の Web ブラウザー ウィンドウで、Nuclino 企業サイトに管理者としてサインインします。

11. **アイコン**をクリックします。

    ![Nuclino 構成](./media/nuclino-tutorial/configure1.png)

12. **[Azure AD SSO]** をクリックし、ドロップダウンから **[Team settings]\(チームの設定\)** を選択します。

    ![Nuclino 構成](./media/nuclino-tutorial/configure2.png)

13. 左側のナビゲーション ウィンドウから **[Authentication]\(認証\)** を選択します。

    ![Nuclino 構成](./media/nuclino-tutorial/configure3.png)

14. **[Authentication]\(認証\)** セクションで、次の手順に従います。

    ![Nuclino 構成](./media/nuclino-tutorial/configure4.png)

    a. **[SAML-based single sign-on (SSO)]\(SAML ベースのシングル サインオン (SSO)\)** を選択します。

    b. **[ACS URL (You need to copy and paste this to your SSO provider)]\(ACS URL (この値をコピーして SSO プロバイダーにコピーしてください)\)** 値をコピーして、Azure portal の **[Nuclino のドメインと URL]** セクションの **[応答 URL]** ボックスに貼り付けます。

    c. **[Entity ID (You need to copy and paste this to your SSO provider)]\(エンティティ ID (この値をコピーして SSO プロバイダーにコピーしてください)\)** 値をコピーして、Azure portal の **[Nuclino のドメインと URL]** セクションの **[識別子]** ボックスに貼り付けます。

    d. **[SSO URL]** ボックスに、Azure portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    e. **[Entity ID]\(エンティティ ID\)** ボックスに、Azure portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    f. ダウンロードした**証明書 (Base64)** ファイルをメモ帳で開きます。 その内容をクリップボードにコピーし、**[Public certificate]\(公開証明書\)** ボックスに貼り付けます。

    g. **[SAVE CHANGES]\(変更の保存\)** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/nuclino-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/nuclino-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/nuclino-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/nuclino-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-a-nuclino-test-user"></a>Nuclino テスト ユーザーの作成

このセクションの目的は、Nuclino で Britta Simon というユーザーを作成することです。 Nuclino では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Nuclino ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、[Nuclino サポート チーム](mailto:contact@nuclino.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Nuclino へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Britta Simon を Nuclino に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Nuclino]** を選択します。

    ![アプリケーションの一覧の [Nuclino] リンク](./media/nuclino-tutorial/tutorial_nuclino_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Nuclino] タイルをクリックすると、Nuclino アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/nuclino-tutorial/tutorial_general_01.png
[2]: ./media/nuclino-tutorial/tutorial_general_02.png
[3]: ./media/nuclino-tutorial/tutorial_general_03.png
[4]: ./media/nuclino-tutorial/tutorial_general_04.png

[100]: ./media/nuclino-tutorial/tutorial_general_100.png

[200]: ./media/nuclino-tutorial/tutorial_general_200.png
[201]: ./media/nuclino-tutorial/tutorial_general_201.png
[202]: ./media/nuclino-tutorial/tutorial_general_202.png
[203]: ./media/nuclino-tutorial/tutorial_general_203.png