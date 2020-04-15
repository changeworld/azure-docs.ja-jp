---
title: チュートリアル:Azure Active Directory と SAP Qualtrics の統合 | Microsoft Docs
description: Azure Active Directory と SAP Qualtrics の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4df889ab-2685-4d15-a163-1ba26567eeda
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: d7a35f9f366f45a111aa7111501c9261ecbfa87e
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667516"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と SAP Qualtrics の統合

このチュートリアルでは、SAP Qualtrics と Azure Active Directory (Azure AD) を統合する方法について説明します。 SAP Qualtrics と Azure AD を統合すると、次のことができます。

* SAP Qualtrics にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SAP Qualtrics に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SAP Qualtrics でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* SAP Qualtrics では、**SP** Initiated SSO と **IDP** Initiated SSO がサポートされます
* SAP Qualtrics では、**Just In Time** ユーザー プロビジョニングがサポートされます
* SAP Qualtrics を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-sap-qualtrics-from-the-gallery"></a>ギャラリーからの SAP Qualtrics の追加

Azure AD への SAP Qualtrics の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に SAP Qualtrics を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**SAP Qualtrics**」と入力します。
1. 結果のパネルから **[SAP Qualtrics]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>SAP Qualtrics の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、SAP Qualtrics に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと SAP Qualtrics の関連ユーザーとの間にリンク関係を確立する必要があります。

SAP Qualtrics で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[SAP Qualtrics の SSO の構成](#configure-sap-qualtrics-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[SAP Qualtrics のテスト ユーザーの作成](#create-sap-qualtrics-test-user)** - SAP Qualtrics で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **SAP Qualtrics** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[SAML によるシングル サインオンのセットアップ]** ページで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。
    
    a. **[識別子]** ボックスに、次の形式で URL を入力します。

    `https://< DATACENTER >.qualtrics.com`
   
    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. **[リレー状態]** ボックスに、次のパターンで URL を入力します:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、応答 URL、リレー状態でこれらの値を更新します。 これらの値を取得するには、[Qualtrics クライアント サポート チーム](https://www.qualtrics.com/support/)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[Set up single sign-on with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

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

このセクションでは、SAP Qualtrics へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SAP Qualtrics]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-sap-qualtrics-sso"></a>SAP Qualtrics の SSO の構成

**SAP Qualtrics** 側でシングル サインオンを構成するには、Azure portal からコピーした**アプリのフェデレーション メタデータ URL** を [SAP Qualtrics サポート チーム](https://www.qualtrics.com/support/)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-sap-qualtrics-test-user"></a>SAP Qualtrics のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを SAP Qualtrics に作成します。 SAP Qualtrics では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 SAP Qualtrics にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [SAP Qualtrics] タイルをクリックすると、SSO を設定した SAP Qualtrics に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で SAP Qualtrics を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって SAP Qualtrics を保護する方法](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

