---
title: 'チュートリアル: Azure AD SSO と Dealpath の統合'
description: Azure Active Directory と Dealpath の間のシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2021
ms.author: jeedes
ms.openlocfilehash: 8631a7f8d3dc3b18407c228ed565b62d7ed96367
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649985"
---
# <a name="tutorial-azure-ad-sso-integration-with-dealpath"></a>チュートリアル: Azure AD SSO と Dealpath の統合

このチュートリアルでは、Dealpath と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Dealpath を統合すると、次のことが可能になります。

* Dealpath にアクセスするユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Dealpath に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、<bpt id="p1">[</bpt>無料アカウント<ept id="p1">](https://azure.microsoft.com/free/)</ept>を取得できます。
* Dealpath でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Dealpath では、**SP** Initiated SSO がサポートされます。

## <a name="add-dealpath-from-the-gallery"></a>ギャラリーから Dealpath を追加する

Azure AD への Dealpath の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Dealpath を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで <bpt id="p1">**</bpt>[Azure Active Directory]<ept id="p1">**</ept> サービスを選択します。
1. <bpt id="p1">**</bpt>[エンタープライズ アプリケーション]<ept id="p1">**</ept> に移動し、 <bpt id="p2">**</bpt>[すべてのアプリケーション]<ept id="p2">**</ept> を選択します。
1. 新しいアプリケーションを追加するには、 <bpt id="p1">**</bpt>[新しいアプリケーション]<ept id="p1">**</ept> を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Dealpath**」と入力します。
1. 結果ウィンドウで **[Dealpath]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-dealpath"></a>Dealpath の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Dealpath に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Dealpath の関連ユーザーとの間にリンク関係を確立する必要があります。

Dealpath に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. <bpt id="p1">**</bpt><bpt id="p2">[</bpt>Azure AD SSO の構成<ept id="p2">](#configure-azure-ad-sso)</ept><ept id="p1">**</ept> - ユーザーがこの機能を使用できるようにします。
    1. <bpt id="p1">**</bpt><bpt id="p2">[</bpt>Azure AD のテスト ユーザーの作成<ept id="p2">](#create-an-azure-ad-test-user)</ept><ept id="p1">**</ept> - B.Simon で Azure AD のシングル サインオンをテストします。
    1. <bpt id="p1">**</bpt><bpt id="p2">[</bpt>Azure AD テスト ユーザーの割り当て<ept id="p2">](#assign-the-azure-ad-test-user)</ept><ept id="p1">**</ept> - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Dealpath SSO の構成](#configure-dealpath-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Dealpath テスト ユーザーの作成](#create-dealpath-test-user)** - Dealpath で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. <bpt id="p1">**</bpt><bpt id="p2">[</bpt>SSO のテスト<ept id="p2">](#test-sso)</ept><ept id="p1">**</ept> - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Dealpath** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. <bpt id="p1">**</bpt>[シングル サインオン方式の選択]<ept id="p1">**</ept> ページで、 <bpt id="p2">**</bpt>[SAML]<ept id="p2">**</ept> を選択します。
1. <bpt id="p1">**</bpt>[SAML によるシングル サインオンのセットアップ]<ept id="p1">**</ept> ページで、 <bpt id="p2">**</bpt>[基本的な SAML 構成]<ept id="p2">**</ept> の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. <bpt id="p1">**</bpt>[基本的な SAML 構成]<ept id="p1">**</ept> セクションで、次の手順を実行します。

    a. <bpt id="p1">**</bpt>[サインオン URL]<ept id="p1">**</ept> ボックスに、URL として「<ph id="ph1">`https://app.dealpath.com/account/login`</ph>」と入力します。

    b. <bpt id="p1">**</bpt>[識別子 (エンティティ ID)]<ept id="p1">**</ept> ボックスに、次のパターンを使用して URL を入力します。<ph id="ph1">`https://api.dealpath.com/saml/metadata/<ID>`</ph>

    > [!NOTE]
    > この識別子の値は実際のものではありません。 この値を実際の識別子で更新してください。 これらの値を取得するには、<bpt id="p1">[</bpt>Dealpath クライアント サポート チーム<ept id="p1">](mailto:kenter@dealpath.com)</ept>に問い合わせてください。 Azure portal の <bpt id="p1">**</bpt>[基本的な SAML 構成]<ept id="p1">**</ept> セクションに示されているパターンを参照することもできます。

4. <bpt id="p1">**</bpt>[SAML でシングル サインオンをセットアップします]<ept id="p1">**</ept> ページの <bpt id="p2">**</bpt>[SAML 署名証明書]<ept id="p2">**</ept> セクションで <bpt id="p3">**</bpt>[ダウンロード]<ept id="p3">**</ept> をクリックして、要件に適した特定のオプションの<bpt id="p4">**</bpt>証明書 (Base64)<ept id="p4">**</ept> をダウンロードし、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. <bpt id="p1">**</bpt>[Dealpath のセットアップ]<ept id="p1">**</ept> セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 <bpt id="p1">**</bpt>[Azure Active Directory]<ept id="p1">**</ept> 、 <bpt id="p2">**</bpt>[ユーザー]<ept id="p2">**</ept> 、 <bpt id="p3">**</bpt>[すべてのユーザー]<ept id="p3">**</ept> の順に選択します。
1. 画面の上部にある <bpt id="p1">**</bpt>[新しいユーザー]<ept id="p1">**</ept> を選択します。
1. <bpt id="p1">**</bpt>[ユーザー]<ept id="p1">**</ept> プロパティで、以下の手順を実行します。
   1. <bpt id="p1">**</bpt>[名前]<ept id="p1">**</ept> フィールドに「<ph id="ph1">`B.Simon`</ph>」と入力します。  
   1. <bpt id="p1">**</bpt>[ユーザー名]<ept id="p1">**</ept> フィールドに「<ph id="ph1">username@companydomain.extension</ph>」と入力します。 たとえば、「 <ph id="ph1">`B.Simon@contoso.com`</ph> 」のように入力します。
   1. <bpt id="p1">**</bpt>[パスワードを表示]<ept id="p1">**</ept> チェック ボックスをオンにし、 <bpt id="p2">**</bpt>[パスワード]<ept id="p2">**</ept> ボックスに表示された値を書き留めます。
   1. <bpt id="p1">**</bpt>Create<ept id="p1">**</ept> をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Dealpath へのアクセスを許可して、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で <bpt id="p1">**</bpt>[エンタープライズ アプリケーション]<ept id="p1">**</ept> を選択し、 <bpt id="p2">**</bpt>[すべてのアプリケーション]<ept id="p2">**</ept> を選択します。
1. アプリケーションの一覧で <bpt id="p1">**</bpt>[Dealpath]<ept id="p1">**</ept> を選択します。
1. アプリの概要ページで、 <bpt id="p1">**</bpt>[管理]<ept id="p1">**</ept> セクションを見つけて、 <bpt id="p2">**</bpt>[ユーザーとグループ]<ept id="p2">**</ept> を選択します。
1. <bpt id="p1">**</bpt>[ユーザーの追加]<ept id="p1">**</ept> を選択し、 <bpt id="p3">**</bpt>[割り当ての追加]<ept id="p3">**</ept> ダイアログで <bpt id="p2">**</bpt>[ユーザーとグループ]<ept id="p2">**</ept> を選択します。
1. <bpt id="p1">**</bpt>[ユーザーとグループ]<ept id="p1">**</ept> ダイアログの [ユーザー] の一覧から <bpt id="p2">**</bpt>[B.Simon]<ept id="p2">**</ept> を選択し、画面の下部にある <bpt id="p3">**</bpt>[選択]<ept id="p3">**</ept> ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 <bpt id="p1">**</bpt>[ロールの選択]<ept id="p1">**</ept> ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. <bpt id="p1">**</bpt>[割り当ての追加]<ept id="p1">**</ept> ダイアログで、 <bpt id="p2">**</bpt>[割り当て]<ept id="p2">**</ept> をクリックします。

## <a name="configure-dealpath-sso"></a>Dealpath SSO の構成

1. 別の Web ブラウザー ウィンドウで、Dealpath に管理者としてサインインします。

2. 右上で <bpt id="p1">**</bpt>[管理ツール]<ept id="p1">**</ept> をクリックし、 <bpt id="p2">**</bpt>[統合]<ept id="p2">**</ept> に移動して、 <bpt id="p3">**</bpt>[SAML 2.0 Authentication]\(SAML 2.0 認証\)<ept id="p3">**</ept> セクションで <bpt id="p4">**</bpt>[設定の更新]<ept id="p4">**</ept> をクリックします。

    ![[S A M L 2.0 Authentication]\(S A M L 2.0 認証\) セクションと [設定の更新] が選択された [管理ツール] - [統合] ページを示すスクリーンショット。](./media/dealpath-tutorial/admin.png)

3. <bpt id="p1">**</bpt>[Set up SAML 2.0 authentication]\(SAML 2.0 認証の設定\)<ept id="p1">**</ept> ページで、次の手順を実行します。

    ![Dealpath の構成を示すスクリーンショット。](./media/dealpath-tutorial/configuration.png) 

    a. <bpt id="p1">**</bpt>[SAML SSO URL]<ept id="p1">**</ept> ボックスに、Azure portal からコピーした<bpt id="p2">**</bpt>ログイン URL<ept id="p2">**</ept> の値を貼り付けます。

    b. <bpt id="p1">**</bpt>[ID プロバイダーの発行者]<ept id="p1">**</ept> ボックスに、Azure portal からコピーした <bpt id="p2">**</bpt>Azure AD 識別子<ept id="p2">**</ept>の値を貼り付けます。

    c. ダウンロードした<bpt id="p2">**</bpt>証明書 (Base64)<ept id="p2">**</ept> をメモ帳で開いて内容をコピーし、 <bpt id="p1">**</bpt>[公開証明書]<ept id="p1">**</ept> ボックスに貼り付けます。

    d. <bpt id="p1">**</bpt>[設定の更新]<ept id="p1">**</ept> をクリックします。

### <a name="create-dealpath-test-user"></a>Dealpath テスト ユーザーの作成

このセクションでは、Dealpath で Britta Simon というユーザーを作成します。 <bpt id="p1">[</bpt>Dealpath クライアント サポート チーム<ept id="p1">](mailto:kenter@dealpath.com)</ept>と連携し、Dealpath プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で <bpt id="p1">**</bpt>[このアプリケーションをテストします]<ept id="p1">**</ept> をクリックします。 これにより、ログイン フローを開始できる Dealpath のサインオン URL にリダイレクトされます。 

* Dealpath のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリの [Dealpath] タイルをクリックすると、Dealpath のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、<bpt id="p1">[</bpt>マイ アプリの概要<ept id="p1">](../user-help/my-apps-portal-end-user-access.md)</ept>に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Dealpath を構成したら、組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 <bpt id="p1">[</bpt>Microsoft Cloud App Security でセッション制御を強制する方法<ept id="p1">](/cloud-app-security/proxy-deployment-aad)</ept>をご覧ください。