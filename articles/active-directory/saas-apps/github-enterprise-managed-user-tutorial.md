---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と GitHub Enterprise Managed User の統合 | Microsoft Docs'
description: Azure Active Directory と GitHub Enterprise Managed User の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/15/2021
ms.author: jeedes
ms.openlocfilehash: 6038db88610e1fde8d95c2c31a9bcfa2c5ad5ac2
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904249"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-managed-user"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と GitHub Enterprise Managed User の統合

このチュートリアルでは、GitHub Enterprise Managed User (EMU) と Azure Active Directory (Azure AD) を統合する方法について説明します。 GitHub Enterprise Managed User と Azure AD を統合すると、次のことができます。

* GitHub Enterprise Managed User にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して GitHub Enterprise Managed User に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* GitHub Enterprise Managed User でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* GitHub Enterprise Managed User では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。
* GitHub Enterprise Managed User には、[**自動化された** ユーザー プロビジョニング](./github-enterprise-managed-user-provisioning-tutorial.md)が必要です。

## <a name="adding-github-enterprise-managed-user-from-the-gallery"></a>ギャラリーからの GitHub Enterprise Managed User の追加

Azure AD への GitHub Enterprise Managed User の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に GitHub Enterprise Managed User を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**GitHub Enterprise Managed User**」と入力します。
1. 結果のパネルから **[GitHub Enterprise Managed User]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-managed-user"></a>GitHub Enterprise Managed User の Azure AD SSO の構成とテスト

GitHub Enterprise Managed User に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - AAD テナントで SAML シングルサインオンを有効にします。
1. **[GitHub Enterprise Managed User の SSO の構成](#configure-github-enterprise-managed-user-sso)** - GitHub Enterprise でシングル サインオン設定を構成します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **GitHub Enterprise Managed User** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. 始める前に Enterprise URL があることを確認します。 下に示す ENTITY フィールドは、EMU 対応 Enterprise URL の Enterprise 名です。 たとえば、 https://github.com/enterprises/contoso - **contoso** が ENTITY です。 **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://github.com/enterprises/<ENTITY>` の形式で URL を入力します。
    
    > [!NOTE]
    > 識別子の形式は、アプリケーションの推奨形式とは異なります。上の形式に従ってください。 さらに、**識別子の末尾にスラッシュを付けないでください。
    
    b. **[応答 URL]** ボックスに、`https://github.com/enterprises/<ENTITY>/saml/consume` のパターンを使用して URL を入力します
    

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://github.com/enterprises/<ENTITY>/sso` という形式で URL を入力します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificate-base64-download.png)

1. **[Set up GitHub Enterprise Managed User]\(GitHub Enterprise Managed User の設定\)** セクションで、下の URL をコピーして、下で GitHub を構成するために保存します。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、SSO の設定を完了するために、アカウントを GitHub Enterprise Managed User に割り当てます。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[GitHub Enterprise Managed User]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] リストで自分のアカウントを選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. **[ロールの選択]** ダイアログで、 **[Enterprise Owner]\(エンタープライズ所有者\)** ロールを選択してから、画面の下部にある **[選択]** ボタンをクリックします。 アカウントは、次のチュートリアルでアカウントをプロビジョニングするときに、GitHub インスタンスのエンタープライズ所有者として割り当てられます。 
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-github-enterprise-managed-user-sso"></a>GitHub Enterprise Managed User SSO の構成

**GitHub Enterprise Managed User** 側でシングル サインオンを構成するには、次のものが必要になります。

1. 上記の AAD Enterprise Managed User Application からの URL: ログイン URL、Azure AD 識別子、ログアウト URL
1. GitHub Enterprise の最初の管理者ユーザーのアカウント名とパスワード。 資格情報は、GitHub Solutions Engineering 担当者からパスワード リセット電子メールで送られてきます。 

### <a name="enable-github-enterprise-managed-user-saml-sso"></a>GitHub Enterprise Managed User SAML SSO の有効化

このセクションでは、上記の AAD から提供されている情報を取得し、それらを Enterprise 設定に入力して、SSO のサポートを有効にします。

1. [https://resources.azure.com](https://github.com) に移動します
1. 右上隅の [サインイン] をクリックします。
1. 最初の管理者ユーザー アカウントの資格情報を入力します。 ログイン ハンドルの形式は `<your enterprise short code>_admin` にします。
1. `https://github.com/enterprises/` `<your enterprise name>` に移動します。 この情報は、Solutions Engineering 担当者から提供されます。
1. 左側のナビゲーション メニューで、 **[設定]** 、 **[セキュリティ]** の順に選択します。
1. **[SAML 認証の有効化]** チェックボックスをオンにします。
1. サインオン URL を入力します。 この URL は、上の AAD からコピーしたログイン URL です。
1. 発行者を入力します。 この URL は、上の AAD からコピーした Azure AD 識別子です。
1. 公開証明書を入力します。 上でダウンロードした base64 証明書を開いて、そのファイルのテキストの内容をこのダイアログに貼り付けます。
1. **[Test SAML configuration]\(SAML 構成のテスト\)** をクリックします。 これにより、Azure AD 資格情報でログインして SAML SSO が正しく構成されていることを確認するためのダイアログが開きます。 自分の AAD 資格情報でログインします。 検証に成功すると **[Passed: Successfully authenticated your SAML SSO identity]\(成功: SAML SSO ID が正常に認証されました\)** メッセージが表示されます。
1. **[保存]** をクリックしてこれらの設定を保持します。
1. 回復用コードは安全な場所に保存 (ダウンロード、印刷、またはコピー) してください。
1. **[SAML 認証を有効にする]** をクリックします。
1. この時点で、SSO が設定されたアカウントだけが Enterprise にログインできます。 SSO によってサポートされるアカウントをプロビジョニングするには、下のプロビジョニングに関するドキュメントの手順に従ってください。

## <a name="next-steps"></a>次のステップ

GitHub Enterprise Managed User では、すべてのアカウントが自動ユーザー プロビジョニングによって作成されることが **必要** です。自動ユーザー プロビジョニングの構成方法の詳細は、[こちら](./github-enterprise-managed-user-provisioning-tutorial.md)をご覧ください。
