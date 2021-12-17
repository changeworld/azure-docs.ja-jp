---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と TAP App Security の統合 | Microsoft Docs'
description: Azure Active Directory と TAP App Security の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/22/2021
ms.author: jeedes
ms.openlocfilehash: d388cf863c8c37ff41338d1b1fb330ab828bd662
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282769"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tap-app-security"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と TAP App Security の統合

このチュートリアルでは、TAP App Security と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と TAP App Security を統合すると、次のことができます。

* TAP App Security にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して TAP App Security に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* TAP App Security でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* TAP App Security では、**SP** Initiated SSO がサポートされます。

* TAP App Security では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

## <a name="adding-tap-app-security-from-the-gallery"></a>ギャラリーからの TAP App Security の追加

Azure AD への TAP App Security の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に TAP App Security を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**TAP App Security**」と入力します。
1. 結果のパネルから **[TAP App Security]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-tap-app-security"></a>TAP App Security の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、TAP App Security に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと TAP App Security の関連ユーザーとの間にリンク関係を確立する必要があります。

TAP App Security に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[TAP App Security の SSO の構成](#configure-tap-app-security-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[TAP App Security のテスト ユーザーの作成](#create-tap-app-security-test-user)** - TAP App Security で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **TAP App Security** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://app.tapappsecurity.com/<CUSTOMER_DOMAIN_WITHOUT_EXTENSION>/metadata` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://app.tapappsecurity.com/<CUSTOMER_DOMAIN_WITHOUT_EXTENSION>/acs` のパターンを使用して URL を入力します

    c. **[サインオン URL]** テキスト ボックスに、URL として「`https://webapp.tapappsecurity.com/`」と入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[TAP App Security クライアント サポート チーム](mailto:support@tapappsecurity.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. TAP App Security アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、TAP App Security アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 |  ソース属性|
    | --------------- | --------- |
    | email | User.mail |
    | first | User.givenname |
    | ログイン (login) | user.userprincipalname |
    | last | User.surname |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[TAP App Security のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に TAP App Security へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[TAP App Security]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-tap-app-security-sso"></a>TAP App Security の SSO の構成

1. TAP App Security の Web サイトに管理者としてログインします。

1. 左側のナビゲーション バーで **[Single Sign-On]\(シングル サインオン\)** をクリックし、 **[Active Directory]** を選択します。

    ![シングル サインオンのスクリーンショット。](./media/tap-app-security-tutorial/active-directory.png)

1. **[Integrate Active Directory app]\(Active Directory アプリの統合\)** ボタンをクリックします。 組織のドメインを入力し、 **[Save]\(保存\)** ボタンをクリックします。

    ![Active Directory のスクリーンショット。](./media/tap-app-security-tutorial/domain.png)

1. 次のように歯車アイコンをクリックします。

    ![アイコンのスクリーンショット。](./media/tap-app-security-tutorial/gear-icon.png)

1. **[Active Directory integration]\(Active Directory 統合\)** ページで、次の手順を実行します。

    ![統合のスクリーンショット。](./media/tap-app-security-tutorial/configuration.png)

    a. **[Reply URL (Assertion Consumer Service URL)]\(応答 URL (アサーション コンシューマー サービス URL)\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** テキスト ボックスに貼り付けます。

    b. **[Identifier (Entity ID)]\(識別子 (エンティティ ID)\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** テキスト ボックスにこの値を貼り付けます。

    c. **[Choose File]\(ファイルの選択\)** をクリックし、Azure portal からダウンロードした **フェデレーション メタデータ XML** ファイルをアップロードします。

    d. **[保存]** をクリックします。

### <a name="create-tap-app-security-test-user"></a>TAP App Security のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを TAP App Security に作成します。 TAP App Security では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ TAP App Security に存在していない場合は、TAP App Security にアクセスしようとしたときに新しいユーザーが作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる TAP App Security のサインオン URL にリダイレクトされます。 

* TAP App Security のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [TAP App Security] タイルをクリックすると、TAP App Security サインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

TAP App Security を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
