---
title: 'チュートリアル: Azure AD SSO と Kion (旧称 cloudtamer.io) の統合'
description: Azure Active Directory と Kion (旧称 cloudtamer.io) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/11/2021
ms.author: jeedes
ms.openlocfilehash: d43a4875e71cf40ab7439d3afcd49fadca86d06b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132710763"
---
# <a name="tutorial-azure-ad-sso-integration-with-kion-formerly-cloudtamerio"></a>チュートリアル: Azure AD SSO と Kion (旧称 cloudtamer.io) の統合

このチュートリアルでは、Kion と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Kion を統合すると、次のことが可能になります。

* Kion にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Kion に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Kion のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Kion では、**SP と IDP** によって開始される SSO がサポートされます
* Kion では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。


## <a name="adding-kion-formerly-cloudtamerio-from-the-gallery"></a>ギャラリーからの Kion (旧称 cloudtamer.io) の追加

Azure AD への Kion の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Kion を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Kion**」と入力します。
1. 結果パネルから **[Kion]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-kion-formerly-cloudtamerio"></a>Kion (旧称: cloudtamer.io) のための Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Kion 用に Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Kion の関連ユーザーとの間にリンク関係を確立する必要があります。

Kion 用に Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Kion SSO の構成](#configure-kion-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Kion のテスト ユーザーの作成](#create-kion-test-user)** - Kion で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。
1. **[グループ アサーション](#group-assertions)** - Azure AD と Kion に対してグループ アサーションを設定します。

### <a name="begin-kion-sso-configuration"></a>Kion SSO の構成を開始する

1. Kion の Web サイトに管理者としてログインします。

1. 右上隅にある **[+]** (プラス) アイコンをクリックして、 **[IDMS]** を選択します。

    ![IDMS の作成のスクリーンショット。](./media/cloudtamer-io-tutorial/idms-creation.png)

1. IDMS の種類として **[SAML 2.0]** を選択します。

1. この画面を開いたままにして、この画面の値を Azure AD 構成にコピーします。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Kion** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** テキスト ボックスで、Kion からの **[SERVICE PROVIDER ISSUER (ENTITY ID)]\(サービス プロバイダー発行者 (エンティティ ID)\)** をこのボックスに貼り付けます。

    b. **[応答 URL]** テキスト ボックスで、Kion からの **[SERVICE PROVIDER ACS URL]\(サービス プロバイダー ACS URL\)** をこのボックスに貼り付けます。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスで、Kion からの **[SERVICE PROVIDER ACS URL]\(サービス プロバイダー ACS URL\)** をこのボックスに貼り付けます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Kion のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Kion へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Kion]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-kion-sso"></a>Kion SSO を構成する

1. **[Add IDMS]\(IDMS の追加\)** ページで、次の手順を行います。

    ![IDMS の追加を示すスクリーンショット。](./media/cloudtamer-io-tutorial/configuration.png)

    a。 **[IDMS Name]\(IDMS 名\)** に、ユーザーがログイン画面から認識する名前を指定します。

    b. **[IDENTITY PROVIDER ISSUER (ENTITY ID)]\(ID プロバイダー発行者 (エンティティ ID)\)** テキストボックスに、Azure portal からコピーした **識別子** を貼り付けます。

    c. Azure portal からダウンロードした **フェデレーション メタデータ XML** をメモ帳で開き、その内容を **[IDENTITY PROVIDER METADATA]\(ID プロバイダーのメタデータ\)** テキストボックスに貼り付けます。

    d. **[SERVICE PROVIDER ISSUER (ENTITY ID)]\(サービス プロバイダー発行者 (エンティティ ID)\)** の値をコピーし、この値を Azure portal の [基本的な SAML 構成] セクションの **[識別子]** テキスト ボックスに貼り付けます。

    e. **[SERVICE PROVIDER ACS URL]\(サービス プロバイダー ACS URL\)** の値をコピーし、その値を Azure portal の [基本的な SAML 構成] セクションにある **[応答 URL]** ボックスに貼り付けます。

    f. [Assertion Mapping]\(アサーション マッピング\) で、次の値を入力します。

    | フィールド | 値 |
    |-----------|-------|
    | 名 | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |
    | 姓 | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |
    | Email | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
    |  ユーザー名 | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
    |

1. **[Create IDMS]\(IDMS の作成\)** をクリックします。


### <a name="create-kion-test-user"></a>Kion テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Kion に作成します。 Kion では、Just-In-Time ユーザー プロビジョニングがサポートされています。これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Kion にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Kion のサインオン URL にリダイレクトされます。  

* Kion のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Kion に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Kion] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Kion に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="group-assertions"></a>グループ アサーション

既存の Azure Active Directory グループを使用して Kion ユーザーのアクセス許可を簡単に管理するには、次の手順を実行します。

### <a name="azure-ad-configuration"></a>Azure AD 構成

1. Azure portal で、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に移動します。
1. 一覧で、Kion のエンタープライズ アプリケーションを選択します。
1. **[概要]** の左側のメニューで、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン]** で、 **[User Attributes & Claims]\(ユーザー属性と要求\)** の下にある **[編集]** を選択します。
1. **[グループ要求を追加する]** を選択します。 
   > [!NOTE]
   > グループ要求は 1 つだけ保持できます。 このオプションが無効になっている場合は、グループ要求が既に定義されている可能性があります。
1. **[グループ要求]** に対して、要求で返される必要があるグループを選択します。
   - Kion で使用する予定であるすべてのグループをこのエンタープライズ アプリケーションに常に割り当てておく場合は、 **[アプリケーションに割り当てられているグループ]** を選択します。
   - すべてのグループを表示する場合 (この選択によってグループ アサーションが大量に発生し、制限の対象になることがあります) は、 **[アプリケーションに割り当てられているグループ]** を選択します。
1. **[ソース属性]** については、既定の **グループ ID** をままにします。
1. **[グループ要求の名前をカスタマイズする]** チェック ボックスをオンにします。
1. **[名前]** に「**memberOf**」と入力します。
1. **[保存]** を選択して、Azure AD での構成を完了します。

### <a name="kion-configuration"></a>Kion の構成

1. Kion で、 **[Users]\(ユーザー\)**  >  **[Identity Management Systems]\(ID 管理システム\)** の順に移動します。
1. Azure AD 用に作成した IDMS を選択します。
1. 概要ページで、 **[User Group Associations]\(ユーザー グループの関連付け\)** タブを選択します。
1. 必要なユーザー グループ マッピングごとに、次の手順を実行します。
   1. **[Add]\(追加\)**  >  **[Add New]\(新規追加\)** の順に選択します。
   1. 表示されるダイアログで、次の操作を行います。
      1. **[Name]\(名前\)** に「**memberOf**」と入力します。
      1. **[Regex]** には、一致させたいグループの (Azure AD の) オブジェクト ID を入力します。
      1. **[User Group]\(ユーザー グループ\)** では、 **[Regex]** 内のグループにマップする Kion 内部グループを選択します。
      1. **[Update on Login]\(ログイン時に更新\)** チェックボックスをオンにします。
   1. **[Add]\(追加\)** を選択して、グループの関連付けを追加します。

## <a name="next-steps"></a>次のステップ

Kion を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。