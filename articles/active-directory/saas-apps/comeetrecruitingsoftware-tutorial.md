---
title: 'チュートリアル: Azure Active Directory と Comeet Recruiting Software の統合 | Microsoft Docs'
description: Azure Active Directory と Comeet Recruiting Software の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/23/2021
ms.author: jeedes
ms.openlocfilehash: d5b338446766d3049002711c9b34bd3325e7bf77
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132311556"
---
# <a name="tutorial-azure-active-directory-integration-with-comeet-recruiting-software"></a>チュートリアル: Azure Active Directory と Comeet Recruiting Software の統合

このチュートリアルでは、Comeet Recruiting Software と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Comeet Recruiting Software を統合すると、次のことができます。

* Comeet Recruiting Software にアクセスするユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで Comeet Recruiting Software に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Comeet Recruiting Software でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Comeet Recruiting Software では、**SP および IDP** Initiated SSO がサポートされます。
* Comeet Recruiting Software では、[自動化されたユーザー プロビジョニング](comeet-recruiting-software-provisioning-tutorial.md)がサポートされます。


## <a name="add-comeet-recruiting-software-from-the-gallery"></a>ギャラリーから Comeet Recruiting Software を追加する

Azure AD への Comeet Recruiting Software の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Comeet Recruiting Software を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Comeet Recruiting Software**」と入力します。
1. 結果のパネルから **[Comeet Recruiting Software]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-comeet-recruiting-software"></a>Comeet Recruiting Software のための Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Comeet Recruiting Software 用に Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Comeet Recruiting Software の関連ユーザーとの間にリンク関係を確立する必要があります。

Comeet Recruiting Software 用に Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Comeet Recruiting Software SSO の構成](#configure-comeet-recruiting-software-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Comeet Recruiting Software のテスト ユーザーの作成](#create-comeet-recruiting-software-test-user)** - Comeet Recruiting Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Comeet Recruiting Software** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を入手するには、[Comeet Recruiting Software クライアント サポート チーム](https://support.comeet.co/knowledgebase/adfs-single-sign-on/)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに URL として「`https://app.comeet.co`」と入力します。

5. Comeet Recruiting Software アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![[編集] ボタンが選択されている [ユーザー属性] セクションを示すスクリーンショット。](common/edit-attribute.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、**編集アイコン** を使用して要求を編集するか、 **[新しい要求の追加]** を使用して要求を追加することで、上の図のように SAML トークン属性を構成し、次の手順を実行します。 

    | 名前 |  ソース属性|
    | ---------------| --------------- |
    | nameidentifier | User.mail |
    | comeet_id | user.userprincipalname |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![[新しい要求の追加] および [保存] アクションが強調表示されている [ユーザーの要求] セクションを示すスクリーンショット。](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[保存]** をクリックします。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Set up Comeet Recruiting Software]\(Comeet Recruiting Software の設定\)** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Comeet Recruiting Software へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Comeet Recruiting Software]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-comeet-recruiting-software-sso"></a>Comeet Recruiting Software SSO を構成する

**Comeet Recruiting Software** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [Comeet Recruiting Software サポート チーム](https://support.comeet.co/knowledgebase/adfs-single-sign-on/)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-comeet-recruiting-software-test-user"></a>Comeet Recruiting Software テスト ユーザーの作成

このセクションでは、Comeet Recruiting Software で Britta Simon というユーザーを作成します。 [Comeet Recruiting Software サポート チーム](mailto:support@comeet.co)と連携し、Comeet Recruiting Software プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

Comeet Recruiting Software では、自動化されたユーザー プロビジョニングもサポートされます。自動化されたユーザー プロビジョニングを構成する方法の詳細については、[こちら](./comeet-recruiting-software-provisioning-tutorial.md)をご覧ください。

### <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

SP Initiated:

* Azure portal で [このアプリケーションをテストします] をクリックします。 これにより、ログイン フローを開始できる Comeet Recruiting Software のサインオン URL にリダイレクトされます。

* Comeet Recruiting Software のサインオン URL に直接移動し、そこからログイン フローを開始します。

IDP Initiated:

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Comeet Recruiting Software に自動的にサインインされます。

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Comeet Recruiting Software] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Comeet Recruiting Software に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Comeet Recruiting Software を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
