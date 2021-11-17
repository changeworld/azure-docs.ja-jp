---
title: 'チュートリアル: Azure AD SSO と The Cloud Security Fabric の統合'
description: Azure Active Directory と The Cloud Security Fabric の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 17c3275b022c1b32b452eac3e672aea938cc57eb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344790"
---
# <a name="tutorial-azure-ad-sso-integration-with-the-cloud-security-fabric"></a>チュートリアル: Azure AD SSO と The Cloud Security Fabric の統合

このチュートリアルでは、The Cloud Security Fabric と Azure Active Directory (Azure AD) を統合する方法について説明します。 The Cloud Security Fabric と Azure AD を統合すると、次のことができます。

* The Cloud Security Fabric にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して自動的に The Cloud Security Fabric にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* The Cloud Security Fabric でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* The Cloud Security Fabric では、**SP** Initiated SSO がサポートされます。

## <a name="add-the-cloud-security-fabric-from-the-gallery"></a>ギャラリーから The Cloud Security Fabric を追加する

The Cloud Security Fabric の Azure AD への統合を構成するには、ギャラリーからマネージド SaaS アプリのリストに The Cloud Security Fabric を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**The Cloud Security Fabric**」と入力します。
1. 結果のパネルから **[The Cloud Security Fabric]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-the-cloud-security-fabric"></a>The Cloud Security Fabric の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、The Cloud Security Fabric に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと The Cloud Security Fabric の関連ユーザーとの間にリンク関係を確立する必要があります。

The Cloud Security Fabric に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[The Cloud Security Fabric SSO の構成](#configure-the-cloud-security-fabric-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[The Cloud Security Fabric のテスト ユーザーの作成](#create-the-cloud-security-fabric-test-user)** - The Cloud Security Fabric で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **The Cloud Security Fabric** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** テキスト ボックスに、次のいずれかのパターンを使用して URL を入力します。

      | **サインオン URL** |
      |--------|
      | `https://platform.cloudlock.com` |
      | `https://app.cloudlock.com` |
      
   b. **[識別子 (エンティティ ID)]** ボックスに、次のいずれかのパターンを使用して URL を入力します。

      | **Identifier** |
      |---------|
      | `https://platform.cloudlock.com/gate/saml/sso/<subdomain>` |
      | `https://app.cloudlock.com/gate/saml/sso/<subdomain>` |
     
    > [!NOTE]
    > この識別子の値は実際のものではありません。 この値を実際の識別子で更新してください。 値を取得する場合は、[The Cloud Security Fabric クライアント サポート チーム](mailto:support@cloudlock.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

5. **[署名]** オプションを要件に応じて変更するには、 **[編集]** ボタンをクリックして **[SAML 署名証明書]** ダイアログを開きます。

    ![SAML 応答](./media/ciscocloudlock-tutorial/saml.png)

    a. **[署名オプション]** で **[SAML 応答とアサーションへの署名]** オプションを選択します。

    b. **[署名アルゴリズム]** に対して **[SHA-256]** を選択します。

    c. **[保存]** をクリックします。  

6. **[The Cloud Security Fabric のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に The Cloud Security Fabric へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[The Cloud Security Fabric]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-the-cloud-security-fabric-sso"></a>The Cloud Security Fabric SSO の構成

**The Cloud Security Fabric** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [The Cloud Security Fabric サポート チーム](mailto:support@cloudlock.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-the-cloud-security-fabric-test-user"></a>The Cloud Security Fabric テスト ユーザーの作成

このセクションでは、The Cloud Security Fabric で B.Simon というユーザーを作成します。 [The Cloud Security Fabric サポート チーム](mailto:support@cloudlock.com)と協力して、The Cloud Security Fabric プラットフォームでユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる The Cloud Security Fabric のサインオン URL にリダイレクトされます。 

* The Cloud Security Fabric のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [The Cloud Security Fabric] タイルをクリックすると、The Cloud Security Fabric サインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

The Cloud Security Fabric を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
