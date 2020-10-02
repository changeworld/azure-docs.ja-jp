---
title: 'チュートリアル: Azure Active Directory と Ariba の統合 | Microsoft Docs'
description: Azure Active Directory と Ariba の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: 36ca2bc8ba7812229c9c22cedd294085d5a63af7
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2020
ms.locfileid: "90055199"
---
# <a name="tutorial-azure-active-directory-integration-with-ariba"></a>チュートリアル: Azure Active Directory と Ariba の統合

このチュートリアルでは、Ariba と Azure Active Directory (Azure AD) を統合する方法について説明します。
Ariba と Azure AD の統合には、次の利点があります。

* Ariba にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Ariba に自動的にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Ariba と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Ariba でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Asana では、**SP** によって開始される SSO がサポートされます

* Ariba を構成すると、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)をご覧ください。

## <a name="adding-ariba-from-the-gallery"></a>ギャラリーから Ariba を追加する

Azure AD への Ariba の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Ariba を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Ariba**」と入力します。
1. 結果のパネルから **[Ariba]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト

このセクションでは、**Britta Simon** という名前のテスト ユーザーに基づいて、Ariba で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Ariba 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Ariba で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Ariba の SSO の構成](#configure-ariba-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Ariba のテスト ユーザーの作成](#create-ariba-test-user)** - Ariba で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Ariba** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Asana のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。
    
    ```http
    https://<subdomain>.sourcing.ariba.com
    https://<subdomain>.supplier.ariba.com
    ```

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`http://<subdomain>.procurement-2.ariba.com`

    c. **[応答 URL]** に、次の URL パターンのいずれかを入力します。

    | [応答 URL]|
    |----------|
    | `https://<subdomain>.ariba.com/CUSTOM_URL` |
    | `https://<subdomain>.procurement-eu.ariba.com/CUSTOM_URL` |
    | `https://<subdomain>.procurement-eu.ariba.com` |
    | `https://<subdomain>.procurement-2.ariba.com` |
    | `https://<subdomain>.procurement-2.ariba.com/CUSTOM_URL` |

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 ここでは、識別子に一意の文字列値を使用することをお勧めします。 これらの値を取得するには、Ariba クライアント サポート チーム (**1-866-218-2155**) に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 


このセクションでは、Azure portal で B. Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「**B.Simon**」と入力します。  
   1. **[ユーザー名]** フィールドに「`<username>@<companydomain>.<extension>`」と入力します。 (例: `B.Simon@contoso.com`)。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Ariba へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Ariba]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-ariba-sso"></a>Ariba の SSO の構成

アプリケーション用に SSO を構成するには、Ariba サポート チームに電話 (**1-866-218-2155**) で連絡し、指示された方法に従って、ダウンロードした**証明書 (Base64)** ファイルを提出してください。

### <a name="create-ariba-test-user"></a>Ariba のテスト ユーザーの作成

このセクションでは、Ariba で Britta Simon というユーザーを作成します。 Ariba サポート チーム (**1-866-218-2155**) と協力して、Ariba プラットフォームでユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Ariba] タイルをクリックすると、SSO を設定した Ariba に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

