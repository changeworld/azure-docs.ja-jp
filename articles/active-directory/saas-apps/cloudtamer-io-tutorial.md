---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と cloudtamer.io の統合 | Microsoft Docs'
description: Azure Active Directory と cloudtamer.io の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2021
ms.author: jeedes
ms.openlocfilehash: 329121332449316f3582ee38c11ad843b25ab812
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054127"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudtamerio"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と cloudtamer.io の統合

このチュートリアルでは、cloudtamer.io と Azure Active Directory (Azure AD) を統合する方法について説明します。 cloudtamer.io を Azure AD と統合すると、次のことができます。

* cloudtamer.io にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して cloudtamer.io に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* cloudtamer.io でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* cloudtamer.io では、**SP および IDP** Initiated SSO がサポートされます。
* cloudtamer.io では、**Just In Time** ユーザー プロビジョニングがサポートされます。


## <a name="adding-cloudtamerio-from-the-gallery"></a>ギャラリーからの cloudtamer.io の追加

Azure AD への cloudtamer.io の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に cloudtamer.io を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**cloudtamer.io**」と入力します。
1. 結果パネルで **[cloudtamer.io]** を選択して、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-cloudtamerio"></a>cloudtamer.io の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、cloudtamer.io に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと cloudtamer.io の関連ユーザーとの間にリンク リレーションシップを確立する必要があります。

cloudtamer.io に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[cloudtamer.io の SSO の構成](#configure-cloudtamerio-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[cloudtamer.io のテスト ユーザーの作成](#create-cloudtamerio-test-user)** - cloudtamer.io で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。
1. **[グループ アサーション](#group-assertions)** - Azure AD と cloudtamer.io に対してグループ アサーションを設定します。

### <a name="begin-cloudtamerio-sso-configuration"></a>cloudtamer.io SSO 構成を開始する

1. cloudtamer.io の Web サイトに管理者としてログインします。

1. 右上隅にある **[+]** (プラス) アイコンをクリックして、 **[IDMS]** を選択します。

    ![IDMS の作成のスクリーンショット。](./media/cloudtamer-io-tutorial/idms-creation.png)

1. IDMS の種類として **[SAML 2.0]** を選択します。

1. この画面を開いたままにして、この画面の値を Azure AD 構成にコピーします。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **cloudtamer.io** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** テキスト ボックスで、cloudtamer.io からの **[SERVICE PROVIDER ISSUER (ENTITY ID)]\(サービス プロバイダー発行者 (エンティティ ID)\)** をこのボックスに貼り付けます。

    b. **[応答 URL]** テキスト ボックスで、cloudtamer.io からの **[SERVICE PROVIDER ACS URL]\(サービス プロバイダー ACS URL\)** をこのボックスに貼り付けます。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスで、cloudtamer.io からの **[SERVICE PROVIDER ACS URL]\(サービス プロバイダー ACS URL\)** をこのボックスに貼り付けます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[cloudtamer.io のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に cloudtamer.io へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[cloudtamer.io]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-cloudtamerio-sso"></a>cloudtamer.io SSO の構成

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


### <a name="create-cloudtamerio-test-user"></a>cloudtamer.io テストユーザーの作成

このセクションでは、Britta Simon というユーザーを cloudtamer.io に作成します。 cloudtamer.io では、Just-In-Time ユーザー プロビジョニングがサポートされていて、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 cloudtamer.io にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる cloudtamer.io のサインオン URL にリダイレクトされます。  

* cloudtamer.io のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した cloudtamer.io に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリの [cloudtamer.io] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した cloudtamer.io に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="group-assertions"></a>グループ アサーション

既存の Azure Active Directory グループを使用して cloudtamer.io ユーザーのアクセス許可を簡単に管理するには、次の手順を実行します。

### <a name="azure-ad-configuration"></a>Azure AD 構成

1. Azure portal で、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に移動します。
1. 一覧で、cloudtamer.io のエンタープライズ アプリケーションを選択します。
1. **[概要]** の左側のメニューで、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン]** で、 **[User Attributes & Claims]\(ユーザー属性と要求\)** の下にある **[編集]** を選択します。
1. **[グループ要求を追加する]** を選択します。 
   > [!NOTE]
   > グループ要求は 1 つだけ保持できます。 このオプションが無効になっている場合は、グループ要求が既に定義されている可能性があります。
1. **[グループ要求]** に対して、要求で返される必要があるグループを選択します。
   - cloudtamer.io で使用する予定であるすべてのグループをこのエンタープライズ アプリケーションに常に割り当てておく場合は、 **[アプリケーションに割り当てられているグループ]** を選択します。
   - すべてのグループを表示する場合 (この選択によってグループ アサーションが大量に発生し、制限の対象になることがあります) は、 **[アプリケーションに割り当てられているグループ]** を選択します。
1. **[ソース属性]** については、既定の **グループ ID** をままにします。
1. **[グループ要求の名前をカスタマイズする]** チェック ボックスをオンにします。
1. **[名前]** に「**memberOf**」と入力します。
1. **[保存]** を選択して、Azure AD での構成を完了します。

### <a name="cloudtamerio-configuration"></a>cloudtamer.io の構成

1. cloudtamer.io で、 **[Users]\(ユーザー\)**  >  **[Identity Management Systems]\(ID 管理システム\)** の順に移動します。
1. Azure AD 用に作成した IDMS を選択します。
1. 概要ページで、 **[User Group Associations]\(ユーザー グループの関連付け\)** タブを選択します。
1. 必要なユーザー グループ マッピングごとに、次の手順を実行します。
   1. **[Add]\(追加\)**  >  **[Add New]\(新規追加\)** の順に選択します。
   1. 表示されるダイアログで、次の操作を行います。
      1. **[Name]\(名前\)** に「**memberOf**」と入力します。
      1. **[Regex]** には、一致させたいグループの (Azure AD の) オブジェクト ID を入力します。
      1. **[User Group]\(ユーザー グループ\)** では、 **[Regex]** 内のグループにマップする cloudtamer.io 内部グループを選択します。
      1. **[Update on Login]\(ログイン時に更新\)** チェックボックスをオンにします。
   1. **[Add]\(追加\)** を選択して、グループの関連付けを追加します。

## <a name="next-steps"></a>次のステップ

cloudtamer.io を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
