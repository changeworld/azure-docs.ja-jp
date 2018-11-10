---
title: 'チュートリアル: Azure Active Directory と Figma の統合 | Microsoft Docs'
description: Azure Active Directory と Figma の間にシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8569cae1-87dd-4c40-9bbb-527ac80d6a96
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: jeedes
ms.openlocfilehash: b57fdb3f039a9395133854f8b4d4f2095e3a4f9b
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095247"
---
# <a name="tutorial-azure-active-directory-integration-with-figma"></a>チュートリアル: Azure Active Directory と Figma の統合

このチュートリアルでは、Figma と Azure Active Directory (Azure AD) を統合する方法について説明します。

Figma と Azure AD の統合には、次の利点があります。

- Figma にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Figma にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Figma と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Figma の[シングル サインオンが有効なサブスクリプション](https://www.figma.com/pricing/)

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 Figma Professional Team の新規ユーザーとアクティブなサブスクライバーの場合、Figma Organization Tier に[サブスクリプションをアップグレードする](https://www.figma.com/pricing/)方法については Figma に問い合わせてください。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Figma の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-figma-from-the-gallery"></a>ギャラリーからの Figma の追加

Azure AD への Figma の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Figma を追加する必要があります。

**ギャラリーから Figma を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

4. 検索ボックスに、「**Figma**」と入力します。 結果ウィンドウで **[Figma]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Figma](./media/figma-tutorial/tutorial_figma_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Figma で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンが機能するには、Azure AD を Figma とリンクする必要があります。  Figma で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. [**Figma サポート チームに連絡し**](mailto:support@figma.com?subject=SAML+Config)、組織の SAML 構成を開始し、ORG_SAML_CONFIG_ID を取得します。
2. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
3. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Figma テスト ユーザーの作成](#creating-a-figma-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Figma で作成します。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
6. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Figma アプリケーションでシングル サインオンを構成します。

**Figma で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure portal の **Figma** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](./media/figma-tutorial/tutorial_general_301.png)

3. **SAML** モードを別のモードから変更する必要がある場合は、画面上部の **[シングル サインオン モードの変更]** をクリックします。

    ![Configure single sign-on](./media/figma-tutorial/tutorial_general_300.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](./media/figma-tutorial/tutorial_general_302.png)

5. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![[Figma のドメインと URL] のシングル サインオン情報](./media/figma-tutorial/tutorial_figma_url1.png)

    a. **[識別子]** ボックスに、`https://www.figma.com/saml/<ORG_SAML_CONFIG_ID>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://www.figma.com/saml/<ORG_SAML_CONFIG_ID>/consume` のパターンを使用して URL を入力します。

6. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Figma のドメインと URL] のシングル サインオン情報](./media/figma-tutorial/tutorial_figma_url2.png)

    **[サインオン URL]** ボックスに、`https://www.figma.com/saml/<ORG_SAML_CONFIG_ID>/start` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 この値を取得するには、[Figma サポート チーム](mailto:support@figma.com?subject=SAML+Config)に問い合わせてください。

7. Figma アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![属性セクション](./media/figma-tutorial/edit_attribute.png)

8. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | Name | ソース属性|
    | ---------------| --------- |
    | `externalId` | `user.mailnickname` |
    | `displayName` | `user.displayname` |
    | `title` | `user.jobtitle` |
    | `emailaddress` | `user.mail` |
    | `familyName` | `user.surname` |
    | `givenName` | `givenName` |
    | `userName` | `user.userprincipalname` |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![新しい属性](./media/figma-tutorial/new_save_attribute.png)

    ![[属性の追加]](./media/figma-tutorial/new_attribute_details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

9. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/figma-tutorial/tutorial_figma_certificate.png)

10. Figma 側でシングル サインオンを構成するには、次のフォームに入力してください。[https://goo.gl/forms/XkRB1z5ed4eVUzXn2](https://goo.gl/forms/XkRB1z5ed4eVUzXn2) 手順 9 の **アプリのフェデレーション メタデータ URL** を使用します。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![Azure AD ユーザーの作成][100]

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![Azure AD のテスト ユーザーの作成](./media/figma-tutorial/create_aaduser_01.png) 

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](./media/figma-tutorial/create_aaduser_02.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon@yourcompanydomain.extension**」と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。

### <a name="creating-a-figma-test-user"></a>Figma テスト ユーザーの作成

このセクションの目的は、Figma で Britta Simon というユーザーを作成することです。 Figma では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Figma ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に HubSpot SAML へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[HubSpot SAML]** を選択します。

    ![Configure single sign-on](./media/figma-tutorial/tutorial_figma_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Figma のタイルをクリックすると、Figma アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、「[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/figma-tutorial/tutorial_general_01.png
[2]: ./media/figma-tutorial/tutorial_general_02.png
[3]: ./media/figma-tutorial/tutorial_general_03.png
[4]: ./media/figma-tutorial/tutorial_general_04.png

[100]: ./media/figma-tutorial/tutorial_general_100.png

[200]: ./media/figma-tutorial/tutorial_general_200.png
[201]: ./media/figma-tutorial/tutorial_general_201.png
[202]: ./media/figma-tutorial/tutorial_general_202.png
[203]: ./media/figma-tutorial/tutorial_general_203.png