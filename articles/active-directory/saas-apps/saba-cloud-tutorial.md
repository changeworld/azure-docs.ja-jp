---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Saba Cloud の統合 | Microsoft Docs'
description: Azure Active Directory と Saba Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2021
ms.author: jeedes
ms.openlocfilehash: 0b993380287edd4528a26dd2d9925a2b66c54fd3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299745"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-cloud"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Saba Cloud の統合

このチュートリアルでは、Saba Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。 Saba Cloud と Azure AD を統合すると、次のことができます。

* Saba Cloud にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Saba Cloud に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Saba Cloud でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Saba Cloud では、**SP および IDP** Initiated SSO がサポートされます。
* Saba Cloud では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。
* Saba Cloud Mobile アプリケーションを Azure AD と共に構成して SSO を有効にできるようになりました。 このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

## <a name="adding-saba-cloud-from-the-gallery"></a>ギャラリーからの Saba Cloud の追加

Azure AD への Saba Cloud の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Saba Cloud を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Saba Cloud**」と入力します。
1. 結果のパネルから **[Saba Cloud]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-saba-cloud"></a>Saba Cloud の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Saba Cloud に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Saba Cloud の関連ユーザーとの間にリンク関係を確立する必要があります。

Saba Cloud に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Saba Cloud の SSO の構成](#configure-saba-cloud-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Saba Cloud のテスト ユーザーの作成](#create-saba-cloud-test-user)** - Saba Cloud で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。
1. **[Saba Cloud (モバイル) の SSO のテスト](#test-sso-for-saba-cloud-mobile)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Saba Cloud** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`<CUSTOMER_NAME>_sp` というパターンを使用して URL を入力します (この値は、「Saba Cloud の SSO の構成」セクションの手順 6. で取得しますが、通常は `<CUSTOMER_NAME>_sp` の形式になっています)。

    b. **[応答 URL]** ボックスに、`https://<CUSTOMER_NAME>.sabacloud.com/Saba/saml/SSO/alias/<ENTITY_ID>` というパターンを使用して URL を入力します。ENTITY_ID には、前の手順で使用した値 (通常は `<CUSTOMER_NAME>_sp`) を指定してください。
    
    > [!NOTE]
    > 応答 URL の指定に誤りがある場合、 **[エンタープライズ アプリケーション]** セクションではなく、Azure AD の **[アプリの登録]** セクションで調整する必要があります。 **[基本的な SAML 構成]** セクションに変更を加えても、応答 URL が更新されるとは限りません。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    a. **[サインオン URL]** ボックスに、`https://<CUSTOMER_NAME>.sabacloud.com` という形式で URL を入力します。

    b. `IDP_INIT---SAML_SSO_SITE=<SITE_ID> ` というパターンを使用して URL を **[リレー状態]** ボックスに入力します。または、SAML がマイクロサイト用に構成されている場合は、`IDP_INIT---SAML_SSO_SITE=<SITE_ID>---SAML_SSO_MICRO_SITE=<MicroSiteId>` というパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL、リレー状態でこれらの値を更新します。 これらの値を取得するには、[Saba Cloud クライアント サポート チーム](mailto:support@saba.com)にご連絡ください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。
    > 
    > RelayState の構成の詳細については、「[マイクロサイトの IdP Initiated SSO と SP Initiated SSO](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-idp-and-sp-initiated-sso-for-a-microsite.html)」を参照してください。

1. **[User Attributes & Claims]\(ユーザー属性とクレーム\)** セクションで、一意のユーザー ID を調整します。Saba ユーザーのプライマリ ユーザー名として使用する予定の ID を使用してください。

   この手順が必要になるのは、ユーザー名とパスワードから SSO への変換を試みる場合のみです。 まだユーザーがいない新しい Saba Cloud デプロイである場合、この手順はスキップしてかまいません。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Saba Cloud のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Saba Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Saba Cloud]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-saba-cloud-sso"></a>Saba Cloud の SSO の構成

1. Saba Cloud 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Saba Cloud のセットアップ]** をクリックすると、Saba Cloud アプリケーションに移動します。 そこから、管理者の資格情報を入力して Saba Cloud にサインインします。 ブラウザー拡張機能によって、アプリケーションが自動的に構成され、手順 3. から 9. が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. Saba Cloud を手動でセットアップする場合は、別の Web ブラウザー ウィンドウで、Saba Cloud 企業サイトに管理者としてサインインします。

