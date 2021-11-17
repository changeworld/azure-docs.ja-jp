---
title: チュートリアル:Azure Active Directory と Beeline の統合 | Microsoft Docs
description: Azure Active Directory と Beeline の間でシングル サインオンを構成する方法について確認します。
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
ms.openlocfilehash: ff9611559467f24d1b01d4e628bdcf9642cca88a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132336147"
---
# <a name="tutorial-azure-active-directory-integration-with-beeline"></a>チュートリアル:Azure Active Directory と Beeline の統合

このチュートリアルでは、BeeLine と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Beeline を統合すると、次のことができます。

* Beeline にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Beeline に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Beeline でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Beeline では、**IDP** Initiated SSO のみがサポートされます。

## <a name="add-beeline-from-the-gallery"></a>ギャラリーからの Beeline の追加

Azure AD への Beeline の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Beeline を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに **[Beeline]** と入力します。
1. 結果のパネルから **[Beeline]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-beeline"></a>Beeline の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Beeline で Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Beeline の関連ユーザーとの間にリンク関係を確立する必要があります。

Beeline で Azure AD SSO を構成してテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Beeline SSO の構成](#configure-beeline-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Beeline のテスト ユーザーの作成](#create-beeline-test-user)** - Beeline で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Beeline** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://projects.beeline.com/<ProjInstance_Name>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://projects.beeline.com/<ProjInstance_Name>/SSO_External.ashx` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[Beeline クライアント サポート チーム](https://www.beeline.com/contact-support/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. Beeline アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 アプリケーションにマップされる適切なユーザー ID を識別するには、まず [Beeline サポート チーム](https://www.beeline.com/contact-support/)と協力してください。 また、このマッピングに使用する属性については、[Beeline サポート チーム](https://www.beeline.com/contact-support/)からのガイダンスに従ってください。 この属性の値は、アプリケーションの **[ユーザー属性]** タブから管理できます。 次のスクリーンショットはその例です。 ここでは **User Identifier** 要求を **userprincipalname** 属性にマッピングしています。これは一意のユーザー ID となり、SAML 応答が成功するたびに Beeline アプリケーションに送信されます。

    ![image](common/edit-attribute.png)

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. [Azure portal](https://portal.azure.com/) の **Beeline** アプリケーション統合ページで、 **[プロパティ]** を選択し、ユーザーのアクセス URL をコピーします。

    ![ユーザーのアクセス URL をコピーする](media/beeline-tutorial/client-access-url.png)

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

このセクションでは、B.Simon に Beeline へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Beeline]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-beeline-sso"></a>Beeline SSO の構成

**Beeline** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal のプロパティからコピーしたユーザーのアクセス URL を [Beeline サポート チーム](https://www.beeline.com/contact-support/)に送信する必要があります。 SAML SSO 接続を両方の側で適切に構成するには、メタデータとユーザーのアクセス URL が必要になります。

### <a name="create-beeline-test-user"></a>Beeline のテスト ユーザーの作成

このセクションでは、Beeline で Britta Simon というユーザーを作成します。 Beeline アプリケーションでは、シングル サインオンを行う前に、すべてのユーザーをアプリケーションにプロビジョニングする必要があります。 [Beeline サポート チーム](https://www.beeline.com/contact-support/)と協力して、すべてのユーザーをアプリケーションにプロビジョニングしてください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Beeline に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [BeeLine] タイルをクリックすると、SSO を設定した BeeLine に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Beeline を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
