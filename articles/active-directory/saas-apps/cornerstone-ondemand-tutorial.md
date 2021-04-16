---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Cornerstone Single Sign-On の統合 | Microsoft Docs'
description: Azure Active Directory と Cornerstone Single Sign-On の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2021
ms.author: jeedes
ms.openlocfilehash: ba6eb0a1b607fc05c4d0c660dd3d7016f81ef4b3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449501"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-single-sign-on"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Cornerstone Single Sign-On の統合

このチュートリアルでは、Cornerstone Single Sign-On と Azure Active Directory (Azure AD) を統合する方法について説明します。 Cornerstone Single Sign-On を Azure AD と統合すると、次のことができます。

* Cornerstone Single Sign-On にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Cornerstone Single Sign-On に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Cornerstone Single Sign-On でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Cornerstone Single Sign-On では、**SP** Initiated SSO がサポートされます。
* Cornerstone Single Sign-On では、[自動化されたユーザー プロビジョニング](cornerstone-ondemand-provisioning-tutorial.md)がサポートされます。


## <a name="adding-cornerstone-single-sign-on-from-the-gallery"></a>ギャラリーからの Cornerstone Single Sign-On の追加

Azure AD への Cornerstone Single Sign-On の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Cornerstone Single Sign-On を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Cornerstone Single Sign-On**」と入力します。
1. 結果パネルから **[Cornerstone Single Sign-On]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-cornerstone-single-sign-on"></a>Cornerstone Single Sign-On の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Cornerstone Single Sign-On に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Cornerstone Single Sign-On の関連ユーザーとの間にリンク関係を確立する必要があります。

Cornerstone Single Sign-On で Azure AD SSO を構成してテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Cornerstone Single Sign-On の SSO の構成](#configure-cornerstone-single-sign-on-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Cornerstone Single Sign-On のテスト ユーザーの作成](#create-cornerstone-single-sign-on-test-user)** - Cornerstone Single Sign-On で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Cornerstone Single Sign-On** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://<PORTAL_NAME>.csod.com` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>` のパターンを使用して URL を入力します

    c. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    > [!NOTE]
    > これらは実際の値ではありません。 実際の応答 URL、識別子、およびサインオン URL でこれらの値を更新します。 これらの値を取得するには、[Cornerstone Single Sign-On クライアント サポート チーム](mailto:moreinfo@csod.com)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Cornerstone Single Sign-On のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Cornerstone Single Sign-On へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーション一覧で **[Cornerstone Single Sign-On]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-cornerstone-single-sign-on-sso"></a>Cornerstone Single Sign-On SSO の構成

**Cornerstone Single Sign-On** 側でシングル サインオンを構成するには、ダウンロードした **証明書 (Base64)** と Azure portal からコピーした適切な URL を [Cornerstone Single Sign-On サポート チーム](mailto:moreinfo@csod.com)に送信するか、パートナーまでお問い合わせください。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-cornerstone-single-sign-on-test-user"></a>Cornerstone Single Sign-On のテスト ユーザーの作成

このセクションの目的は、Cornerstone Single Sign-On で B.Simon というユーザーを作成することです。 Cornerstone Single Sign-On では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](./cornerstone-ondemand-provisioning-tutorial.md)をご覧ください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Cornerstone Single Sign-On のサインオン URL にリダイレクトされます。 

* Cornerstone Single Sign-On のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Cornerstone Single Sign-On] タイルをクリックすると、Cornerstone Single Sign-On のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Cornerstone Single Sign-On を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。