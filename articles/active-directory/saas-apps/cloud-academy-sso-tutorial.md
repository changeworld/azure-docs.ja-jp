---
title: チュートリアル:Azure Active Directory SSO と Cloud Academy - SSO の統合
description: このチュートリアルでは、Azure Active Directory と Cloud Academy - SSO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.openlocfilehash: 822e28402d0b7829b835ad03a3b3cf7d05c3d343
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181001"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-cloud-academy---sso"></a>チュートリアル:Azure Active Directory シングル サインオンと Cloud Academy - SSO の統合

このチュートリアルでは、Cloud Academy - SSO と Azure Active Directory (Azure AD) を統合する方法について説明します。 Cloud Academy - SSO と Azure AD を統合すると、次のことができます。

* Azure AD を使用して、Cloud Academy - SSO にアクセスできるユーザーを制御します。
* ユーザーが自分の Azure AD アカウントを使用して Cloud Academy - SSO に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[シングル サインオン (SSO) とは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Cloud Academy - SSO サブスクリプション。

## <a name="tutorial-description"></a>チュートリアルの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

Cloud Academy - SSO によって、SP によって開始される SSO がサポートされます

Cloud Academy - SSO を構成したら、ご自分の組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="add-cloud-academy---sso-from-the-gallery"></a>ギャラリーからの Cloud Academy - SSO の追加

Azure AD への Cloud Academy - SSO の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Cloud Academy - SSO を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左ウィンドウで、 **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Cloud Academy - SSO**」と入力します。
1. 結果のパネルから **[Cloud Academy - SSO]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Cloud Academy - SSO の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Cloud Academy - SSO に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Cloud Academy - SSO の関連ユーザーとの間にリンク関係を確立する必要があります。

Cloud Academy - SSO で Azure AD SSO を構成してテストするには、次の大まかな手順に従います。

1. **[Azure AD SSO を構成](#configure-azure-ad-sso)** して、ユーザーがこの機能を使用できるようにします。
    1. Azure AD のシングル サインオンをテストするための **[Azure AD テスト ユーザーを作成](#create-an-azure-ad-test-user)** します。
    1. **[テスト ユーザーにアクセス権を付与](#grant-access-to-the-test-user)** して、Azure AD シングル サインオンをユーザーが使用できるようにします。
1. アプリケーション側で **[Cloud Academy - SSO のシングル サインオンを構成します](#configure-single-sign-on-for-cloud-academy)** 。
    1. Azure AD のユーザーに対応するユーザーとして、 **[Cloud Academy - SSO テスト ユーザーを作成します](#create-a-cloud-academy-test-user)** 。
1. **[SSO をテスト](#test-sso)** して、構成が正しく機能することを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Cloud Academy - SSO** アプリケーション統合ページの **[管理]** セクションで、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の鉛筆ボタンを選択して設定を編集します。

   ![基本的な SAML 構成を編集するための鉛筆ボタンを示すスクリーンショット。](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションの **[サインオン URL]** ボックスに「`https://cloudacademy.com/login/enterprise/`」と入力します。

1. **[SAML でシングル サインオンを設定します]** ページの **[SAML 署名証明書]** セクションで、コピー ボタンを選択して **[アプリのフェデレーション メタデータ URL]** をコピーします。 URL を保存します。

    ![アプリのフェデレーション メタデータ URL のコピー ボタンを示すスクリーンショット。](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左ペインで、 **[Azure Active Directory]** を選択します。 **[ユーザー]** を選択し、 **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、次の手順を実行します。
   1. **[名前]** ボックスに「**B.Simon**」と入力します。  
   1. **[ユーザー名]** ボックスに「\<username>@\<companydomain>.\<extension>」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** をオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="grant-access-to-the-test-user"></a>テスト ユーザーへのアクセス権の付与

このセクションでは、B. Simon に Cloud Academy - SSO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Cloud Academy - SSO]** を選択します。
1. アプリの概要ページの **[管理]** セクションで、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] オプションを示すスクリーンショット。](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] ボタンを示すスクリーンショット。](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスで、**ユーザー** の一覧で **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 画面の下部にある **[選択]** ボタンをクリックします。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-single-sign-on-for-cloud-academy"></a>Cloud Academy のシングル サインオンを構成する

1. 別のブラウザー ウィンドウで、Cloud Academy - SSO 企業サイトに管理者としてサインインします。

1. 会社名を選択し、表示されたメニューで **[Settings & Integrations]\(設定と統合\)** を選択します。

    ![[Settings & Integrations]\(設定と統合\) オプションを示すスクリーンショット。](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. **[Settings & Integrations]\(設定と統合\)** ページの **[Integrations]\(統合\)** タブで、 **[SSO]** カードを選択します。

    ![[Integrations]\(統合\) タブの [SSO] カードを示すスクリーンショット。](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. このページで、次の手順を行います。

    ![[統合] > [SSO] ページを示すスクリーンショット。](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. **[Entity ID URL]\(エンティティ ID URL\)** ボックスに、Azure portal からコピーしたエンティティ ID 値を入力します。

    b. **[SSO URL]** ボックスに、Azure portal からコピーしたログイン URL の値を貼り付けます。

    c. Azure portal からダウンロードした Base64 証明書をメモ帳で開きます。 その内容を **[Certificate]\(証明書\)** ボックスに貼り付けます。

    d. **[Name ID Format]\(名前 ID の形式\)** ボックスは、既定値の `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` のままにします。

1. **[保存]** を選択します。

    > [!NOTE]
    > Cloud Academy-SSO の構成方法の詳細については、「[Setting Up Single Sign-On](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On)」 (シングル サインオンを設定する) を参照してください。

### <a name="create-a-cloud-academy-test-user"></a>Cloud Academy テスト ユーザーを作成する

1. Cloud Academy - SSO にサインインします。

1. 会社の名前を選択し、表示されるメニューで **[メンバー]** を選択します。

    ![[メンバー] オプションを示すスクリーンショット。](./media/cloud-academy-sso-tutorial/create-user.PNG)

1. **[Invite Members]\(メンバーの招待\)** を選択し、 **[Invite a Single Member]\(メンバーを 1 名招待する\)** を選択します。

    ![[Invite a Single Member]\(メンバーを 1 名招待する\) オプションを示すスクリーンショット。](./media/cloud-academy-sso-tutorial/create-user-1.PNG)

1. 必須フィールドに値を入力してから、 **[Invite]\(招待\)** を選択します:

    ![[Invite a Member]\(メンバーの招待\) ダイアログ ボックスを示すスクリーンショット。](./media/cloud-academy-sso-tutorial/create-user-2.PNG)

## <a name="test-sso"></a>SSO のテスト 

アクセス パネルを使用して Azure AD SSO の構成をテストします。

アクセス パネルで [Cloud Academy - SSO] タイルを選択すると、SSO を設定した Cloud Academy - SSO インスタンスに自動的にサインインします。 詳細については、「[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアル](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Cloud Academy - SSO を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御を使用して Cloud Academy - SSO を保護する方法](/cloud-app-security/proxy-intro-aad)