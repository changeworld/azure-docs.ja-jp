---
title: チュートリアル:Azure Active Directory と ClickUp Productivity の統合 | Microsoft Docs
description: Azure Active Directory と ClickUp Productivity Platform の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2021
ms.author: jeedes
ms.openlocfilehash: 29bc02466825aa2ea2c1a9ece2826b1262293392
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285209"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>チュートリアル:Azure Active Directory と ClickUp Productivity Platform の統合

このチュートリアルでは、ClickUp Productivity Platform と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と ClickUp Productivity Platform を統合すると、次のことができます。

* ClickUp Productivity Platform にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントで ClickUp Productivity Platform に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* ClickUp Productivity Platform でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* ClickUp Productivity Platform では、**SP** Initiated SSO がサポートされます。

## <a name="add-clickup-productivity-platform-from-the-gallery"></a>ギャラリーからの ClickUp Productivity Platform の追加

Azure AD への ClickUp Productivity Platform の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に ClickUp Productivity Platform を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**ClickUp Productivity Platform**」と入力します。
1. 結果パネルで **[ClickUp Productivity Platform]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-clickup-productivity-platform"></a>ClickUp Productivity Platform の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、ClickUp Productivity Platform に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと ClickUp Productivity Platform の関連ユーザーとの間にリンク関係を確立する必要があります。

ClickUp Productivity Platform で Azure AD SSO を構成してテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[ClickUp Productivity Platform の SSO の構成](#configure-clickup-productivity-platform-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[ClickUp Productivity Platform のテスト ユーザーの作成](#create-clickup-productivity-platform-test-user)** - ClickUp Productivity Platform で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **ClickUp Productivity Platform** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、URL として「`https://app.clickup.com/login/sso`」と入力します。

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > この識別子の値は実際のものではありません。 この値は、実際の識別子に置き換えてください。これについては後で説明します。

5. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

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

このセクションでは、B.Simon に ClickUp Productivity Platform へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[ClickUp Productivity Platform]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-clickup-productivity-platform-sso"></a>ClickUp Productivity Platform の SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として ClickUp Productivity Platform テナントにサインオンします。

2. **[User profile]\(ユーザー プロファイル\)** をクリックし、 **[Settings]\(設定\)** を選択します。

    ![[スクリーンショットは、Settings]\(設定\) アイコンが選択された ClickUp Productivity テナントを示します。](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![スクリーンショットは、[Settings]\(設定\) を示します。](./media/clickup-productivity-platform-tutorial/configure-1.png)

3. [Single Sign-On (SSO) Provider]\(シングル サインオン (SSO) プロバイダー\) で **[Microsoft]** を選択します。

    ![スクリーンショットは、[Microsoft] が選択されている [Authentication]\(認証\) ペインを示します。](./media/clickup-productivity-platform-tutorial/configure-2.png)

4. **[Configure Microsoft Single Sign On]\(Microsoft のシングル サインオンの構成\)** ページで、次の手順を実行します。

    ![スクリーンショットは、[Configure Microsoft Single Sign On]\(Microsoft のシングル サインオンの構成\) ページを示します。ここで、[Entity I D]\(エンティティ I D\) をコピーし、[Azure Federation Metadata U R L]\(Azure フェデレーション メタデータ U R L\) を保存できます。](./media/clickup-productivity-platform-tutorial/configure-3.png)

    a. **[Copy]\(コピー\)** をクリックして [Entity ID]\(エンティティ ID\) の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子 (エンティティ ID)]** ボックスに貼り付けます。

    b. **[Azure Federation Metadata Url]\(Azure のフェデレーション メタデータ URL\)** ボックスに、Azure portal からコピーした [アプリのフェデレーション メタデータ URL] の値を貼り付けて、 **[Save]\(保存\)** をクリックします。

5. この設定を完了するには、 **[Authenticate With Microsoft to complete setup]\(Microsoft で認証してセットアップを完了する\)** をクリックして、Microsoft アカウントで認証します。

    ![スクリーンショットは、[Authenticate With Microsoft to complete setup]\(Microsoft で認証してセットアップを完了する\) ボタンを示します。](./media/clickup-productivity-platform-tutorial/configure-4.png)


### <a name="create-clickup-productivity-platform-test-user"></a>ClickUp Productivity Platform のテスト ユーザーの作成

1. 別の Web ブラウザーのウィンドウで、管理者として ClickUp Productivity Platform テナントにサインオンします。

2. **[User profile]\(ユーザー プロファイル\)** をクリックし、 **[People]\(ユーザー\)** を選択します。

    ![スクリーンショットは、ClickUp Productivity テナントを示します。](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![スクリーンショットは、[People]\(ユーザー\) リンクが選択されていることを示します。](./media/clickup-productivity-platform-tutorial/user-1.png)

3. テキスト ボックスにユーザーのメール アドレスを入力して、 **[Invite]\(招待\)** をクリックします。

    ![スクリーンショットは、[Team Users Settings]\(チーム ユーザー設定\) を示します。これを設定するとメールでユーザーを招待できます。](./media/clickup-productivity-platform-tutorial/user-2.png)

    > [!NOTE]
    > ユーザーは通知を受け取ったら、招待を承諾してアカウントをアクティブにする必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる ClickUp Productivity Platform のサインオン URL にリダイレクトされます。 

* ClickUp Productivity Platform のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [ClickUp Productivity Platform] タイルをクリックすると、ClickUp Productivity Platform のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

ClickUp Productivity Platform を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。