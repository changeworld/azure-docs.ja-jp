---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Trelica の統合 | Microsoft Docs
description: Azure Active Directory と Trelica の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 577bdae2-dbab-4445-a07e-de0119b65d76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17c68a95530f345d1ec0ed077681ec4cd6eb3775
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402437"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trelica"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Trelica の統合

このチュートリアルでは、Trelica と Azure Active Directory (Azure AD) を統合する方法について説明します。

この統合により、次のことができます。

* Trelica にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Trelica に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、[Azure Active Directory を使用したアプリケーション アクセスとシングル サインオンの概要](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Trelica サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Trelica では、IDP initiated SSO がサポートされます。
* Trelica では、Just-In-Time ユーザー プロビジョニングがサポートされます。
* Trelica を構成した後、セッション制御を適用できます。 この制御により、組織の機密データを流出と侵入からリアルタイムで保護することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-trelica-from-the-gallery"></a>ギャラリーからの Trelica の追加

Azure AD への Trelica の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Trelica を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左端のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Trelica**」と入力します。
1. 検索結果から **[Trelica]** を選択し、そのアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-trelica"></a>Trelica の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Trelica に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Trelica 内の関連ユーザーとの間にリンク関係を確立する必要があります。

Trelica で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Trelica の SSO の構成](#configure-trelica-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Trelica のテスト ユーザーの作成](#create-a-trelica-test-user)** - Trelica で B.Simon に対応するユーザーを作成します。 このユーザーを Azure AD の対応するユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Trelica** アプリケーション統合ページで、 **[管理]** セクションに移動します。 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![[基本的な SAML 構成] の鉛筆アイコンが強調表示された [SAML によるシングル サインオンのセットアップ] ページ](common/edit-urls.png)

1. **[SAML によるシングル サインオンのセットアップ]** ページで、次の値を入力します。

    1. **[識別子]** ボックスに、URL **https://app.trelica.com** を入力します。

    1. **[応答 URL]** ボックスに、`https://app.trelica.com/Id/Saml2/<CUSTOM_IDENTIFIER>/Acs` というパターンの URL を入力します。

    > [!NOTE]
    > **応答 URL** は、実際の値ではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[Trelica クライアント サポート チーム](mailto:support@trelica.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[SAML 署名証明書]** セクションに移動します。 **[アプリのフェデレーション メタデータ URL]** の右側にあるコピー ボタンを選択して、URL をコピーします。 この URL をお使いのコンピューターに保存します。

    ![[アプリのフェデレーション メタデータ URL] の横にあるコピー ボタンが強調表示された [SAML 署名証明書] セクション](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左端のペインで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「**B.Simon**」と入力します。
   1. **[ユーザー名]** フィールドに、「**B.Simon@** _companydomain_ **.** _extension_」と入力します。 たとえば、「 B.Simon@contoso.com 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Trelica へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[Trelica]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションに移動し、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] が強調表示された [管理] セクション](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

   ![[ユーザーの追加] が強調表示された [ユーザーとグループ] ウィンドウ](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[B.Simon]** を選択します。 次に、画面の下部にある **[選択]** ボタンを選択します。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** ボタンを選択します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-trelica-sso"></a>Trelica の SSO の構成

**Trelica** 側でシングル サインオンを構成するには、コピーした **[アプリのフェデレーション メタデータ URL]** の値を [Trelica サポート チーム](mailto:support@trelica.com)に送信します。 この設定が構成され、SAML SSO 接続が両側で正しく行われます。

### <a name="create-a-trelica-test-user"></a>Trelica のテスト ユーザーの作成

このセクションでは、Trelica で B.Simon というユーザーを作成します。

Trelica では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションには、ユーザー側で行うアクションはありません。 Trelica にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、マイ アプリ ポータルを使用して自分の Azure AD のシングル サインオン構成をテストします。

マイ アプリ ポータルで [Trelica] タイルを選択すると、SSO を設定した Trelica に自動的にサインインします。 マイ アプリ ポータルの詳細については、[マイ アプリ ポータルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory の統合に関するチュートリアル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Trelica を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Trelica を保護する方法](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
