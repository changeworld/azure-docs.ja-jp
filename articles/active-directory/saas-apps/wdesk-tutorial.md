---
title: チュートリアル:Azure Active Directory と Wdesk の統合 | Microsoft Docs
description: Azure Active Directory と Wdesk の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: jeedes
ms.openlocfilehash: d85d7ef37536b54ecfc1b65d19eafd1d499ca050
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104603272"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Wdesk の統合

このチュートリアルでは、Wdesk と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Wdesk を統合すると、次のことができます。

* Wdesk にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Wdesk に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Wdesk でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Wdesk では、**SP** Initiated SSO と **IDP** Initiated SSO がサポートされます。

## <a name="add-wdesk-from-the-gallery"></a>ギャラリーからの Wdesk の追加

Azure AD への Wdesk の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Wdesk を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Wdesk**」と入力します。
1. 結果のパネルから **[Wdesk]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-wdesk"></a>Wdesk の Azure AD SSO の構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Wdesk で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Wdesk 内の関連ユーザー間にリンク関係が確立されている必要があります。

Wdesk に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Wdesk の SSO の構成](#configure-wdesk-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Wdesk のテスト ユーザーの作成](#create-wdesk-test-user)** - Wdesk で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Wdesk** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<subdomain>.wdesk.com/auth/login/saml/<instancename>` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値は、SSO を構成するときに WDesk ポータルから得られます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Wdesk のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Wdesk へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Wdesk]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-wdesk-sso"></a>Wdesk の SSO の構成

1. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Wdesk にサインインします。

1. 左下の **[Admin]\(管理者\)** をクリックし、 **[Account Admin]\(アカウント管理者\)** を選びます。
 
    ![[Admin]\(管理者\) メニューから [Account Admin]\(アカウント管理者\) が選択されていることを示すスクリーンショット。](./media/wdesk-tutorial/account.png)

1. Wdesk 管理ツールで、 **[Security]\(セキュリティ\)** に移動した後、 **[SAML]**  >  **[SAML Settings]\(SAML の設定\)** の順に移動します。

    ![[SAML] タブから [SAML Settings]\(SAML の設定\) が選択されていることを示すスクリーンショット。](./media/wdesk-tutorial/settings.png)

1. **[SAML User ID Settings]\(SAML ユーザー ID 設定\)** で、 **[SAML User ID is Wdesk Username]\(SAML ユーザー ID は Wdesk ユーザー名\)** をオンにします。

    ![[SAML User I D Settings]\(SAML ユーザー I D 設定\) を示すスクリーンショット。ここで、[SAML User I D is W desk Username]\(SAML ユーザー I D は W desk ユーザー名\) を選択できます。](./media/wdesk-tutorial/wdesk-username.png)

4. **[General Settings]\(一般設定\)** で、 **[Enable SAML Single Sign On]\(SAML のシングル サインオンを有効にする\)** をオンにします。

    ![[Edit SAML Settings]\(SAML 設定の編集\) を示すスクリーンショット。ここで、[Enable SAML Single Sign-On]\(SAML のシングル サインオンを有効にする\) を選択できます。](./media/wdesk-tutorial/user-settings.png)

5. **[Service Provider Details]\(サービス プロバイダーの詳細\)** で、以下の手順を実行します。

    ![[Service Provider Details]\(サービス プロバイダーの詳細\) を示すスクリーンショット。ここで、説明されている値を入力できます。](./media/wdesk-tutorial/service-provider.png)

    1. **[Login URL]\(ログイン URL\)** をコピーし、Azure Portal の **[サインオン URL]** ボックスに貼り付けます。

    1. **[Metadata Url]\(メタデータ URL\)** をコピーし、Azure Portal の **[識別子]** ボックスに貼り付けます。

    1. **[Consumer url]\(コンシューマー URL\)** をコピーし、Azure Portal の **[応答 URL]** ボックスに貼り付けます。

    1. Azure Portal の **[保存]** をクリックして、変更を保存します。      

1. **[Configure IdP Settings]\(IdP の設定の構成\)** をクリックして、 **[Edit IdP Settings]\(IdP の設定の編集\)** ダイアログを開きます。 **[Choose File]\(ファイルの選択\)** をクリックし、Azure Portal から保存した **Metadata.xml** ファイルを選択して、アップロードします。
    
    ![[Edit I d P Settings]\(I d P の設定の編集\) を示すスクリーンショット。ここで、メタデータをアップロードできます。](./media/wdesk-tutorial/metadata.png)
  
1. **[変更を保存]** をクリックします。

    ![[Save changes]\(変更を保存\) ボタンを示すスクリーンショット。](./media/wdesk-tutorial/save.png)

### <a name="create-wdesk-test-user"></a>Wdesk テスト ユーザーの作成

Azure AD ユーザーが Wdesk にサインインできるようにするには、ユーザーを Wdesk にプロビジョニングする必要があります。 Wdesk では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Wdesk にサインインします。

2. **[Admin]\(管理者\)**  >  **[Account Admin]\(アカウント管理者\)** の順に移動します。

     ![[Admin]\(管理者\) メニューから [Account Admin]\(アカウント管理者\) が選択されていることを示すスクリーンショット。](./media/wdesk-tutorial/account.png)

3. **[People]\(ユーザー\)** の **[Members]\(メンバー\)** をクリックします。

4. **[Add Member]\(メンバーの追加\)** をクリックして、 **[Add Member]\(メンバーの追加\)** ダイアログ ボックスを開きます。 
   
    ![[Members]\(メンバー\) タブを示すスクリーンショット。ここで、[Add Member]\(メンバーの追加\) を選択できます。](./media/wdesk-tutorial/create-user-1.png)  

5. **[User]\(ユーザー\)** ボックスにユーザー名を入力し (例: b.simon@contoso.com)、 **[Continue]\(続行\)** をクリックします。

    ![[Add User]\(ユーザーの追加\) ダイアログ ボックスを示すスクリーンショット。ここで、ユーザーを入力できます。](./media/wdesk-tutorial/create-user-3.png)

6.  次のように、詳細を入力します。
  
    ![[Add User]\(ユーザーの追加\) ダイアログ ボックスを示すスクリーンショット。ここで、ユーザーの基本情報を追加できます。](./media/wdesk-tutorial/create-user-4.png)
 
    a. **[E-mail]\(電子メール\)** ボックスに、ユーザーのメール アドレスを入力します (例: b.simon@contoso.com)。

    b. **[名]** ボックスに、ユーザーの名を入力します (例: **B**)。

    c. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **Simon**)。

7. **[Save Member]\(メンバーの保存\)** ボタンをクリックします。  

    ![[Save Member]\(メンバーの保存\) ボタンを含む [Send welcome email]\(ウェルカム電子メールの送信\) を示すスクリーンショット。](./media/wdesk-tutorial/create-user-5.png)

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Wdesk のサインオン URL にリダイレクトされます。  

* Wdesk のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Wdesk に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Wdesk] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Wdesk に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Wdesk を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。
