---
title: 'チュートリアル: Azure AD SSO と Percolate の統合'
description: このチュートリアルでは、Azure Active Directory と Percolate の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/06/2021
ms.author: jeedes
ms.openlocfilehash: ad44d1fd0f1968549dec090f9895c3c62a589eb0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329877"
---
# <a name="tutorial-azure-ad-sso-integration-with-percolate"></a>チュートリアル: Azure AD SSO と Percolate の統合

このチュートリアルでは、Percolate と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Percolate を統合すると、次のことができます。

* Percolate にアクセスできる Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Percolate に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Percolate と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオンが有効な Percolate サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Percolate では、SP-initiated SSO と IdP-initiated SSO がサポートされます。

## <a name="add-percolate-from-the-gallery"></a>ギャラリーから Percolate を追加する

Azure AD への Percolate の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Percolate を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Percolate**」と入力します。
1. 結果のパネルから **[Percolate]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-percolate"></a>Percolate に対する Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Percolate に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Percolate の関連ユーザーとの間にリンク関係を確立する必要があります。

Percolate に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Percolate SSO の構成](#configure-percolate-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Percolate テスト ユーザーの作成](#create-percolate-test-user)** - Percolate で B.Simon に対応するユーザーを作成し、Azure AD のそのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Percolate** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** ダイアログ ボックスでは、IdP-initiated モードでアプリケーションを構成するためのアクションは必要ありません。 アプリは既に Azure と統合されています。

5. SP Initiated モードでアプリケーションを構成する場合は、 **[追加の URL を設定します]** を選択し、 **[サインオン URL]** ボックスに **https://percolate.com/app/login** と入力します。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**コピー** アイコンを選択して **[アプリのフェデレーション メタデータ URL]** をコピーします。 この URL を保存します。

    ![アプリのフェデレーション メタデータ URL のコピー](common/copy-metadataurl.png)

7. **[Percolate のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL をコピーする](common/copy-configuration-urls.png)

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

このセクションでは、Percolate へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Percolate]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-percolate-sso"></a>Percolate SSO を構成する

1. 新しい Web ブラウザー ウィンドウで、管理者として Percolate にサインインします。

2. ホーム ページの左側で、 **[設定]** を選択します。
    
    ![[設定] を選択する](./media/percolate-tutorial/menu.png)

3. 左側のウィンドウで、 **[組織]** の下にある **[SSO]** を選択します。

    ![[組織] の下にある [SSO] の選択](./media/percolate-tutorial/metadata.png)

    1. **[ログイン URL]** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    1. **[エンティティ ID]** ボックスに、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。

    1. メモ帳で、Azure portal からダウンロードした base 64 エンコード証明書を開きます。 その内容をコピーして、 **[x509 certificates]\(x509 証明書\)** ボックスに貼り付けます。

    1. **[Email attribute]\(電子メール属性\)** ボックスに、「**emailaddress**」と入力します。

    1. **[Identity provider metadata URL]\(ID プロバイダー メタデータ URL\)** ボックスはオプションのフィールドです。 **[アプリのフェデレーション メタデータ URL]** を Azure portal からコピーした場合、それをこのボックスに貼り付けることができます。

    1. **[Should AuthNRequests be signed?]\(AuthNRequests には署名が必要ですか?\)** リストで、 **[いいえ]** を選択します。

    1. **[Enable SSO auto-provisioning]\(SSO の自動プロビジョニングを有効にする\)** リストで、 **[いいえ]** を選択します。

    1. **[保存]** を選択します。

### <a name="create-percolate-test-user"></a>Percolate テスト ユーザーの作成

Azure AD ユーザーが Percolate にサインインできるようにするには、それらを Percolate に追加する必要があります。 手動で追加する必要があります。

ユーザー アカウントを作成するには、以下の手順に従います。

1. 管理者として Percolate にサインインします。

2. 左側のウィンドウで、 **[組織]** の下にある **[ユーザー]** を選択します。 **[新しいユーザー]** を選択します。

    ![[新しいユーザー] の選択](./media/percolate-tutorial/users.png)

3. **[ユーザーの作成]** ページで、以下の手順を実行します。

    ![[ユーザーの作成] ページ](./media/percolate-tutorial/new-user.png)

    1. **[電子メール]** ボックスに、ユーザーの電子メール アドレスを入力します。 たとえば、「 brittasimon@contoso.com 」のように入力します。

    1. **[フル ネーム]** ボックスに、ユーザーの名前を入力します。 たとえば、「**Brittasimon**」と入力します。

    1. **[ユーザーの作成]** を選択します。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Percolate のサインオン URL にリダイレクトされます。  

* Percolate のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Percolate に自動的にサインインします。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Percolate] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Percolate に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Percolate を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
