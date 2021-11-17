---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Clebex の統合 | Microsoft Docs'
description: Azure Active Directory と Clebex の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2021
ms.author: jeedes
ms.openlocfilehash: 82fd60f6d438703ad2c782003d06bbf0f8290394
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307709"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-clebex"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Clebex の統合

このチュートリアルでは、Clebex と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Clebex を統合すると、次のことが可能になります。

* Clebex にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Clebex に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Clebex でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Clebex では、**SP** Initiated SSO がサポートされます。

* Clebex では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

* Clebex では、[自動化されたユーザー プロビジョニング](clebex-provisioning-tutorial.md)がサポートされます。


## <a name="add-clebex-from-the-gallery"></a>ギャラリーから Clebex を追加する

Azure AD への Clebex の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Clebex を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Clebex**」と入力します。
1. 結果のパネルから **[Clebex]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-clebex"></a>Clebex の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Clebex に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Clebex の関連ユーザーとの間にリンク関係を確立する必要があります。

Clebex に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Clebex の SSO の構成](#configure-clebex-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Clebex のテスト ユーザーの作成](#create-clebex-test-user)** - Clebex で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Clebex** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<CustomerName>.domain.extention/<ID>` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<CustomerName>.domain.extention/<ID>` のパターンを使用して URL を入力します

    c. **[サインオン URL]** ボックスに、`https://<CustomerName>.domain.extention/<ID>` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 この値を取得するには、[Clebex クライアント サポート チーム](mailto:support@clebex.net)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Clebex のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Clebex へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Clebex]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-clebex-sso"></a>Clebex の SSO の構成

1. Clebex の Web サイトに管理者としてログインします。

1. [COMPANY ADMIN]\(会社の管理者\) -> **[Connectors]\(コネクタ\)**  ->  **[Single Sign On (SSO)]\(シングル サインオン (SSO)\)** に移動し、 **[select]\(選択\)** をクリックします。

    ![コネクタの種類の選択を示すスクリーンショット。](./media/clebex-tutorial/single-sign-on.png)

1. [CONNECTORS]\(コネクタ\) で、 **[Azure SSO]** を選択し、[EDIT-CONNECTOR]\(編集 - コネクタ\) セクションで次の手順を実行します。

    ![コネクタと構成の選択を示すスクリーンショット。](./media/clebex-tutorial/azure-sso.png)

    a. **[IDENTIFIER]\(識別子\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** テキスト ボックスに貼り付けます。

    b. **[REPLY URL]\(応答 URL\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** テキスト ボックスにこの値を貼り付けます。

    c. **[ENTITY ID]\(エンティティ ID\)** テキスト ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    d. **[SAML]** テキスト ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    e. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[CERTIFICATE]\(証明書\)** テキスト ボックスに貼り付けます。

    f. **[SAVE-CHANGES]\(変更の保存\)** をクリックします。

### <a name="create-clebex-test-user"></a>Clebex のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Clebex に作成します。 Clebex では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Clebex にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

Clebex では、自動ユーザー プロビジョニングもサポートされます。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](./clebex-provisioning-tutorial.md)をご覧ください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Clebex のサインオン URL にリダイレクトされます。 

* Clebex のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Clebex] タイルをクリックすると、Clebex のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Clebex を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
