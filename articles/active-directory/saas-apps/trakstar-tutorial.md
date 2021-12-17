---
title: 'チュートリアル: Azure Active Directory と Trakstar の統合 | Microsoft Docs'
description: Azure Active Directory と Trakstar の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/27/2021
ms.author: jeedes
ms.openlocfilehash: 0a2d981b749bb90534afe49cbe411c650f415462
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132303928"
---
# <a name="tutorial-azure-active-directory-integration-with-trakstar"></a>チュートリアル: Azure Active Directory と Trakstar の統合

このチュートリアルでは、Trakstar と Azure Active Directory (Azure AD) を統合する方法について説明します。 Trakstar を Azure AD と統合すると、次のことができます。

* Trakstar にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Trakstar に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Azure AD と Trakstar の統合を構成するには、次のアイテムが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Trakstar でのシングル サインオンが有効なサブスクリプション。
* SSO は、Trakstar の有料の機能です。 ご自分の組織で有効にする場合は、[Trakstar クライアント サポート チーム](mailto:support@trakstar.com)に問い合わせてください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Trakstar では、**SP** によって開始される SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-trakstar-from-the-gallery"></a>ギャラリーからの Trakstar の追加

Azure AD への Trakstar の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Trakstar を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Trakstar**」と入力します。
1. 結果のパネルから **[Trakstar]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-trakstar"></a>Trakstar のための Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Trakstar 用に Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Trakstar の関連ユーザーとの間にリンク関係を確立する必要があります。

Trakstar 用に Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Trakstar SSO の構成](#configure-trakstar-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Trakstar のテスト ユーザーの作成](#create-trakstar-test-user)** - Trakstar で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Trakstar** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、対応する入力フィールドに次の値を入力します。

    | フィールド名 | 値 | 注意 | 
    | ---------------------- | ----- | ---- |
    | **応答 URL (Assertion Consumer Service URL)** | `https://app.trakstar.com/auth/saml/callback?namespace=<YOUR_NAMESPACE>` | `<YOUR_NAMESPACE>` は実際の値に置き換えます。これは Trakstar Perform の **ACS (Consumer) URL** フィールドに表示されます。 この表の後に表示される注記を参照してください。 |
    | **サインオン URL** | `https://app.trakstar.com/auth/saml/?namespace=<YOUR_NAMESPACE>` | この URL は前の URL に "_類似_" していますが、`/callback` 部分がありません。 |
    | **識別子 (エンティティ ID)** | `https://app.trakstar.com` | |
    
    > [!NOTE]
    > これらの値は、例としてのみ使用しています。 Trakstar Perform でのご自身の名前空間に固有の値を使用する必要があります。それらを表示するには、アプリケーションにサインインし、 **[設定]**  >  **[Authentication & SSO]\(認証と SSO\)**  >  **[SAML 2.0]**  >  **[構成]** の順に移動します。
    > 
    > **[設定]** に **[Authentication & SSO]\(認証と SSO\)** タブが表示されない場合は、この機能がない可能性があります。Trakstar のカスタマー サポートにお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Trakstar のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Trakstar へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Trakstar]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-trakstar-sso"></a>Trakstar SSO の構成

**Trakstar** 側でシングル サインオンを構成するには、管理者としてサインインし、ダウンロードした **証明書 (Base64)** の内容と Azure portal からコピーした適切な URL を入力する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-trakstar-test-user"></a>Trakstar のテスト ユーザーの作成

このセクションでは、Trakstar で Britta Simon というユーザーを作成します。 Trakstar 管理者と連携し、Trakstar プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Trakstar のサインオン URL にリダイレクトされます。 

* Trakstar のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Trakstar] タイルをクリックすると、Trakstar サインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Trakstar を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
