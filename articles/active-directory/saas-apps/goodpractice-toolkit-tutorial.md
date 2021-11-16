---
title: チュートリアル:Azure Active Directory と Mind Tools Toolkit の統合 | Microsoft Docs
description: Azure Active Directory と Mind Tools Toolkit の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2021
ms.author: jeedes
ms.openlocfilehash: 73deb218b847fe51803f63e53055327dc8a022f8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285388"
---
# <a name="tutorial-azure-active-directory-integration-with-mind-tools-toolkit"></a>チュートリアル:Azure Active Directory と Mind Tools Toolkit の統合

このチュートリアルでは、Mind Tools Toolkit と Azure Active Directory (Azure AD) を統合する方法について説明します。 Mind Tools Toolkit と Azure AD を統合すると、次のことが可能になります。

* Mind Tools Toolkit にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Mind Tools Toolkitt に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

Mind Tools Toolkit と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Mind Tools Toolkit サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Mind Tools Toolkit では、SP Initiated SSO がサポートされます。
* Mind Tools Toolkit では、Just-In-Time ユーザー プロビジョニングがサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-mind-tools-toolkit-from-the-gallery"></a>ギャラリーからの Mind Tools Toolkit の追加

Azure AD への Mind Tools Toolkit の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Mind Tools Toolkit を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左端のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Mind Tools Toolkit**」と入力します。
1. 検索結果から **[Mind Tools Toolkit]** を選択し、そのアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-mind-tools-toolkit"></a>Mind Tools Toolkit の Azure AD SSO の構成とテスト

**B. Simon** というテスト ユーザーを使用して、Mind Tools Toolkit に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Mind Tools Toolkit の関連ユーザーとの間にリンク関係を確立する必要があります。

Mind Tools Toolkit に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Mind Tools Toolkit の SSO の構成](#configure-mind-tools-toolkit-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Mind Tools Toolkit のテスト ユーザーの作成](#create-mind-tools-toolkit-test-user)** - Mind Tools Toolkit で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Mind Tools Toolkit** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションの **[サインオン URL]** ボックスに、`https://app.goodpractice.net/#/<subscriptionUrl>/s/<LOCATION_ID>` というパターンの URL を入力します。

    > [!NOTE]
    > **サインオン URL** は、実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Mind Tools Toolkit クライアント サポート チーム](mailto:support@goodpractice.com)にお問い合わせください。

1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[SAML 署名証明書]** セクションに移動します。 **[フェデレーション メタデータ XML]** の右側にある **[ダウンロード]** を選択して、XML テキストをダウンロードし、お使いのコンピューターに保存します。 XML コンテンツは、選択したオプションによって異なります。

    ![[フェデレーション メタデータ XML] の横にある [ダウンロード] が強調表示された [SAML 署名証明書] セクション](common/metadataxml.png)

1. **[Mind Tools Toolkit のセットアップ]** セクションで、次の URL のうち必要なものをコピーします。

    ![構成 URL が強調表示された [Mind Tools Toolkit のセットアップ] セクション](common/copy-configuration-urls.png)

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

このセクションでは、Mind Tools Toolkit へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Mind Tools Toolkit]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-mind-tools-toolkit-sso"></a>Mind Tools Toolkit の SSO の構成

**Mind Tools Toolkit** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** テキストと以前にコピーした URL を [Mind Tools Toolkit サポート チーム](mailto:support@goodpractice.com)に送信します。 この設定が構成され、SAML SSO 接続が両側で正しく行われます。

### <a name="create-mind-tools-toolkit-test-user"></a>Mind Tools Toolkit のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Mind Tools Toolkit に作成します。 Mind Tools Toolkit では、Just-In-Time ユーザー プロビジョニングがサポートされています。これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Mind Tools Toolkit にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Mind Tools Toolkit のサインオン URL にリダイレクトされます。 

* Mind Tools Toolkit のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Mind Tools Toolkit] タイルをクリックすると、Mind Tools Toolkit のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Mind Tools Toolkit を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
