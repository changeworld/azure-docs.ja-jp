---
title: 'チュートリアル: Azure Active Directory と Panorama9 の統合 | Microsoft Docs'
description: Azure Active Directory と Panorama9 の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2021
ms.author: jeedes
ms.openlocfilehash: d85e00c222254c108a6057a39d97a6db1b740c07
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306987"
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>チュートリアル: Azure Active Directory と Panorama9 の統合

このチュートリアルでは、Panorama9 と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Panorama9 を統合すると、次のことができます。

* Panorama9 にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Panorama9 に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Panorama9 でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Panorama9 では、**SP** Initiated SSO がサポートされます。

## <a name="add-panorama9-from-the-gallery"></a>ギャラリーから Panorama9 を追加する

Azure AD への Panorama9 の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Panorama9 を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Panorama9**」と入力します。
1. 結果のパネルから **[Panorama9]** を選択してアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-panorama9"></a>Panorama9 の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Panorama9 に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Panorama9 の関連ユーザーとの間にリンク関係を確立する必要があります。

Panorama9 に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Panorama9 SSO の構成](#configure-panorama9-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Panorama9 テスト ユーザーの作成](#create-panorama9-test-user)** - Panorama9 で B.Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Panorama9** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、URL として「`https://dashboard.panorama9.com/saml/access/3262`」と入力します。

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://www.panorama9.com/saml20/<TENANT_NAME>`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Panorama9 クライアント サポート チーム](https://support.panorama9.com/)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML 署名証明書]** セクションで **[編集]** ボタンをクリックして、 **[SAML 署名証明書]** ダイアログを開きます。

    ![SAML 署名証明書の編集](common/edit-certificate.png)

6. **[SAML 署名証明書]** セクションで **[Thumbprint]\(拇印\)** をコピーし、お使いのコンピューターに保存します。

    ![[Thumbprint]\(拇印\) の値をコピーする](common/copy-thumbprint.png)

7. **[Panorama9 のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Panorama9 へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Panorama9]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-panorama9-sso"></a>Panorama9 SSO の構成

1. 別の Web ブラウザーのウィンドウで、Panorama9 企業サイトに管理者としてサインインします。

2. 上部のツールバーで、 **[管理]** 、 **[拡張機能]** の順にクリックします。
   
    ![拡張機能](./media/panorama9-tutorial/toolbar.png "拡張機能")

3. **[拡張機能]** ダイアログで、 **[シングル サインオン]** をクリックします。
   
    ![シングル サインオン](./media/panorama9-tutorial/extension.png "[Single Sign-On]")

4. **[設定]** セクションで、次の手順に従います。
   
    ![[設定]](./media/panorama9-tutorial/configuration.png "設定")
   
    a. **[Identity provider URL]\(ID プロバイダー URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。
   
    b. **[Certificate Fingerprint]\(証明書のフィンガープリント\)** テキスト ボックスに、Azure Portal からコピーした証明書の **[拇印]** 値を貼り付けます。    
         
5. **[保存]** をクリックします。

### <a name="create-panorama9-test-user"></a>Panorama9 テスト ユーザーの作成

Azure AD ユーザーが Panorama9 にサインインできるようにするには、ユーザーを Panorama9 にプロビジョニングする必要があります。  

Panorama9 の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **Panorama9** 企業サイトに管理者としてサインインします。

2. 上部のメニューで、 **[管理]** 、 **[ユーザー]** の順にクリックします。
   
    ![[管理] タブと [ユーザー] タブが選択されている画面のスクリーンショット。](./media/panorama9-tutorial/user.png "ユーザー")

3. [ユーザー] セクションの **+** をクリックして新しいユーザーを追加します。

    ![ユーザー](./media/panorama9-tutorial/new-user.png "ユーザー")

4. [ユーザー データ] セクションに移動し、 **[電子メール]** テキストボックスにプロビジョニングする有効な Azure Active Directory ユーザーの電子メール アドレスを入力します。

5. [ユーザー] セクションに移動し、 **[保存]** をクリックします。
   
    > [!NOTE]
    > Azure Active Directory アカウント所有者が電子メールを受信し、リンクに従ってアカウントを確認すると、そのアカウントがアクティブになります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Panorama9 のサインオン URL にリダイレクトされます。 

* Panorama9 のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Panorama9] タイルをクリックすると、Panorama9 のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Panorama9 を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
