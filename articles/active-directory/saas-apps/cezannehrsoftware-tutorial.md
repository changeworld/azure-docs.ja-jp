---
title: 'チュートリアル: Azure Active Directory と Cezanne HR Software の統合 | Microsoft Docs'
description: Azure Active Directory と Cezanne HR Software の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: jeedes
ms.openlocfilehash: 437427e80870fc4f86a6f5953a2ac4a29affed0b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349131"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>チュートリアル: Azure Active Directory と Cezanne HR Software の統合

このチュートリアルでは、Cezanne HR Software を Azure Active Directory (Azure AD) と統合する方法について説明します。 Cezanne HR Software を Azure AD と統合すると、次のことができます。

* Cezanne HR Software にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Cezanne HR Software に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Cezanne HR Software のシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Cezanne HR Software により、**SP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-cezanne-hr-software-from-the-gallery"></a>ギャラリーからの Cezanne HR Software の追加

Azure AD への Cezanne HR Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Cezanne HR Software を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Cezanne HR Software**」と入力します。
1. 結果のパネルから **Cezanne HR Software** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-cezanne-hr-software"></a>Cezanne HR Software に対して Azure AD SSO を構成し、テストする

**B.Simon** というテスト ユーザーを使用して、Cezanne HR Software に Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Cezanne HR Software の関連ユーザーとの間にリンク関係を確立する必要があります。

