---
title: 'チュートリアル: Azure AD SSO と Symantec Web Security Service (WSS) の統合'
description: Azure Active Directory と Symantec Web Security Service (WSS) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2021
ms.author: jeedes
ms.openlocfilehash: 572a09fa957ae271d131358a612f00bc9ae2107e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132316835"
---
# <a name="tutorial-azure-ad-sso-integration-with-symantec-web-security-service-wss"></a>チュートリアル: Azure AD SSO と Symantec Web Security Service (WSS) の統合

このチュートリアルでは、Symantec Web Security Service (WSS) アカウントを Azure Active Directory (Azure AD) アカウントに統合して、WSS で Azure AD にプロビジョニングされたエンド ユーザーを SAML 認証を使用して認証し、ユーザー レベルまたはグループ レベルのポリシー ルールを適用できるようにする方法について説明します。

Symantec Web Security Service (WSS) と Azure AD の統合には、次の利点があります。

* WSS アカウントによって使用されるすべてのエンド ユーザーとグループを Azure AD ポータルから管理できます。

* エンド ユーザーは、Azure AD 資格情報を使用して WSS で自己認証を行うことができます。

* WSS アカウントに定義されたユーザー レベルおよびグループ レベルのポリシー ルールを適用できます。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Symantec Web Security Service (WSS) シングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Symantec Web Security Service (WSS) では、**IDP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>ギャラリーから Symantec Web Security Service (WSS) を追加する

Azure AD への Symantec Web Security Service (WSS) の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから Symantec Web Security Service (WSS) を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Symantec Web Security Service (WSS)** 」と入力します。
1. [結果] パネルで **[Symantec Web Security Service (WSS)]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-symantec-web-security-service-wss"></a>Symantec Web Security Service (WSS) の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Symantec Web Security Service (WSS) に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Symantec Web Security Service (WSS) の関連ユーザーの間で、リンク関係を確立する必要があります。

Symantec Web Security Service (WSS) で Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Symantec Web Security Service (WSS) の SSO の構成](#configure-symantec-web-security-service-wss-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Symantec Web Security Service (WSS) テスト ユーザーの作成](#create-symantec-web-security-service-wss-test-user)** - Symantec Web Security Service (WSS) で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Symantec Web Security Service (WSS)** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** ダイアログ ボックスで、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://saml.threatpulse.net:8443/saml/saml_realm` という URL を入力します。

    b. **[応答 URL]** ボックスに、URL として「`https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`」と入力します。

    > [!NOTE]
    > **ID** と **応答 URL** の値がなんらかの理由で有効でない場合には、[Symantec Web Security Service (WSS) クライアント サポート チーム](https://www.symantec.com/contact-us)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

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

このセクションでは、B.Simon に Symantec Web Security Service (WSS) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、**[Symantec Web Security Service (WSS)]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-symantec-web-security-service-wss-sso"></a>Symantec Web Security Service (WSS) の SSO の構成

Symantec Web Security Service (WSS) 側にシングル サインオンを構成するには、WSS のオンライン ドキュメントを参照してください。 ダウンロードした **フェデレーション メタデータ XML** は、WSS ポータルにインポートする必要があります。 WSS ポータルの構成でサポートが必要な場合には、[Symantec Web Security Service (WSS) サポート チーム](https://www.symantec.com/contact-us)に問い合わせてください。

### <a name="create-symantec-web-security-service-wss-test-user"></a>Symantec Web Security Service (WSS) のテスト ユーザーの作成

このセクションでは、Symantec Web Security Service (WSS) で Britta Simon というユーザーを作成します。 対応するエンド ユーザー名を WSS ポータルで手動で作成するか、または、Azure AD にプロビジョニングされたユーザーまたはグループが数分後 (最大 15 分) に WSS ポータルに同期されるのを待ちます。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 また、Web サイトを参照するために使用する、エンド ユーザー マシンのパブリック IP アドレスを Symantec Web Security Service (WSS) ポータルにプロビジョニングする必要もあります。

> [!NOTE]
> [ここ](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1)をクリックして、マシンのパブリック IP アドレスを取得してください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Symantec Web Security Service (WSS) に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Symantec Web Security Service (WSS)] タイルをクリックすると、SSO を設定した Symantec Web Security Service (WSS) に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Symantec Web Security Service (WSS) を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を強制することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
