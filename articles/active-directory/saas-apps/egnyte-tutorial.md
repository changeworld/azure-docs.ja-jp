---
title: チュートリアル:Azure Active Directory と Egnyte の統合 | Microsoft Docs
description: Azure Active Directory と Egnyte の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: a2ac510b28a81ffc086daf9f97e4bbe86ffb69e4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132314328"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Egnyte の統合

このチュートリアルでは、Egnyte と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Egnyte を統合すると、次のことができます。

* Egnyte にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Egnyte に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Egnyte でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Egnyte では、**SP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-egnyte-from-the-gallery"></a>ギャラリーからの Egnyte の追加

Azure AD への Egnyte の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Egnyte を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Egnyte**」と入力します。
1. 結果のパネルから **[Egnyte]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-egnyte"></a>Egnyte の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Form.com に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Form.com の関連ユーザーとの間にリンク関係を確立する必要があります。

Form.com に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Egnyte の SSO の構成](#configure-egnyte-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Egnyte のテスト ユーザーの作成](#create-egnyte-test-user)** - Egnyte で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Egnyte** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、`https://<companyname>.egnyte.com` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<companyname>.egnyte.com/samlconsumer/AzureAD` のパターンを使用して URL を入力します
    
    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と応答 URL で値を更新してください。 この値を取得するには、[Egnyte クライアント サポート チーム](https://www.egnyte.com/corp/contact_egnyte.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

5. **[Egnyte のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Egnyte へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Egnyte]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-egnyte-sso"></a>Egnyte の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Egnyte 企業サイトに管理者としてサインインします。

2. **[設定]** をクリックします。
   
    ![設定 1](./media/egnyte-tutorial/settings-tab.png "設定")

3. メニューで、[**設定**] をクリックします。

    ![メニュー 1](./media/egnyte-tutorial/menu-tab.png "メニュー")

4. **[構成]** タブをクリックし、**[セキュリティ]** をクリックします。

    ![Security](./media/egnyte-tutorial/configuration.png "セキュリティ")

5. **[シングル サインオン認証]** セクションで、以下の手順を実行します。

    ![シングル サインオン認証](./media/egnyte-tutorial/authentication.png "シングル サインオン認証")   
    
    1. [**シングル サインオン認証**] として [**SAML 2.0**] を選択します。

    1. [**Id プロバイダー**] として [**AzureAD**] を選択します。

    1. Azure portal からコピーした **ログイン URL** を **[ID プロバイダーのログイン URL]** テキスト ボックス内に貼り付けます。

    1. Azure portal からコピーした **Azure AD 識別子** を **[Identity provider entity ID]\(ID プロバイダーのエンティティ ID\)** テキスト ボックスに貼り付けます。

    1. Azure Portal からダウンロードした base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[Identity provider certificate]\(ID プロバイダー証明書\)** ボックスに貼り付けます。

    1. **[既定のユーザー マッピング]** として **[電子メール アドレス]** を選択します。

    1. **[ドメイン固有の発行者の値を使用]** として、**[無効]** を選択します。

    1. **[保存]** をクリックします。

### <a name="create-egnyte-test-user"></a>Egnyte のテスト ユーザーの作成

Azure AD ユーザーが Egnyte にサインインできるようにするには、そのユーザーを Egnyte にプロビジョニングする必要があります。 Egnyte の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Egnyte** 企業サイトに管理者としてサインインします。

2. **[設定] \> [ユーザーとグループ]** の順にクリックします。

3. **[新しいユーザーの追加]** をクリックし、追加するユーザーの種類を選択します。
   
    ![ユーザー](./media/egnyte-tutorial/add-user.png "ユーザー")

4. **[New Power User]\(新規のパワー ユーザー\)** セクションで、次の手順を実行します。
    
    ![新しい標準ユーザー](./media/egnyte-tutorial/new-user.png "[新しい標準ユーザー]")   

    a. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (例: **Brittasimon\@contoso.com**) を入力します。

    b. **[ユーザー名]** テキスト ボックスにユーザー名 (**Brittasimon** など) を入力します。

    c. **[Authentication Type]\(認証の種類\)** として **[シングル サインオン]** を選択します。
   
    d. **[保存]** をクリックします。
    
    >[!NOTE]
    >Azure Active Directory アカウント保有者に通知メールが届きます。
    >

>[!NOTE]
>他の Egnyte ユーザー アカウント作成ツールや、Egnyte から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。
>

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Egnyte のサインオン URL にリダイレクトされます。 

* Egnyte のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Egnyte] タイルをクリックすると、Egnyte のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Egnyte を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-any-app)。