Azure AD SSO と Cezanne HR Software を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Cezanne HR Software の SSO の構成](#configure-cezanne-hr-software-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Cezanne HR Software のテスト ユーザーの作成](#create-cezanne-hr-software-test-user)** - Cezanne HR Software で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Cezanne HR Software** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. **[識別子 (エンティティ ID)]** ボックスに `https://w3.cezanneondemand.com/CezanneOnDemand/` という URL を入力します。

    c. **[応答 URL]** ボックスに、`https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp` のパターンを使用して URL を入力します。
    
    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と応答 URL でこれらの値を更新してください。 これらの値を入手するには、[Cezanne HR Software クライアント サポート チーム](https://cezannehr.com/services/support/)にお問い合わせください。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Cezanne HR Software のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Cezanne HR Software へのアクセスを許可して、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Cezanne HR Software]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-cezanne-hr-software-sso"></a>Cezanne HR Software の SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として Cezanne HR Software テナントにサインオンします。

2. サイド メニューの **[Administration]\(管理\)** をクリックします。 次に、 **[Security Settings]\(セキュリティの設定\)** に移動し、 **[Single Sign-On]\(シングル サインオン\)** をクリックします。

    ![スクリーンショットは、[Security Settings]\(セキュリティの設定\) と [Single Sign-On Configuration]\(シングル サインオンの構成\) が選択されている Cezanne H R Software テナントを示します。](./media/cezannehrsoftware-tutorial/settings.png)

3. **[Allow users to log in using the following Single Sign-On (SSO) Service (ユーザーに次のシングル サインオン (SSO) サービスを使用したログインを許可する)]** パネルで **[SAML 2.0]** チェック ボックスをオンにして、 **[Advanced Configuration (詳細設定)]** オプションを選択します。

    ![スクリーンショットは、[SAML 2.0] と [Advanced Configuration]\(詳細設定\) が選択されている [Allow users]\(ユーザーに許可する\) ペインを示します。](./media/cezannehrsoftware-tutorial/configuration.png)

4. **[Add New (新規追加)]** ボタンをクリックします。

    ![スクリーンショットは、[Add New]\(新規追加\) ボタンを示します。](./media/cezannehrsoftware-tutorial/new-button.png)

5. **[SAML 2.0 IDENTITY PROVIDERS]\(SAML 2.0 ID プロバイダー\)** セクションで次のフィールドの内容を入力し、 **[OK]** をクリックします。

    ![スクリーンショットは、この手順で説明した値を入力できるペインを示します。](./media/cezannehrsoftware-tutorial/identity-provider.png)

    a. **[Display Name]\(表示名\)** - 表示名として ID プロバイダーの名前を入力します。

    b. **[Entity Identifier]\(エンティティ識別子\)** - [Entity Identifier]\(エンティティ識別子\) ボックスに、Azure portal からコピーした Azure AD 識別子の値を貼り付けます。

    c. **[SAML Binding]\(SAML バインディング\)** - SAML バインディングを "POST" に変更します。

    d. **[Security Token Service Endpoint]\(セキュリティ トークン サービス エンドポイント\)** - [Security Token Service Endpoint]\(セキュリティ トークン サービス エンドポイント\) ボックスに、Azure portal からコピーしたログイン URL の値を貼り付けます。

    e. **[User ID Attribute Name]\(ユーザー ID 属性名\)** - [User ID Attribute Name]\(ユーザー ID 属性名\) ボックスに「 http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress 」と入力します。

    f. **[Public Key Certificate]\(公開キー証明書\)** - アップロード アイコンをクリックして、Azure portal からダウンロードした証明書をアップロードします。

6. [OK] をクリックします。

7. [保存] ボタンをクリックします。 

    ![スクリーンショットは、[Single Sign-on Configuration]\(シングル サインオン構成\) の [Save]\(保存\) ボタンを示します。](./media/cezannehrsoftware-tutorial/save-button.png)

### <a name="create-cezanne-hr-software-test-user"></a>Cezanne HR Software のテスト ユーザーの作成

Azure AD ユーザーが Cezanne HR Software にログインできるようにするには、ユーザーを Cezanne HR Software にプロビジョニングする必要があります。 Cezanne HR Software の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Cezanne HR Software 企業サイトに管理者としてログインします。

2. サイド メニューの **[Administration]\(管理\)** をクリックします。 次に、 **[Users]\(ユーザー\)** に移動し、 **[Add New User]\(新しいユーザーの追加\)** をクリックします

    ![[Manage Users]\(ユーザーの管理\) と [Add New User]\(新しいユーザーの追加\) が選択されている Cezanne H R Software テナントを示すスクリーンショット。](./media/cezannehrsoftware-tutorial/manage-users.png "[新しいユーザー]")

3. **[PERSON DETAILS]\(個人の詳細\)** セクションで、次の手順を実行します。

    ![スクリーンショットは、この手順で説明した値を入力できる [PERSON DETAILS]\(個人の詳細\) セクションを示します。](./media/cezannehrsoftware-tutorial/details.png "[新しいユーザー]")

    a. **[Internal User (内部ユーザー)]** をオフに設定します。

    b. 名を入力します   

    c. 姓を入力します

    d. 電子メール アドレスを入力します。

4. **[Account Information (アカウント情報)]** セクションで、次の手順を実行します。

    ![スクリーンショットは、この手順で説明した値を入力できる [Account Information]\(アカウント情報\) を示します。](./media/cezannehrsoftware-tutorial/account.png "[新しいユーザー]")

    a. **[Username]\(ユーザー名\)** ボックスに、ユーザーの電子メール (Brittasimon@contoso.com など) を入力します。

    b. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。

    c. **[Security Role (セキュリティ ロール)]** で **[HR Professional (人事担当者)]** を選択します。

    d. **[OK]** をクリックします。
    ![[OK] ボタンのスクリーンショット。](https://user-images.githubusercontent.com/80324891/115694644-f6eb5700-a358-11eb-9b23-a87a24921052.png)

5. **[Single Sign-On (シングル サインオン)]** タブに移動し、 **[SAML 2.0 Identifiers (SAML 2.0 識別子)]** 領域で **[Add New (新規追加)]** を選択します。

    ![スクリーンショットは、[Add New]\(新規追加\) を選択できる [Single Sign-On]\(シングル サインオン\) タブを示します。](./media/cezannehrsoftware-tutorial/single-sign-on.png "User")

6. **[Identity Provider]\(ID プロバイダー\)** でお使いの ID プロバイダーを選択し、 **[User Identifier]\(ユーザー識別子\)** ボックスにユーザーのメール アドレスを入力します。

    ![スクリーンショットは、ご使用の ID プロバイダーと [User Identifier]\(ユーザー識別子\) を選択できる [SAML 2.0 Identifiers]\(SAML 2.0 識別子\) を示します。](./media/cezannehrsoftware-tutorial/user-identifier.png "User")

7. **[保存]** ボタンをクリックします。

    ![スクリーンショットは、[User Settings]\(ユーザー設定\) の[Save]\(保存\) ボタンを示します。](./media/cezannehrsoftware-tutorial/save.png "User")

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Cezanne HR Software のサインオン URL にリダイレクトされます。 

* Cezanne HR Software のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで Cezanne HR Software タイルをクリックすると、Cezanne HR Software のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Cezanne HR Software を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
