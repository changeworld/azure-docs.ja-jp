---
title: 'チュートリアル: Azure Active Directory と Soloinsight-CloudGate SSO の統合 | Microsoft Docs'
description: Azure Active Directory と Soloinsight-CloudGate SSO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: 0fabc06ae9656015296be8c6d96a0f3c3313bc3c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132333335"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>チュートリアル: Soloinsight-CloudGate SSO と Azure Active Directory の統合

このチュートリアルでは、Soloinsight-CloudGate SSO を Azure Active Directory (Azure AD) と統合する方法について学習します。 Soloinsight CloudGate SSO を Azure AD と統合すると、次のことが可能になります。

* Soloinsight-CloudGate SSO にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して、Soloinsight-CloudGate SSO に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Soloinsight-CloudGate SSO シングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Soloinsight-CloudGate SSO では、**SP** によって開始される SSO がサポートされます。
* Soloinsight-CloudGate SSO では、[自動化されたユーザー プロビジョニング](soloinsight-cloudgate-sso-provisioning-tutorial.md)がサポートされます。

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>ギャラリーから Soloinsight-CloudGate SSO を追加する

Soloinsight-CloudGate SSO の Azure AD への統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Soloinsight-CloudGate SSO を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Soloinsight-CloudGate SSO**」と入力します。
1. 結果パネルから **[Soloinsight-CloudGate SSO]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-soloinsight-cloudgate-sso"></a>Soloinsight-CloudGate SSO に対する Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Soloinsight-CloudGate SSO に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Soloinsight-CloudGate SSO の関連ユーザーの間で、リンク関係を確立する必要があります。

Soloinsight-CloudGate SSO に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Soloinsight-CloudGate SSO の構成](#configure-soloinsight-cloudgate-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Soloinsight-CloudGate SSO のテスト ユーザーの作成](#create-soloinsight-cloudgate-sso-test-user)** - Soloinsight-CloudGate SSO で B.Simon に対応するユーザーを作成し、Azure AD のユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Soloinsight-CloudGate SSO** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次の手順を実行します。

    1. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.sigateway.com/login`

    1. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > これらは実際の値ではありません。 これらの値を実際のサインオン URL と識別子に更新します。これについては、このチュートリアルの「**Soloinsight-CloudGate SSO のシングル サインオンの構成**」セクションで後述します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Set up Soloinsight-CloudGate SSO]\(Soloinsight-CloudGate SSO のセットアップ\)** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Soloinsight-CloudGate SSO へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Soloinsight-CloudGate SSO]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-soloinsight-cloudgate-sso"></a>Soloinsight-CloudGate SSO を構成する

1. Soloinsight-CloudGate SSO 内での構成を自動化するには、**[拡張機能のインストール]** をクリックして、**My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、**[Setup Soloinsight-CloudGate SSO]\(Soloinsight-CloudGate SSO のセットアップ\)** をクリックすると、Soloinsight-CloudGate SSO アプリケーションに移動します。 そこから、管理者資格情報を提供して Soloinsight-CloudGate SSO にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 8 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Soloinsight-CloudGate SSO を手動でセットアップしたい場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Soloinsight-CloudGate SSO 企業サイトにサインインし、次の手順を行います。

4. 基本的な SAML の構成中に Azure portal で貼り付ける値を取得するには、ご自分の資格情報を使用して CloudGate Web ポータルにサインインし、SSO 設定にアクセスします。その場合、**[ホーム] > [管理] > [システム設定] > [全般]** の順に移動します。

    ![CloudGate SSO 設定](./media/soloinsight-cloudgate-sso-tutorial/main-settings.png)

5. **SAML コンシューマー URL**

    * **[Saml Consumer URL]\(SAML コンシューマー URL\)** と **[リダイレクト URL]** の各フィールドに対して使用可能なリンクをコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子 (エンティティ ID)]** フィールドと **[応答 URL]** フィールドにそれぞれ貼り付けます。

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/identifier.png)

6. **SAML 署名証明書**

    * Azure portal の SAML 署名証明書リストからダウンロードされた証明書 (Base64) ファイルのソースに移動して、右クリックします。 一覧から、**[Edit with Notepad++]\(Notepad++ で編集\)** オプションを選択します。 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * 証明書 (Base64) Notepad++ ファイルの内容をコピーします。

        ![証明書のコピー](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * その内容を CloudGate Web ポータルの SSO 設定の **[Certificate]\(証明書\)** フィールドに貼り付け、保存ボタンをクリックします。

        ![証明書ポータル](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **既定のグループ**

    * CloudGate Web ポータルの **[Default Group]\(既定のグループ\)** オプションのドロップダウン リストから **[Business Admin]\(ビジネス管理\)** を選択します。

        ![既定のグループ](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **AD 識別子とログイン URL**

    * Azure portal の **[Set up Soloinsight-CloudGate SSO]\(Soloinsight-CloudGate SSO のセットアップ\)** 構成からコピーした **ログイン URL** を、CloudGate Web ポータルの SSO 設定セクションに入力します。

    * Azure portal の **ログイン URL** リンクを、CloudGate Web ポータルの **[AD Login URL]\(AD ログイン URL\)** フィールドに貼り付けます。

    * Azure portal の **Azure AD 識別子** リンクを、CloudGate Web ポータルの **[AD Identifier]\(AD 識別子\)** フィールドに貼り付けます

        ![AD ログイン](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Soloinsight CloudGate SSO テスト ユーザーを作成する

テスト ユーザーを作成するには、CloudGate Web ポータルのメイン メニューで **[Employees]\(従業員\)** を選択し、[Add New employee]\(新しい従業員の追加\) フォームに入力します。 テスト ユーザーに割り当てられる権限のレベルは **[Business Admin]\(ビジネス管理\)** です。**[Create]\(作成\)** をクリックすると、必要なフィールドがすべて入力されます。

![テスト用の従業員](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

> [!NOTE]
> Soloinsight-CloudGate SSO では、自動ユーザー プロビジョニングもサポートされます。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](./soloinsight-cloudgate-sso-provisioning-tutorial.md)をご覧ください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Soloinsight-CloudGate SSO のサインオン URL にリダイレクトされます。 

* Soloinsight-CloudGate SSO のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで Soloinsight-CloudGate SSO タイルをクリックすると、Soloinsight-CloudGate SSO のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Soloinsight-CloudGate SSO を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
