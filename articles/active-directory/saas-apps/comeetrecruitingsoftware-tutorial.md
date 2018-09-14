---
title: 'チュートリアル: Azure Active Directory と Comeet Recruiting Software の統合 | Microsoft Docs'
description: Azure Active Directory と Comeet Recruiting Software の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 75f51dc9-9525-4ec6-80bf-28374f0c8adf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2018
ms.author: jeedes
ms.openlocfilehash: 137ba7a7e82ff3e57d862868859e8049838701a3
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2018
ms.locfileid: "43311150"
---
# <a name="tutorial-azure-active-directory-integration-with-comeet-recruiting-software"></a>チュートリアル: Azure Active Directory と Comeet Recruiting Software の統合

このチュートリアルでは、Comeet Recruiting Software と Azure Active Directory (Azure AD) を統合する方法について説明します。

Comeet Recruiting Software と Azure AD の統合には、次の利点があります。

- Comeet Recruiting Software にアクセスするユーザーを Azure AD 上でコントロールできます。
- ユーザーが自分の Azure AD アカウントで自動的に Comeet Recruiting Software にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Comeet Recruiting Software と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Comeet Recruiting Software でのシングル サインオンが有効なサブスクリプション

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Comeet Recruiting Software の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-comeet-recruiting-software-from-the-gallery"></a>ギャラリーからの Comeet Recruiting Software の追加

Azure AD への Comeet Recruiting Software の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Comeet Recruiting Software を追加する必要があります。

**ギャラリーから Comeet Recruiting Software を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Comeet Recruiting Software**」と入力し、結果ウィンドウで **Comeet Recruiting Software** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Comeet Recruiting Software](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Comeet Recruiting Software で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Comeet Recruiting Software ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Comeet Recruiting Software の関連ユーザーの間で、リンク関係が確立されている必要があります。

Comeet Recruiting Software で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Comeet Recruiting Software テスト ユーザーの作成](#create-a-comeet-recruiting-software-test-user)** - Comeet Recruiting Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Comeet Recruiting Software アプリケーションでシングル サインオンを構成します。

**Comeet Recruiting Software で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **Comeet Recruiting Software** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_samlbase.png)

3. **[Comeet Recruiting Software のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![Comeet のドメインと URL のシングル サインオン情報](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_url1.png)

    a. **[識別子]** ボックスに、`https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値は、[サポート ページ](https://support.comeet.co/knowledgebase/adfs-single-sign-on/)に示すように、Comeet Recruiting Software ポータルから取得します。

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Comeet Recruiting Software のドメインと URL] のシングル サインオン情報](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_url2.png)

    **[サインオン URL]** ボックスに、URL として「`https://app.comeet.co`」を入力します。

5. Comeet Recruiting Software アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットはその例です。 **[ユーザー識別子]** の既定値は **user.userprincipalname** ですが、**Comeet Recruiting Software** はこれがユーザーのメール アドレスにマップされることを想定します。 そのため、一覧の **user.mail** 属性を使用するか、組織構成に基づいて適切な属性値を使用できます。

    ![Configure single sign-on](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_attribute.png)

6. **[ユーザー属性]** セクションの **[その他のすべてのユーザー属性を表示および編集する]** をクリックし、属性を展開します。 表示される各属性について、次の手順を実行します。

    | 属性名 | 属性値 |
    | ---------------| --------------- |
    | comeet_id | user.userprincipalname |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/comeetrecruitingsoftware-tutorial/tutorial_attribute_04.png)

    ![Configure single sign-on](./media/comeetrecruitingsoftware-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される**属性名**を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[OK]** をクリックします。

7. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_certificate.png)

8. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/comeetrecruitingsoftware-tutorial/tutorial_general_400.png)

9. **Comeet Recruiting Software** 側にシングル サインオンを構成するには、[サポート ページ](https://support.comeet.co/knowledgebase/adfs-single-sign-on/)に示すように、ダウンロードしたメタデータ XML のコンテンツを Comeet Recruiting Software に貼り付ける必要があります。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/comeetrecruitingsoftware-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/comeetrecruitingsoftware-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/comeetrecruitingsoftware-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/comeetrecruitingsoftware-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-a-comeet-recruiting-software-test-user"></a>Comeet Recruiting Software テスト ユーザーの作成

このセクションでは、Comeet Recruiting Software で Britta Simon というユーザーを作成します。 [Comeet Recruiting Software サポート チーム](mailto:support@comeet.co)と連携し、Comeet Recruiting Software プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Comeet Recruiting Software へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Comeet Recruiting Software に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Comeet Recruiting Software]** を選択します。

    ![アプリケーションの一覧の Comeet Recruiting Software のリンク](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Comeet Recruiting Software のタイルをクリックすると、自動的に Comeet Recruiting Software アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_01.png
[2]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_02.png
[3]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_03.png
[4]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_04.png

[100]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_100.png

[200]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_200.png
[201]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_201.png
[202]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_202.png
[203]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_203.png