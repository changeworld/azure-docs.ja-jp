---
title: 'チュートリアル: Azure Active Directory と Infinite Campus の統合 | Microsoft Docs'
description: Azure Active Directory と Infinite Campus の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/17/2021
ms.author: jeedes
ms.openlocfilehash: 817bc6c7b425af9cc02bc339967cb29b1c4b81fd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285236"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>チュートリアル: Azure Active Directory と Infinite Campus の統合

このチュートリアルでは、Infinite Campus と Azure Active Directory (Azure AD) を統合する方法について説明します。 Infinite Campus と Azure AD を統合すると、次のことができます。

* Infinite Campus にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Infinite Campus に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Infinite Campus と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Infinite Campus シングル サインオンが有効なサブスクリプション。
* 構成を実行するには、少なくとも Azure Active Directory 管理者であり、「Student Information System (SIS)」のキャンパス製品セキュリティ ロールを持っている必要があります。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Infinite Campus では、**SP** によって開始される SSO がサポートされます。

## <a name="add-infinite-campus-from-the-gallery"></a>ギャラリーからの Infinite Campus の追加

Azure AD への Infinite Campus の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから Infinite Campus を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Infinite Campus**」と入力します。
1. 結果パネルで **[Infinite Campus]** を選択して、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-infinite-campus"></a>Infinite Campus の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Infinite Campus に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Infinite Campus の関連ユーザーとの間にリンク関係を確立する必要があります。

Infinite Campus に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Infinite Campus SSO の構成](#configure-infinite-campus-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Infinite Campus テスト ユーザーの作成](#create-infinite-campus-test-user)** - Infinite Campus で、B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Infinite Campus** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. [基本的な SAML 構成] セクションで、次の手順を実行します (ドメインはホスティング モデルによって異なりますが、**FULLY-QUALIFIED-DOMAIN** の値は Infinite Campus のインストールと一致する必要があることに注意してください)。

    a. **[サインオン URL]** ボックスに、`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>` のパターンを使用して URL を入力します。

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

このセクションでは、B.Simon に Infinite Campus へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Infinite Campus]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-infinite-campus-sso"></a>Infinite Campus SSO の構成

1. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Infinite Campus にサインインします。

2. メニューの左側にある **[System Administration]\(システム管理\)** をクリックします。

    ![管理](./media/infinitecampus-tutorial/admin.png)

3. **[User Security]\(ユーザー セキュリティ\)**  >  **[SAML Management]\(SAML 管理\)**  >  **[SSO Service Provider Configuration]\(SSO サービス プロバイダーの構成\)** に移動します。

    ![SAML](./media/infinitecampus-tutorial/security.png)

4. **[SSO Service Provider Configuration]\(SSO サービス プロバイダーの構成\)** ページで、次の手順に従います。

    ![SSO](./media/infinitecampus-tutorial/configuration.png)

    a. **[Enable SAML Single Sign On]\(SAML シングル サインオンを有効にする\)** チェック ボックスをオンにします。

    b. **[Optional Attribute Name]\(省略可能な属性名\)** を編集して **名前** を含めます。

    c. **[Select an option to retrieve Identity Provider (IDP) server data]\(ID プロバイダー (IDP) サーバー データ取得のオプションを選択する\)** セクションで、**[Metadata URL]\(メタデータ URL\)** を選択し、Azure portal からコピーした **アプリのフェデレーション メタデータ URL** をボックスに貼り付けて、**[Sync]\(同期\)** をクリックします。

    d. **[Sync]\(同期\)** をクリックすると、**[SSO Service Provider Configuration]\(SSO サービス プロバイダーの構成\)** ページに値が自動的に設定されます。 これらの値は上記のステップ 4 で示される値と一致することを確認できます。

    e. **[保存]** をクリックします。

### <a name="create-infinite-campus-test-user"></a>Infinite Campus テスト ユーザーの作成

Infinite Campus はデモグラフィック中心のアーキテクチャです。 Infinite Campus プラットフォームでのユーザーの追加については、[Infinite Campus サポート チーム](mailto:sales@infinitecampus.com)に問い合わせてください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Infinite Campus のサインオン URL にリダイレクトされます。 

* Infinite Campus のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Infinite Campus] タイルをクリックすると、Infinite Campus のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Infinite Campus を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
