---
title: 'チュートリアル: Azure AD SSO と Coverity 静的アプリケーションのセキュリティ テストの統合'
description: Azure Active Directory と Coverity 静的アプリケーションのセキュリティ テストの間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/03/2021
ms.author: jeedes
ms.openlocfilehash: 6faeb92b62892b29ee4fc27fc8b075f6a22a40b2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132338518"
---
# <a name="tutorial-azure-ad-sso-integration-with-coverity-static-application-security-testing"></a>チュートリアル: Azure AD SSO と Coverity 静的アプリケーションのセキュリティ テストの統合

このチュートリアルでは、Coverity 静的アプリケーションのセキュリティ テストと Azure Active Directory (Azure AD) を統合する方法について説明します。 Coverity 静的アプリケーションのセキュリティ テストと Azure AD を統合すると、次のことができます。

* Coverity 静的アプリケーションのセキュリティ テストにアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Coverity 静的アプリケーションのセキュリティ テストに自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Coverity 静的アプリケーションのセキュリティ テストでのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Coverity 静的アプリケーションのセキュリティ テストでは、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。

## <a name="add-coverity-static-application-security-testing-from-the-gallery"></a>Coverity 静的アプリケーションのセキュリティ テストをギャラリーから追加する

Coverity 静的アプリケーションのセキュリティ テストの Azure AD への統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Coverity 静的アプリケーションのセキュリティ テストを追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Coverity 静的アプリケーションのセキュリティ テスト**」と入力します。
1. 結果のパネルから **[Coverity 静的アプリケーションのセキュリティ テスト]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-coverity-static-application-security-testing"></a>Coverity 静的アプリケーションのセキュリティ テストの Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Coverity 静的アプリケーションのセキュリティ テストに対して Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Coverity 静的アプリケーションのセキュリティ テストの関連ユーザーとの間にリンク関係を確立する必要があります。

Coverity 静的アプリケーションのセキュリティ テストに対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Coverity 静的アプリケーションのセキュリティ テストの SSO の構成](#configure-coverity-static-application-security-testing-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Coverity 静的アプリケーションのセキュリティ テストのテスト ユーザーの作成](#create-coverity-static-application-security-testing-test-user)** - Coverity 静的アプリケーションのセキュリティ テストで B.Simon に対応するユーザーを作成し、Azure AD のそのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Coverity 静的アプリケーションのセキュリティ テスト** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。 

    a. **[識別子]** ボックスに、`https://<COVERITYURL>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<COVERITYURL>` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<COVERITYURL>` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Coverity 静的アプリケーションのセキュリティ テスト クライアント サポート チーム](mailto:software-integrity-support@synopsys.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (PEM)]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificate-base64-download.png)

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

このセクションでは、B.Simon に Coverity 静的アプリケーションのセキュリティ テストへのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Coverity 静的アプリケーションのセキュリティ テスト]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-coverity-static-application-security-testing-sso"></a>Coverity 静的アプリケーションのセキュリティ テストの SSO の構成

**Coverity 静的アプリケーションのセキュリティ テスト** 側でシングル サインオンを構成するには、ダウンロードした **証明書 (PEM)** と Azure portal からコピーした適切な URL を、[Coverity 静的アプリケーションのセキュリティ テスト サポート チーム](mailto:software-integrity-support@synopsys.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-coverity-static-application-security-testing-test-user"></a>Coverity 静的アプリケーションのセキュリティ テストのテスト ユーザーの作成

このセクションでは、Coverity 静的アプリケーションのセキュリティ テストで Britta Simon というユーザーを作成します。 [Coverity 静的アプリケーションのセキュリティ テスト サポート チーム](mailto:software-integrity-support@synopsys.com)と連携して、Coverity 静的アプリケーションのセキュリティ テスト プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始する Coverity 静的アプリケーションのセキュリティ テストのサインオン URL にリダイレクトされます。  

* Coverity 静的アプリケーションのセキュリティ テストのサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Coverity 静的アプリケーションのセキュリティ テストに自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Coverity 静的アプリケーションのセキュリティ テスト] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Coverity 静的アプリケーションのセキュリティ テストに自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Coverity 静的アプリケーションのセキュリティ テストを構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を強制することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
