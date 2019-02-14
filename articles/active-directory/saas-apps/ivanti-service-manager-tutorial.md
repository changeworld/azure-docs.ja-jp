---
title: チュートリアル:Azure Active Directory と Ivanti Service Manager (ISM) の統合 | Microsoft Docs
description: Azure Active Directory と Ivanti Service Manager (ISM) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 14297c74-0d57-4146-97fa-7a055fb73057
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8859c0363a738c744f5c9b496cf77f5aaf1ba61
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203507"
---
# <a name="tutorial-azure-active-directory-integration-with-ivanti-service-manager-ism"></a>チュートリアル:Azure Active Directory と Ivanti Service Manager (ISM) の統合

このチュートリアルでは、Ivanti Service Manager (ISM) と Azure Active Directory (Azure AD) を統合する方法について説明します。

Ivanti Service Manager (ISM) と Azure AD を統合すると、次の利点があります。

- Ivanti Service Manager (ISM) にどの Azure AD ユーザーがアクセスできるかを管理できます。
- ユーザーが自分の Azure AD アカウントで自動的に Ivanti Service Manager (ISM) にサインオン (シングル サインオン) できるように設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Ivanti Service Manager (ISM) と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Ivanti Service Manager (ISM) のシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Ivanti Service Manager (ISM) を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-ivanti-service-manager-ism-from-the-gallery"></a>ギャラリーから Ivanti Service Manager (ISM) を追加する

Azure AD と Ivanti Service Manager (ISM) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Ivanti Service Manager (ISM) を追加する必要があります。

**ギャラリーから Ivanti Service Manager (ISM) を追加するには、次の手順を行います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Ivanti Service Manager (ISM)**」と入力し、結果パネルから **[Ivanti Service Manager (ISM)]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果リストの Ivanti Service Manager (ISM)](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーを使用して、Ivanti Service Manager (ISM) で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Ivanti Service Manager (ISM) ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Ivanti Service Manager (ISM) の関連ユーザーの間で、リンク関係が確立されている必要があります。

Ivanti Service Manager (ISM) で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Ivanti Service Manager (ISM) のテスト ユーザーの作成](#creating-an-ivanti-service-manager-ism-test-user)** - Ivanti Service Manager (ISM) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Ivanti Service Manager (ISM) アプリケーションでシングル サインオンを構成します。

**Ivanti Service Manager (ISM) で Azure AD シングル サインオンを構成するには、次の手順を行います。**

1. Azure portal の **Ivanti Service Manager (ISM)** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](common/tutorial-general-301.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](common/editconfigure.png)

4. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![Ivanti Service Manager (ISM) のドメインと URL のシングル サインオン情報](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-url.png)

    a. **[識別子]** ボックスに、次のパターンで URL を入力します。
    | |
    |--|
    | `https://<customer>.saasit.com/` |
    | `https://<customer>.saasiteu.com/` |
    | `https://<customer>.saasitau.com/` |

    b. **[応答 URL]** ボックスに、`https://<customer>/handlers/sso/SamlAssertionConsumerHandler.ashx` のパターンを使用して URL を入力します。

5. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![Ivanti Service Manager (ISM) のドメインと URL のシングル サインオン情報](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-url1.png)

    **[サインオン URL]** ボックスに、`https://<customer>.saasit.com/` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 [Ivanti Service Manager (ISM) クライアント サポート チーム](https://www.ivanti.com/support/contact)に問い合わせてこれらの値を取得します。

6. **[SAML 署名証明書]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**証明書 (Raw)** をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-certificate.png) 

7. **[Ivanti Service Manager (ISM) の設定]** セクションで、要件に従って適切な URL をコピーします。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![Ivanti Service Manager (ISM) の構成](common/configuresection.png)

8. **Ivanti Service Manager (ISM)** 側でシングル サインオンを構成するには、ダウンロードした**証明書 (Raw)** と、コピーした**ログイン URL**、**Azure AD 識別子**、**ログアウト URL** を [Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact) サポート チームに送る必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![Azure AD ユーザーの作成][100]

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![Azure AD のテスト ユーザーの作成](common/create-aaduser-01.png) 

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](common/create-aaduser-02.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon@yourcompanydomain.extension**」と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **作成**を選択します。

### <a name="creating-an-ivanti-service-manager-ism-test-user"></a>Ivanti Service Manager (ISM) テスト ユーザーの作成

このセクションの目的は、Ivanti Service Manager (ISM) で Britta Simon というユーザーを作成することです。 Ivanti Service Manager (ISM) では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Ivanti Service Manager (ISM) にアクセスしようとすると、ユーザーがまだ存在しない場合は新しいユーザーが作成されます。
>[!Note]
>手動でユーザーを作成する必要がある場合は、 [Ivanti Service Manager (ISM) サポート チーム](https://www.ivanti.com/support/contact)にお問い合わせください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Ivanti Service Manager (ISM) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーション一覧から **[Ivanti Service Manager (ISM)]** を選択します。

    ![Configure single sign-on](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Ivanti Service Manager (ISM)] タイルをクリックすると、自動的に Ivanti Service Manager (ISM) アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png
