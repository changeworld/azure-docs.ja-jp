---
title: 'チュートリアル: Azure AD SSO と Humanity の統合'
description: Azure Active Directory と Humanity の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/26/2021
ms.author: jeedes
ms.openlocfilehash: 518586bb7b0e7ac54e2fa9e0f4ca76e514a5abb1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132333411"
---
# <a name="tutorial-azure-ad-sso-integration-with-humanity"></a>チュートリアル: Azure AD SSO と Humanity の統合

このチュートリアルでは、Humanity と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Humanity を統合すると、次のことができます。

* Humanity にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Humanity に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Humanity でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Humanity では、**SP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-humanity-from-the-gallery"></a>ギャラリーからの Humanity の追加

Azure AD への Humanity の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Humanity を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Humanity**」と入力します。
1. 結果のパネルから **[Humanity]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-humanity"></a>Humanity の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Humanity に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Humanity の関連ユーザーとの間にリンク関係を確立する必要があります。

Humanity に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Humanity の SSO の構成](#configure-humanity-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Humanity のテスト ユーザーの作成](#create-humanity-test-user)** - Humanity で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Humanity** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** ボックスに `https://company.humanity.com/app/` という URL を入力します。

    b. **[サインオン URL]** ボックスに、URL として「`https://company.humanity.com/includes/saml/`」と入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際の識別子とサインオン URL で更新してください。 これらの値を取得するには、[Humanity クライアント サポート チーム](https://www.humanity.com/support/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Humanity のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Humanity へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Humanity]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-humanity-sso"></a>Humanity SSO の構成

1. 別の Web ブラウザー ウィンドウで、**Humanity** 企業サイトに管理者としてログインします。

2. 上部のメニューで **[Admin]** をクリックします。

    ![管理者](./media/shiftplanning-tutorial/menu.png "[Admin]")

3. **[Integration]** の **[Single Sign-On]** をクリックします。

    ![[Integration]\(統合\) メニューの [Single Sign-On]\(シングル サインオン\) が選択されている画面のスクリーンショット。](./media/shiftplanning-tutorial/integration.png "[Single Sign-On]")

4. **[Single Sign-On Options]** セクションで、次の手順に従います。

    ![[Single Sign-On]\(シングル サインオン\) セクションのスクリーンショット。ここで、説明されている値を入力できます。](./media/shiftplanning-tutorial/settings.png "[Single Sign-On]")

    a. **[SAML Enabled]** を選択します。

    b. **[Allow Password Login]** を選択します。

    c. **[SAML Issuer URL]\(SAML 発行者の URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. **[Remote Logout URL]\(リモート ログアウト URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    e. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[X.509 証明書]** テキストボックスに貼り付けます。

    f. **[設定の保存]** をクリックします。

### <a name="create-humanity-test-user"></a>Humanity のテスト ユーザーの作成

Azure AD ユーザーが Humanity にログインできるようにするには、ユーザーを Humanity にプロビジョニングする必要があります。 Humanity の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Humanity** 企業サイトに管理者としてログインします。

2. **[Admin]** をクリックします。

    ![管理者](./media/shiftplanning-tutorial/menu.png "[Admin]")

3. **[Staff]** をクリックします。

    ![スタッフ](./media/shiftplanning-tutorial/profile.png "[Staff]")

4. **[Actions]\(アクション\)** の **[Add Employee]\(従業員の追加\)** をクリックします。

    ![[Add Employee]\(従業員の追加\)](./media/shiftplanning-tutorial/actions.png "[Add Employees]")

5. **[Add Employees]** セクションで、次の手順に従います。

    ![[Save Employees]\(従業員の保存\)](./media/shiftplanning-tutorial/accounts.png "[Save Employees]")

    a. プロビジョニングする有効な Azure AD アカウントの **姓**、**名**、**電子メール アドレス** を、対応するテキスト ボックスに入力します。

    b. **[Save Employees]** をクリックします。

> [!NOTE]
> 他の Humanity ユーザー アカウント作成ツールや、Humanity から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Humanity のサインオン URL にリダイレクトされます。 

* Humanity のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Humanity] タイルをクリックすると、Humanity のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Humanity を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
