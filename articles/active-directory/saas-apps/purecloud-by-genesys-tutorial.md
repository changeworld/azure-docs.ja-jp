---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Genesys Cloud for Azure の統合 | Microsoft Docs'
description: Azure Active Directory と Genesys Cloud for Azure の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: e3e5cc70f889eb24d50e67beb95a1e6324cecb02
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341732"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-genesys-cloud-for-azure"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Genesys Cloud for Azure の統合

このチュートリアルでは、Genesys Cloud for Azure と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Genesys Cloud for Azure を統合すると、次のことができます。

* Genesys Cloud for Azure にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Genesys Cloud for Azure に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 お持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Genesys Cloud for Azure でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Genesys Cloud for Azure では、**SP および IDP** Initiated SSO がサポートされます。

* Genesys Cloud for Azure では、[自動化されたユーザー プロビジョニング](purecloud-by-genesys-provisioning-tutorial.md)がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-genesys-cloud-for-azure-from-the-gallery"></a>ギャラリーからの Genesys Cloud for Azure の追加

Azure AD への Genesys Cloud for Azure の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Genesys Cloud for Azure を追加する必要があります。 そのためには、次の手順に従います。

1. 職場または学校アカウントを使用するか、個人用 Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに **[Genesys Cloud for Azure]** と入力します。
1. 結果のパネルから **[Genesys Cloud for Azure]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-genesys-cloud-for-azure"></a>Genesys Cloud for Azure の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Genesys Cloud for Azure で Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Genesys Cloud for Azure の関連ユーザーとの間にリンク関係を確立する必要があります。

Genesys Cloud for Azure で Azure AD SSO を構成してテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Genesys Cloud for Azure SSO の構成](#configure-genesys-cloud-for-azure-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Genesys Cloud for Azure のテスト ユーザーの作成](#create-genesys-cloud-for-azure-test-user)** - Genesys Cloud for Azure で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

Azure portal で Azure AD SSO を有効にするには、これらの手順を実行します。

1. Azure portal の **Genesys Cloud for Azure** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** Initiated モードで構成する場合は、次の手順を行います。

    a. **[識別子]** ボックスに、自分のリージョンに対応する URL を入力します。
    
    | 識別子 |
    |---|
    | https://login.mypurecloud.com/saml |
    | https://login.mypurecloud.de/saml |
    | https://login.mypurecloud.jp/saml |
    | https://login.mypurecloud.ie/saml |
    | https://login.mypurecloud.com.au/saml |
    |

    b. **[応答 URL]** ボックスに、自分のリージョンに対応する URL を入力します。

    | [応答 URL] |
    |---|
    | https://login.mypurecloud.com/saml |
    | https://login.mypurecloud.de/saml |
    | https://login.mypurecloud.jp/saml |
    | https://login.mypurecloud.ie/saml |
    | https://login.mypurecloud.com.au/saml |
    |

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** を選択して次の手順を実行します。

    **[サインオン URL]** ボックスに、自分のリージョンに対応する URL を入力します。
    
    |[サインオン URL] |
    |---|
    | https://login.mypurecloud.com |
    | https://login.mypurecloud.de |
    | https://login.mypurecloud.jp |
    | https://login.mypurecloud.ie |
    | https://login.mypurecloud.com.au |
    |

1. Genesys Cloud for Azure アプリケーションでは、特定の形式の SAML アサーションを受け取るため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Genesys Cloud for Azure アプリケーションでは、次の表に示すとおり、いくつかの属性が SAML 応答で返されることが想定されています。 これらの属性も値が事前に設定されますが、必要に応じてそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ---------------| --------------- |
    | Email | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Genesys Cloud for Azure のセットアップ]** セクションで、要件に基づいて適切な 1 つ以上の URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で B. Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに、username@companydomain.extension という形式でユーザー名を入力します。 (例: `B.Simon@contoso.com`)。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Genesys Cloud for Azure へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Genesys Cloud for Azure]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-genesys-cloud-for-azure-sso"></a>Genesys Cloud for Azure SSO の構成

