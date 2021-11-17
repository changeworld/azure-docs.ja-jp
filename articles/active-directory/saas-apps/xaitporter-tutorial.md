---
title: 'チュートリアル: Azure Active Directory と XaitPorter の統合 | Microsoft Docs'
description: Azure Active Directory と XaitPorter の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2021
ms.author: jeedes
ms.openlocfilehash: 71ea5cbb1e2c0c5db8933696f5d1797fabcbda62
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332556"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>チュートリアル: Azure Active Directory と XaitPorter の統合

このチュートリアルでは、XaitPorter と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と XaitPorter を統合すると、次のことが可能になります。

* XaitPorter にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して XaitPorter に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

XaitPorter と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* XaitPorter でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* XaitPorter では、**SP** Initiated SSO がサポートされます。

## <a name="add-xaitporter-from-the-gallery"></a>ギャラリーからの XaitPorter の追加

Azure AD への XaitPorter の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に XaitPorter を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**XaitPorter**」と入力します。
1. 結果のパネルから **[XaitPorter]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-xaitporter"></a>XaitPorter の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、XaitPorter に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと XaitPorter の関連ユーザーとの間にリンク関係を確立する必要があります。

XaitPorter に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[XaitPorter SSO の構成](#configure-xaitporter-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[XaitPorter のテスト ユーザーの作成](#create-xaitporter-test-user)** - XaitPorter で B.Simon に対応するユーザーを作成し、Azure AD のユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **XaitPorter** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.xaitporter.com`

    b. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.xaitporter.com/saml/login`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際の識別子とサインオン URL で更新してください。 これらの値を取得するには、[XaitPorter クライアント サポート チーム](https://www.xait.com/support/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

6. **IP アドレス** または **アプリのフェデレーション メタデータ URL** を [SmartRecruiters サポート チーム](https://www.smartrecruiters.com/about-us/contact-us/)に提供します。これにより、XaitPorter では、承認済みリストを構成して、XaitPorter インスタンスから IP アドレスに確実に到達できるようにすることができます。 

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

このセクションでは、B. Simon に XaitPorter へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[XaitPorter]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-xaitporter-sso"></a>XaitPorter SSO の構成

1. XaitPorter 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **マイ アプリによるセキュリティで保護されたサインイン拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Setup XaitPorter]** (XaitPorter のセットアップ) をクリックすると、XaitPorter アプリケーションに移動します。 そこから、管理者資格情報を提供して XaitPorter にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. XaitPorter を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として XaitPorter 企業サイトにサインインして、次の手順のようにします。

4. **[管理]** をクリックします。

    ![XaitPorter サイトで選択されている [Admin]\(管理者\) を示すスクリーンショット。](./media/xaitporter-tutorial/admin.png)

5. **[システム設定]** ドロップダウン リストから、 **[シングル サインオンの管理]** を選択します。

    ![[System Setup]\(システム設定\) で選択されている [Manage Single Sign-On]\(シングル サインオンの管理\) を示すスクリーンショット。](./media/xaitporter-tutorial/user.png)

6. **[シングル サインオンの管理]** セクションで、次の手順に従います。

    ![これらの手順を実行できる [MANAGE SINGLE SIGN-ON]\(シングル サインオンの管理\) セクションを示すスクリーンショット。](./media/xaitporter-tutorial/authentication.png)

    a. **[シングル サインオンを有効にする]** を選択します。

    b. **[ID プロバイダーの設定]** ボックスで、Azure Portal からコピーした **アプリのフェデレーション メタデータ URL** を貼り付けて、 **[フェッチ]** をクリックします。

    c. **[Enable Autocreation of Users]\(ユーザーの自動作成を有効にする\)** を選択します。

    d. **[OK]** をクリックします。

### <a name="create-xaitporter-test-user"></a>XaitPorter のテスト ユーザーの作成

このセクションでは、XaitPorter で Britta Simon というユーザーを作成します。 XaitPorter プラットフォームでユーザーを追加するには、[XaitPorter クライアント サポート チーム](https://www.xait.com/support/)に問い合わせてください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる XaitPorter のサインオン URL にリダイレクトされます。 

* XaitPorter のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [XaitPorter] タイルをクリックすると、XaitPorter のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

XaitPorter を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
