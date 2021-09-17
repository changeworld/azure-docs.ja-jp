---
title: 'チュートリアル: Azure Active Directory と RFPIO の統合 | Microsoft Docs'
description: Azure Active Directory と RFPIO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/27/2021
ms.author: jeedes
ms.openlocfilehash: f3bed54fb44e73742f05796e98984519462d9123
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728321"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>チュートリアル: Azure Active Directory と RFPIO の統合

このチュートリアルでは、RFPIO と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と RFPIO を統合すると、次のことが可能になります。

* RFPIO にアクセスできる Azure AD ユーザーを制御します。
* ユーザーが自分の Azure AD アカウントを使用して RFPIO に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

RFPIO と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* RFPIO でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* RFPIO では、**SP と IDP** によって開始される SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-rfpio-from-the-gallery"></a>ギャラリーからの RFPIO の追加

Azure AD への RFPIO の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に RFPIO を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**RFPIO**」と入力します。
1. 結果のパネルから **[RFPIO]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-rfpio"></a>RFPIO のための Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、RFPIO 用に Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと RFPIO の関連ユーザーとの間にリンク関係を確立する必要があります。

RFPIO 用に Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[RFPIO SSO の構成](#configure-rfpio-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[RFPIO のテスト ユーザーの作成](#create-rfpio-test-user)** - RFPIO で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **RFPIO** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://www.rfpio.com` という URL を入力します。

    b. **[追加の URL を設定します]** をクリックします。

    c. **[リレー状態]** テキストボックスに文字列値を入力します。 この値を取得するには、[RFPIO サポート チーム](https://www.rfpio.com/contact/)に問い合わせてください。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://www.app.rfpio.com`」と入力します。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[RFPIO のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に RFPIO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[RFPIO]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-rfpio-sso"></a>RFPIO SSO の構成

1. 別の Web ブラウザー ウィンドウで、**RFPIO** Web サイトに管理者としてサインインします。

1. 左下隅にあるドロップダウンをクリックします。

    ![ペイン下部の下矢印を示すスクリーンショット。](./media/rfpio-tutorial/app.png)

1. **[組織設定]** をクリックします。 

    ![[組織設定] が選択された画面のスクリーンショット。](./media/rfpio-tutorial/organization.png)

1. **[FEATURES & INTEGRATION (機能と統合)]** をクリックします。

    ![[Settings]\(設定\) の [Features and Integration]\(機能と統合\) が選択されている画面のスクリーンショット。](./media/rfpio-tutorial/features.png)

1. **[SAML SSO 構成]** で **[編集]** をクリックします。

    ![[SAML S S O 構成] と [編集] ボタンが強調表示されている画面のスクリーンショット。](./media/rfpio-tutorial/edit-button.png)

1. このセクションでは、次のアクション実行します。

    ![SAML が有効になっている [SAML SSO 構成] を示すスクリーンショット。](./media/rfpio-tutorial/configuration.png)
    
    a. **ダウンロードしたメタデータ XML** の内容をコピーし、 **[ID 構成]** フィールドに貼り付けます。

    > [!NOTE]
    > ダウンロードした **フェデレーション メタデータ XML** の内容をコピーするには、**Notepad++** または適切な **XML エディター** を使用します。

    b. **[検証]** をクリックします。

    c. **[検証]** をクリックした後、 **[SAML(Enabled)(SAML (有効))]** をフリップしてオンにします。

    d. **[送信]** をクリックします。

### <a name="create-rfpio-test-user"></a>RFPIO のテスト ユーザーの作成

1. RFPIO 企業サイトに管理者としてサインインします。

1. 左下隅にあるドロップダウンをクリックします。

    ![ペイン下部の下矢印を示すスクリーンショット。](./media/rfpio-tutorial/app.png)

1. **[組織設定]** をクリックします。 

    ![[組織設定] が選択された画面のスクリーンショット。](./media/rfpio-tutorial/organization.png)

1. **[チーム メンバー]** をクリックします。

    ![[設定] の [チーム メンバー] が選択されている画面のスクリーンショット。](./media/rfpio-tutorial/members.png)

1. **[メンバーの追加]** をクリックします。

    ![[メンバーの追加] ボタンを示す画面のスクリーンショット。](./media/rfpio-tutorial/add-members.png)

1. **[新しいメンバーの追加]** セクションで、 次の操作を実行します。

    ![[新しいメンバーの追加] を示すスクリーンショット。ここで、説明されている値を入力できます。](./media/rfpio-tutorial/new-members.png)

    a. **[Enter one email per line(1 行につき 1 つの電子メール アドレスを入力する)]** フィールドに **電子メール アドレス** を入力します。

    b. 要件に応じて **[ロール]** を選択してください。

    c. **[メンバーの追加]** をクリックします。

    > [!NOTE]
    > Azure Active Directory アカウント所有者が電子メールを受信し、リンクに従ってアカウントを確認すると、そのアカウントがアクティブになります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる RFPIO のサインオン URL にリダイレクトされます。  

* RFPIO のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した RFPIO に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [RFPIO] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した RFPIO に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

RFPIO を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。