1. 別の Web ブラウザー ウィンドウで、Genesys Cloud for Azure に管理者としてサインインします。

1. 上部の **[Admin] (管理)** を選択し、 **[Integrations] (統合)** の **[Single Sign-on] (シングル サインオン)** に移動します。

    ![[PureCloud Admin]\(PureCloud 管理\) ウィンドウのスクリーンショット。ここで [Single Sign-on]\(シングル サインオン\) を選択することができます。](./media/purecloud-by-genesys-tutorial/configure-1.png)

1. **[ADFS/Azure AD (Premium)]** タブに切り替えて、次の手順に従います。

    ![[Integrations]\(統合\) ページのスクリーンショット。ここで、説明されている値を入力できます。](./media/purecloud-by-genesys-tutorial/configure-2.png)

    a. **[Browse] (参照)** を選択して、Azure portal からダウンロードした base 64 でエンコードされた証明書を **[ADFS Certificate] (ADFS 証明書)** にアップロードします。

    b. **[ADFS Issuer URI] (ADFS 発行者の URI)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    c. **[Target URI] (ターゲット URI)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. **[証明書利用者 ID ]** の値については、Azure portal に移動し、**Genesys Cloud for Azure** アプリケーション統合ページで **[プロパティ]** タブを選択して、 **[アプリケーション ID]** の値をコピーします。 それを **[Relying Party Identifier] (証明書利用者識別子)** ボックスに貼り付けます。

    ![アプリケーション ID の値が表示される [プロパティ] ペインのスクリーンショット。](./media/purecloud-by-genesys-tutorial/configuration.png)

    e. **[保存]** を選択します。

### <a name="create-genesys-cloud-for-azure-test-user"></a>Genesys Cloud for Azure のテスト ユーザーを作成する

Azure AD ユーザーを Genesys Cloud for Azure にサインインできるようにするには、そのユーザーを Genesys Cloud for Azure にプロビジョニングする必要があります。 Genesys Cloud for Azure では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順のようにします。**

1. 管理者として Genesys Cloud for Azure にログインします。

1. 上部の **[Admin] (管理)** を選択し、 **[People & Permissions] (ユーザーとアクセス許可)** の **[People] (ユーザー)** に移動します。

    ![[PureCloud Admin]\(PureCloud 管理\) ウィンドウのスクリーンショット。ここで [People]\(ユーザー\) を選択することができます。](./media/purecloud-by-genesys-tutorial/configure-3.png)

1. **[People] (ユーザー)** ページで、 **[Add Person] (ユーザーの追加)** を選択します。

    ![ユーザーを追加できる [People]\(ユーザー\) ページのスクリーンショット。](./media/purecloud-by-genesys-tutorial/configure-4.png)

1. **[Add People to the Organization] (組織へのユーザーの追加)** ダイアログボックスで、次の手順に従います。

    ![スクリーンショットは、説明した値を入力できるページを示しています。](./media/purecloud-by-genesys-tutorial/configure-5.png)

    a. **[Full Name] (フル ネーム)** ボックスに、ユーザーの名前を入力します。 次に例を示します。**B.simon**。

    b. **[Email] (メール)** ボックスに、ユーザーのメール アドレスを入力します。 例: **b.simon\@contoso.com**。

    c. **［作成］** を選択します

> [!NOTE]
> Genesys Cloud for Azure では、自動ユーザー プロビジョニングもサポートされます。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](./purecloud-by-genesys-provisioning-tutorial.md)をご覧ください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Genesys Cloud for Azure のサインオン URL にリダイレクトされます。  

* Genesys Cloud for Azure のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Genesys Cloud for Azure に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Genesys Cloud for Azure] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Genesys Cloud for Azure に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Genesys Cloud for Azure を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-any-app)。
