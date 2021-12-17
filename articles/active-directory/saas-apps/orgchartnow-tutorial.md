---
title: 'チュートリアル: Azure Active Directory と OrgChart Now の統合 | Microsoft Docs'
description: Azure Active Directory と OrgChart Now の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: b326114013502840e05284ba6d7049273ea42a44
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287623"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>チュートリアル: Azure Active Directory と OrgChart Now の統合

このチュートリアルでは、OrgChart Now を Azure Active Directory (Azure AD) と統合する方法について説明します。 OrgChart Now を Azure AD と統合すると、次のことができます。

* OrgChart Now にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して OrgChart Now に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* OrgChart Now のシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* OrgChart Now により、**SP** および **IDP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-orgchart-now-from-the-gallery"></a>ギャラリーから OrgChart Now を追加する

Azure AD への OrgChart Now の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に OrgChart Now を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**OrgChart Now**」と入力します。
1. 結果パネルから **OrgChart Now** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-orgchart-now"></a>OrgChart Now に対して Azure AD SSO を構成し、テストする

**B.Simon** というテスト ユーザーを使用して、OrgChart Now に Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと OrgChart Now の関連ユーザーの間にリンク関係を確立する必要があります。

Azure AD SSO と OrgChart Now を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[OrgChart Now の SSO の構成](#configure-orgchart-now-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[OrgChart Now のテスト ユーザーの作成](#create-orgchart-now-test-user)** - OrgChart Now で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成 

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **OrgChart Now** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    **[識別子]** ボックスに、`https://sso2.orgchartnow.com` という URL を入力します。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com` という形式で URL を入力します。

    > [!NOTE]
    > `<YourEntityID>` は、 **[OrgChart Now のセットアップ]** セクションでコピーする **Azure AD の ID** です (このチュートリアルで後ほど説明します)。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[OrgChart Now のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に OrgChart Now へのアクセスを許可して、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[OrgChart Now]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-orgchart-now-sso"></a>OrgChart Now の SSO の構成

**OrgChart Now** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [OrgChart Now サポート チーム](mailto:ocnsupport@officeworksoftware.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-orgchart-now-test-user"></a>OrgChart Now のテスト ユーザーの作成

Azure AD ユーザーが OrgChart Now にログインできるようにするには、そのユーザーを OrgChart Now にプロビジョニングする必要があります。 

1. OrgChart Now では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 OrgChart Now にアクセスしようとすると、ユーザーがまだ存在しない場合は新しいユーザーが作成されます。 Just-In-Time ユーザー プロビジョニング機能では、認識されている IDP から SSO 要求が届いたとき、SAML アサーションの電子メールがユーザーの一覧に見つからない場合にのみ **読み取り専用** ユーザーが作成されます。 この自動プロビジョニング機能のために、OrgChart Now で「**全般**」というタイトルのアクセス グループを作成する必要があります。 次の手順でアクセス グループを作成してください。

    a. UI の右上隅にある **歯車** をクリックし、**[グループの管理]** オプションに進みます。

      ![OrgChart Now のグループ](./media/orgchartnow-tutorial/groups.png)   

    b. **[追加]** アイコンを選択し、グループに「**全般**」という名前を付け、**[OK]** をクリックします。 

      ![OrgChart Now の [追加]](./media/orgchartnow-tutorial/general.png)

    c. 全般 (読み取り) ユーザーがアクセスできるフォルダーを選択します。
    
      ![OrgChart Now のフォルダー](./media/orgchartnow-tutorial/folders.png)

    d. 管理者ユーザーのみが変更できるようにフォルダーを **ロックします**。 **[OK]** をクリックします。

      ![OrgChart Now のロック機能](./media/orgchartnow-tutorial/lock.png)

2. **管理者** ユーザーと **読み取り/書き込み** ユーザーを作成するには、SSO 経由で特権レベルへのアクセスを取得する目的でユーザーを手動作成する必要があります。 ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

    a. セキュリティ管理者として OrgChart Now にログインします。

    b. 右上隅にある **[設定]** をクリックし、**[ユーザーの管理]** に移動します。

      ![OrgChart Now の設定](./media/orgchartnow-tutorial/settings.png)

    c. **[追加]** をクリックし、次の手順を実行します。

      ![OrgChart Now の管理](./media/orgchartnow-tutorial/manage-users.png)

    1. **[ユーザー ID]** テキストボックスに **brittasimon\@contoso.com** のようなユーザー ID を入力します。

    1. **[Email Address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (**brittasimon\@contoso.com** など) を入力します。

    1. **[追加]** をクリックします。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる OrgChart Now のサインオン URL にリダイレクトされます。  

* OrgChart Now のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した OrgChart Now に自動的にサインインします。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで OrgChart Now タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した OrgChart Now に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

OrgChart Now を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
