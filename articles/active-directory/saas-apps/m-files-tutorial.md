---
title: 'チュートリアル: Azure Active Directory と M-Files の統合 | Microsoft Docs'
description: Azure Active Directory と M-Files の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: 66a641cbf5735f6f136c2e765aca593075179303
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313335"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>チュートリアル: Azure Active Directory と M-Files の統合

このチュートリアルでは、M-Files と Azure Active Directory (Azure AD) を統合する方法について説明します。 M-Files と Azure AD を統合すると、次のことができます。

* M-Files にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して M-Files に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* M-Files でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* M-Files では、**SP** Initiated SSO がサポートされます。

## <a name="add-m-files-from-the-gallery"></a>ギャラリーからの M-Files の追加

Azure AD への M-Files の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に M-Files を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**M-Files**」と入力します。
1. 結果のパネルから **[M-Files]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-m-files"></a>M-Files の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、M-Files に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと M-Files の関連ユーザーとの間にリンク関係を確立する必要があります。

M-Files に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[M-Files の SSO の構成](#configure-m-files-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[M-Files テスト ユーザーの作成](#create-m-files-test-user)** - M-Files で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **M-Files** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[M-Files クライアント サポート チーム](mailto:support@m-files.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[M-Files のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に M-Files へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[M-Files]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-m-files-sso"></a>M-Files の SSO の構成

1. アプリケーション用に構成された SSO を入手するには、[M-Files サポート チーム](mailto:support@m-files.com)に連絡して、ダウンロードしたメタデータを提供してください。
   
    >[!NOTE]
    >M-File デスクトップ アプリケーション用に SSO を構成する場合は、次の手順に従います。 M-Files の Web バージョン用に SSO を構成するだけの場合は、追加の手順は必要ありません。  

1. 次の手順に従って M-File デスクトップ アプリケーションを構成し、Azure AD の SSO を有効にします。 M-Files をダウンロードするには、[M-Files のダウンロード](https://www.m-files.com/customers/product-downloads/download-update-links/)ページに移動します。

1. **[M-Files デスクトップ設定]** ウィンドウを開きます。 **[追加]** をクリックします。
   
    ![[Add]\(追加\) を選択できる [M-Files Desktop Settings]\(M-Files デスクトップ設定\) を示すスクリーンショット。](./media/m-files-tutorial/settings.png)

1. **[Document Vault Connection Properties] \(資格情報コンテナーの接続プロパティのドキュメント化)** ウィンドウで、次の手順を実行します。
   
    ![説明されている値を入力できる [Document Vault Connection Properties]\(ドキュメント コンテナーの接続のプロパティ\) を示すスクリーンショット。](./media/m-files-tutorial/general.png)  

    [サーバー] セクションで、次のように値を入力します。  

    a. **[名前]** は「`<tenant-name>.cloudvault.m-files.com`」と入力します。 
 
    b. **[ポート番号]** は「**4466**」と入力します。 

    c. **[プロトコル]** は **[HTTPS]** を選択します。 

    d. **[認証]** フィールドで、 **[特定の Windows ユーザー]** を選択します。 署名の入力を求められます。 Azure AD の資格情報を入力します。 

    e. **[Vault on Server] \(サーバーの資格情報コンテナー)** は、サーバー上の対応する資格情報コンテナーを選択します。
 
    f. **[OK]** をクリックします。

### <a name="create-m-files-test-user"></a>M-Files テスト ユーザーの作成

このセクションの目的は、M-Files で Britta Simon というユーザーを作成することです。 M-Files にユーザーを追加するには、[M-Files サポート チーム](mailto:support@m-files.com)と連携します。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる M-Files のサインオン URL にリダイレクトされます。 

* M-Files のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [M-Files] タイルをクリックすると、M-Files のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

M-Files を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
