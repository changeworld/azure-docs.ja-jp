---
title: 'チュートリアル: Azure Active Directory と Rackspace SSO の統合 | Microsoft Docs'
description: Azure Active Directory と Rackspace SSO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2021
ms.author: jeedes
ms.openlocfilehash: 5fffea39509166bcf600537372f0f37f1534308c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306932"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>チュートリアル: Azure Active Directory と Rackspace SSO の統合

このチュートリアルでは、Rackspace SSO と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Rackspace SSO を統合すると、次のことができるようになります。

* Rackspace SSO にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Rackspace SSO に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Rackspace SSO と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Rackspace SSO でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Rackspace SSO では、**SP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-rackspace-sso-from-the-gallery"></a>ギャラリーから Rackspace SSO を追加する

Azure AD への Rackspace SSO の統合を構成するに、ギャラリーから管理対象 SaaS アプリの一覧に Rackspace SSO を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Rackspace SSO**」と入力します。
1. 結果のパネルから **[Rackspace SSO]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-rackspace-sso"></a>Rackspace SSO の Azure AD SSO の構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Rackspace SSO で Azure AD のシングル サインオンを構成し、テストします。
Rackspace でシングル サインオンを使用する場合、Rackspace ユーザーは、Rackspace ポータルに初めてサインインしたときに自動的に作成されます。 

Rackspace SSO で Azure AD のシングル サインオンを構成してテストするには、次の手順を行う必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Rackspace SSO のシングル サインオンの構成](#configure-rackspace-sso-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Rackspace コントロール パネルでの属性マッピングの設定](#set-up-attribute-mapping-in-the-rackspace-control-panel)** - Rackspace のロールを Azure AD ユーザーに割り当てます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Rackspace SSO** アプリケーション統合ページで **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、適切な [URL](https://login.rackspace.com/federate/sp.xml) からダウンロードできる **サービス プロバイダー メタデータ ファイル** をアップロードし、次の手順を実行します。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![スクリーンショットは、[メタデータ ファイルをアップロードする] リンクを含む、[基本的な SAML 構成] を示しています。](common/upload-metadata.png)

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![スクリーンショットは、ファイルを選択してアップロードできるダイアログ ボックスを示しています。](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、必要な URL が自動的に設定されます。

    d. **[サインオン URL]** テキスト ボックスに、URL として「`https://login.rackspace.com/federate/`」と入力します。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

このファイルが Rackspace にアップロードされて、必要な ID フェデレーションの構成設定が読み込まれます。

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

このセクションでは、B.Simon に Rackspace SSO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Rackspace SSO]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-rackspace-sso-single-sign-on"></a>Rackspace SSO のシングル サインオンを構成する

**Rackspace SSO** 側でシングル サインオンを構成するには:

1. [コントロール パネルへの ID プロバイダーの追加](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)に関するドキュメントを参照します。
1. その手順に従って以下を実行します。
    1. 新しい ID プロバイダーを作成します。
    1. ユーザーがサインイン時に会社を特定するために使用するメール ドメインを指定します。
    1. Azure コントロール パネルから先にダウンロードした **フェデレーション メタデータ XML** をアップロードします。

これにより、Azure と Rackspace が連携するために必要となる基本的な SSO 設定が正しく構成されます。

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Rackspace コントロール パネルでの属性マッピングの設定

Rackspace は、**属性マッピング ポリシー** を使用して Rackspace のロールやグループをシングル サインオン ユーザーに割り当てます。 Azure AD の SAML 要求は、**属性マッピング ポリシー** によって、Rackspace に必要なユーザー構成フィールドに変換されます。 詳細については、Rackspace の[属性マッピングの基礎に関するドキュメント](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)を参照してください。 いくつかの考慮事項があります。

* Azure AD グループを使用して、さまざまなレベルの Rackspace アクセスを割り当てたい場合は、Azure **Rackspace SSO** のシングル サインオン設定でグループ要求を有効にする必要があります。 それらのグループは、**属性マッピング ポリシー** を使用して、Rackspace の適切なロールとグループに対応付けられます。

    ![グループ要求の設定](common/sso-groups-claim.png)

* 既定では、Azure AD が Azure AD グループの (名前ではなく) UID を SAML 要求で送信します。 ただし、オンプレミスの Active Directory を Azure AD と同期している場合は、実際のグループの名前を送信することもできます。

    ![グループ要求の名前の設定](common/sso-groups-claims-names.png)

次の例の **属性マッピング ポリシー** では、以下を示しています。
1. Rackspace ユーザーの名前を `user.name` SAML 要求に設定します。 任意の要求を使用できますが、これはユーザーのメール アドレスを含んだフィールドに設定するのが最も一般的です。
1. グループ名またはグループ UID で Azure AD グループを突き合わせることによって、Rackspace のロールである `admin` と `billing:admin` をユーザーに設定します。 `roles` フィールドには `"{0}"` の "*代入*" が使用され、`remote` ルール式の結果で置き換えられます。
1. SAML 交換で標準的な既知の SAML 要求を探すことによって Rackspace が追加の SAML フィールドを取得できるよう、" *既定の代入*" である `"{D}"` を使用します。

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> ポリシー ファイルの編集する際は YAML 構文が検証されるテキスト エディターを必ず使用してください。

その他の例については、Rackspace の[属性マッピングの基礎に関するドキュメント](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)を参照してください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Rackspace SSO のサインオン URL にリダイレクトされます。 

* Rackspace SSO のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリの [Rackspace SSO] タイルをクリックすると、Rackspace SSO のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

**Rackspace SSO** のシングル サインオン設定にある **[Validate]\(検証\)** ボタンを使用することもできます。

   ![SSO の検証ボタン](common/sso-validate-sign-on.png)

## <a name="next-steps"></a>次の手順

Rackspace SSO を構成した後、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
