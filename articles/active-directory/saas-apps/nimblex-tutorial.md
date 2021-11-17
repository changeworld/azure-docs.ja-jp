---
title: 'チュートリアル: Azure Active Directory と Nimblex の統合 | Microsoft Docs'
description: Azure Active Directory と Nimblex の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2021
ms.author: jeedes
ms.openlocfilehash: b0cb2182e1733a9607d5f967e9c11eb9e4ea7f43
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132342150"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>チュートリアル: Azure Active Directory と Nimblex の統合

このチュートリアルでは、Nimblex と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Nimblex を統合すると、次のことができます。

* Nimblex にアクセスできる Azure AD ユーザーを制御します。
* ユーザーが自分の Azure AD アカウントを使用して Nimblex に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Nimblex でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Nimblex では、**SP** Initiated SSO がサポートされます。

* Nimblex では、**Just In Time** ユーザー プロビジョニングがサポートされます。

## <a name="add-nimblex-from-the-gallery"></a>ギャラリーからの Nimblex の追加

Azure AD への Nimblex の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Nimblex を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Nimblex**」と入力します。
1. 結果のパネルから **[Nimblex]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-nimblex"></a>Nimblex の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Nimblex に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Nimblex の関連ユーザーとの間にリンク関係を確立する必要があります。

Nimblex に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Nimblex SSO の構成](#configure-nimblex-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Nimblex のテスト ユーザーの作成](#create-nimblex-test-user)** - Nimblex で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Nimblex** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、`https://<YOUR_APPLICATION_PATH>/Login.aspx` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://<YOUR_APPLICATION_PATH>/` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<path-to-application>/SamlReply.aspx` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 この値を取得するには、[Nimblex クライアント サポート チーム](mailto:support@ebms.com.au)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで **[ダウンロード]** をクリックして、要件に適した特定のオプションの **証明書 (Base64)** をダウンロードし、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Nimblex のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に NAVEX One へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[NAVEX One]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-nimblex-sso"></a>Nimblex SSO の構成

1. 別の Web ブラウザー ウィンドウで、Nimblex にセキュリティ管理者としてサインインします。

2. ページの右上の **設定** ロゴをクリックします。

    ![スクリーンショットは、設定アイコンを示しています。](./media/nimblex-tutorial/settings.png)

3. **コントロール パネル** ページの **[セキュリティ]** の下で、 **[シングル サインオン]** をクリックします。

    ![スクリーンショットは、[Security]\(セキュリティ\) メニューの [Single Sign-On]\(シングル サインオン\) が選択されていることを示しています。](./media/nimblex-tutorial/security.png)

4. **[シングル サインオンの管理]** ページで、インスタンス名を選択して **[編集]** をクリックします。

    ![スクリーンショットは、[Single Sign-On]\(シングル サインオン\) を示しています。ここでは、[Edit]\(編集\) を選択できます。](./media/nimblex-tutorial/edit-tab.png)

5. **[SSO プロバイダーの編集]** ページで、次の手順を行います。

    ![スクリーンショットは、説明した値を入力できる [Edit SSO Provider]\(SSO プロバイダーの編集\) を示しています。](./media/nimblex-tutorial/certificate.png)

    a. **[説明]** テキストボックスに、インスタンス名を入力します。

    b. Azure portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[証明書]** ボックスに貼り付けます。

    c. **[Identity Provider Sso Target Url]\(ID プロバイダーの SSO ターゲット URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. **[保存]** をクリックします。

### <a name="create-nimblex-test-user"></a>Nimblex のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Nimblex に作成します。 Nimblex では、Just-In-Time ユーザー プロビジョニングがサポートされます。この設定は既定で有効です。 このセクションでは、ユーザー側で必要な操作はありません。 Nimblex にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Nimblex クライアント サポート チーム](mailto:support@ebms.com.au)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Nimblex のサインオン URL にリダイレクトされます。 

* Nimblex のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Nimblex] タイルをクリックすると、Nimblex のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Nimblex を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