1. **メニュー** アイコンをクリックし、 **[Admin]\(管理\)** をクリックして、 **[System Admin]\(システム管理\)** タブを選択します。

    ![システム管理のスクリーンショット](./media/saba-cloud-tutorial/system.png)

1. **[Configure System]\(システムの構成\)** の **[SAML SSO Setup]\(SAML SSO のセットアップ\)** を選択し、 **[SETUP SAML SSO]\(SAML SSO のセットアップ\)** ボタンをクリックします。 

    ![構成のスクリーンショット](./media/saba-cloud-tutorial/configure.png)

1. ポップアップ ウィンドウで、ドロップダウンから **[Microsite]\(マイクロサイト\)** を選択し、 **[ADD AND CONFIGURE]\(追加と構成\)** をクリックします。

    ![サイトまたはマイクロサイトの追加のスクリーンショット](./media/saba-cloud-tutorial/microsite.png)

1. **[Configure IDP]\(IDP の構成\)** セクションで **[BROWSE]\(参照\)** をクリックし、Azure portal からダウンロードした **フェデレーション メタデータ XML** ファイルをアップロードします。 **[Site Specific IDP]\(サイト個別の IDP\)** チェック ボックスをオンにし、 **[IMPORT]\(インポート\)** をクリックします。

    ![証明書のインポートのスクリーンショット](./media/saba-cloud-tutorial/certificate.png) 

1. **[Configure SP]\(SP の構成\)** セクションで **[Entity Alias]\(エンティティのエイリアス\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子 (エンティティ ID)]** ボックスにその値を貼り付けます。 **[GENERATE]\(生成\)** をクリックします。

    ![[Configure SP]\(SP の構成\) のスクリーンショット](./media/saba-cloud-tutorial/generate-metadata.png) 

1. **[Configure Properties]\(プロパティの構成\)** セクションで、事前設定されたフィールドを確認し、 **[SAVE]\(保存\)** をクリックします。 

    ![[Configure Properties]\(プロパティの構成\) のスクリーンショット](./media/saba-cloud-tutorial/configure-properties.png) 
    
    **[Max Authentication Age (in seconds)]\(最大認証期間 (秒)\)** は、Azure AD がログインに許容する既定の最大ローリング期間に合わせて、**7776000** (90 日) に設定しなければならない場合があります。 そのようにしないと、"`(109) Login failed. Please contact system administrator.` ((109) ログインに失敗しました。システム管理者に問い合わせてください)" というエラーが発生する可能性があります。

### <a name="create-saba-cloud-test-user"></a>Saba Cloud のテスト ユーザーを作成する

このセクションでは、Britta Simon というユーザーを Saba Cloud に作成します。 Saba Cloud では、Just-In-Time ユーザー プロビジョニングがサポートされています。これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Saba Cloud にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

> [!NOTE]
> Saba Cloud で SAML Just-In-Time ユーザー プロビジョニングを有効にする方法については、[こちら](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-user-provisioning-with-saml.html)のドキュメントを参照してください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Saba Cloud のサインオン URL にリダイレクトされます。  

* Saba Cloud のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Saba Cloud に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Saba Cloud] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Saba Cloud に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

> [!NOTE]
> サインオン URL が Azure AD で設定されていない場合、アプリケーションは IDP Initiated モードとして扱われます。サインオン URL が設定されている場合は、ユーザーが常に Service Provider Initiated フローのために Azure AD によって Saba Cloud アプリケーションにリダイレクトされます。

## <a name="test-sso-for-saba-cloud-mobile"></a>Saba Cloud (モバイル) の SSO のテスト

1. Saba Cloud Mobile アプリケーションを開き、テキスト ボックスに **サイト名** を入力して **Enter** キーを押します。

    ![[Site Name]\(サイト名\) のスクリーンショット。](./media/saba-cloud-tutorial/site-name.png)

1. **メール アドレス** を入力し、 **[Next]\(次へ\)** をクリックします。

    ![メール アドレスのスクリーンショット](./media/saba-cloud-tutorial/email-address.png)

1. サインインに成功すると、アプリケーション ページが表示されます。

    ![サインインの成功を示すスクリーンショット。](./media/saba-cloud-tutorial/dashboard.png)

## <a name="next-steps"></a>次のステップ

Saba Cloud を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-any-app)。
