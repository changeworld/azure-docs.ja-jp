---
title: 'チュートリアル: Azure Active Directory と UserVoice の統合 | Microsoft Docs'
description: Azure Active Directory と UserVoice の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2021
ms.author: jeedes
ms.openlocfilehash: 4f975d710bb1e44b131e488fe2bd4b07f4d7dcfb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132303630"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>チュートリアル: Azure Active Directory と UserVoice の統合

このチュートリアルでは、UserVoice と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と UserVoice を統合すると、次のことが可能になります。

* UserVoice にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して UserVoice に自動的にサインインできるようにしきます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

UserVoice と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* UserVoice でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* UserVoice では、**SP** によって開始される SSO がサポートされます。

## <a name="add-uservoice-from-the-gallery"></a>ギャラリーからの UserVoice の追加

Azure AD への UserVoice の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に UserVoice を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**UserVoice**」と入力します。
1. 結果のパネルから **[UserVoice]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-uservoice"></a>UserVoice の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、UserVoice に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと UserVoice の関連ユーザーとの間にリンク関係を確立する必要があります。

UserVoice に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[UserVoice SSO の構成](#configure-uservoice-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[UserVoice のテスト ユーザーの作成](#create-uservoice-test-user)** - UserVoice で B.Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **UserVoice** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<TENANT_NAME>.UserVoice.com`

    b. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<TENANT_NAME>.UserVoice.com`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際の識別子とサインオン URL で更新してください。 これらの値を取得するには、[UserVoice クライアント サポート チーム](https://www.uservoice.com/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML 署名証明書]** セクションで **[編集]** ボタンをクリックして、 **[SAML 署名証明書]** ダイアログを開きます。

    ![SAML 署名証明書の編集](common/edit-certificate.png)

6. **[SAML 署名証明書]** セクションで **[Thumbprint]\(拇印\)** をコピーし、お使いのコンピューターに保存します。

    ![[Thumbprint]\(拇印\) の値をコピーする](common/copy-thumbprint.png)

7. **[UserVoice のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に UserVoice へのアクセスを許可して、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[UserVoice]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-uservoice-sso"></a>UserVoice SSO の構成

1. 別の Web ブラウザーのウィンドウで、UserVoice 企業サイトに管理者としてサインインします。

2. 上部のツール バーの **[設定]** をクリックし、メニューから **[Web ポータル]** を選択します。
   
    ![アプリ側の [Settings]\(設定\) セクション](./media/uservoice-tutorial/portal.png "設定")

3. **[Web ポータル]** タブの **[ユーザー認証]** セクションで、 **[編集]** をクリックして **[ユーザー認証の編集]** ダイアログ ページを開きます。
   
    ![[Web ポータル] タブ](./media/uservoice-tutorial/user.png "Web ポータル")

4. **[ユーザー認証の編集]** ダイアログ ページで、次の手順に従います。
   
    ![[ユーザー認証の編集]](./media/uservoice-tutorial/authentication.png "[ユーザー認証の編集]")
   
    a. **[シングル サインオン]** をクリックします。
 
    b. Azure portal からコピーした **[ログイン URL]** の値を **[SSO Remote Sign-In]\(SSO リモート サインイン\)** ボックスに貼り付けます。

    c. Azure portal からコピーした **[ログアウト URL]** の値を **[SSO Remote Sign-Out]\(SSO リモート サインアウト\) ボックス** に貼り付けます。
 
    d. Azure portal からコピーした **[拇印]** の値を **[Current certificate SHA1 fingerprint]\(現在の証明書 SHA1 のフィンガープリント\)** ボックスに貼り付けます。
    
    e. **[認証設定の保存]** をクリックします。

### <a name="create-uservoice-test-user"></a>UserVoice のテスト ユーザーの作成

Azure AD ユーザーが UserVoice にサインインできるようにするには、そのユーザーを UserVoice にプロビジョニングする必要があります。 UserVoice の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1. **UserVoice** テナントにサインインします。

2. **[設定]** に移動します。
   
    ![[設定]](./media/uservoice-tutorial/account.png "設定")

3. **[全般]** をクリックします。

4. **[エージェントとアクセス許可]** をクリックします。
   
    ![[エージェントとアクセス許可]](./media/uservoice-tutorial/general.png "[エージェントとアクセス許可]")

5. **[管理者の追加]** をクリックします。
   
    ![[管理者の追加]](./media/uservoice-tutorial/admin.png "[管理者の追加]")

6. **[管理者の招待]** ダイアログで、次の手順を実行します。
   
    ![[管理者の招待]](./media/uservoice-tutorial/invite.png "[管理者の招待]")
   
    a. [電子メール] ボックスに、プロビジョニングするアカウントの電子メール アドレスを入力し、 **[追加]** をクリックします。
   
    b. **[招待]** をクリックします。

> [!NOTE]
> 他の UserVoice ユーザー アカウント作成ツールや、UserVoice から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる UserVoice のサインオン URL にリダイレクトされます。 

* UserVoice のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [UserVoice] タイルをクリックすると、UserVoice サインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

UserVoice を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
