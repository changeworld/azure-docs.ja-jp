---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Fabric の統合 | Microsoft Docs'
description: Azure Active Directory と Fabric の間でシングル サインオンを構成する方法について確認します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2021
ms.author: jeedes
ms.openlocfilehash: 950468ff48a0d384d46a04fa3b2a93b0419c2fbe
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132314140"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fabric"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Fabric の統合

このチュートリアルでは、Fabric と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Fabric を統合すると、次のことが可能になります。

* Fabric にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Fabric に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Fabric でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Fabric では、**SP** Initiated SSO がサポートされます。

## <a name="adding-fabric-from-the-gallery"></a>ギャラリーからの Fabric の追加

Azure AD への Fabric の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Fabric を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Fabric**」と入力します。
1. 結果パネルから **Fabric** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-fabric"></a>Fabric の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Fabric に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Fabric の関連ユーザーとの間にリンク関係を確立する必要があります。

Fabric に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Fabric の SSO の構成](#configure-fabric-sso)** - アプリケーション側でシングル サインオンの設定を構成します。
    1. **[Fabric のロールの作成](#create-fabric-roles)** - Fabric で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Fabric** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

   1. **[識別子]** ボックスに、次の形式で URL を入力します。  
      `https://<HOSTNAME>`

   1. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。  
      `https://<HOSTNAME>:<PORT>/api/authenticate`
    
   1. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。  
      `https://<HOSTNAME>:<PORT>`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は、実際の識別子、応答 URL、サインオン URL で更新してください。 これらの値を取得するには、K2View COE チームに問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Fabric のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

1. **[トークン暗号化]** セクションで **[証明書のインポート]** を選択し、Fabric の証明書ファイルをアップロードします。 それを取得するには、K2View COE チームに連絡してください。

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

このセクションでは、B.Simon に Fabric へのアクセスを許可して、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーション一覧で、**Fabric** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-fabric-sso"></a>Fabric の SSO の構成

**Fabric** 側でシングル サインオンを構成するには、ダウンロードした **証明書 (Base64)** と Azure portal からコピーした適切な URL を K2View COE サポート チームに送る必要があります。 SAML SSO 接続が両側で正しく設定されるように、チームによって設定が構成されます。

詳細については、[K2view のナレッジ ベース](https://support.k2view.com/knowledge-base.html)で "*Fabric SAML の構成*" および "*Azure AD SAML セットアップ ガイド*" に関する記事を参照してください。

### <a name="create-fabric-roles"></a>Fabric のロールを作成する

K2View COE のサポート チームと協力して、Azure AD グループと一致し、Fabric を使用するユーザーに関連する、Fabric ロールを設定します。 SAML の応答で送信されるので、グループ ID を Fabric のチームに提供します。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で、 **[このアプリケーションをテストします]** を選択します。 Fabric のサインオン URL にリダイレクトされ、ログイン フローを開始することができます。 

* Fabric のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリ ポータルで **[Fabric]** タイルを選択すると、Fabric のサインオン URL にリダイレクトされます。 マイ アプリ ポータルの詳細については、[マイ アプリ ポータルの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Fabric を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-any-app)。
