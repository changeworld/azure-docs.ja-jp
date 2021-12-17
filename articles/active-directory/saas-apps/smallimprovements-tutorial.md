---
title: 'チュートリアル: Azure AD SSO と Small Improvements の統合'
description: Azure Active Directory と Small Improvements の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/27/2021
ms.author: jeedes
ms.openlocfilehash: 0a9fd3388589370f75179ea95d62b3d646876dd2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341200"
---
# <a name="tutorial-azure-ad-sso-integration-with-small-improvements"></a>チュートリアル: Azure AD SSO と Small Improvements の統合

このチュートリアルでは、Small Improvements と Azure Active Directory (Azure AD) を統合する方法について説明します。 Small Improvements と Azure AD を統合すると、次のことが可能になります。

* Small Improvements にアクセスできる Azure AD ユーザーを制御します。
* ユーザーが自分の Azure AD アカウントを使用して Small Improvements に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Small Improvements でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Small Improvements では、**SP** Initiated SSO がサポートされます。

## <a name="add-small-improvements-from-the-gallery"></a>ギャラリーから Small Improvements を追加する

Azure AD への Small Improvements の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Small Improvements を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Small Improvements**」と入力します。
1. 結果のパネルから **[Small Improvements]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-small-improvements"></a>Small Improvements での Azure AD SSO を構成してテストする

**B.Simon** という名前のテスト ユーザーを使用して、Small Improvements での Azure AD SSO を構成してテストします。 SSO が機能するには、Azure AD ユーザーと Small Improvements の関連するユーザーの間のリンク関係を確立する必要があります。

Small Improvements での Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Small Improvements の SSO の構成](#configure-small-improvements-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Small Improvements のテスト ユーザーの作成](#create-small-improvements-test-user)** - Small Improvements で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Small Improvements** アプリケーション統合ページで、 **[管理]** セクションを見つけ、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.small-improvements.com`

    b. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際の識別子とサインオン URL で更新してください。 これらの値を取得するには、[Small Improvements クライアント サポート チーム](mailto:support@small-improvements.com)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Small Improvements のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Small Improvements へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Small Improvements]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-small-improvements-sso"></a>Small Improvements の SSO を構成する

1. 別の Web ブラウザー ウィンドウで、管理者として Small Improvements 企業サイトにサインオンします。

1. メイン ダッシュボード ページで、左側の **[管理]** ボタンをクリックします。

    ![[管理] ボタンが選択された画面のスクリーンショット。](./media/smallimprovements-tutorial/admin.png) 

1. **[統合]** セクションで、**[SAML SSO]** ボタンをクリックします。

    ![[統合] で SAML S S O アイコンが選択された画面のスクリーンショット。](./media/smallimprovements-tutorial/menu.png) 

1. [SSO Setup] ページで、次の手順に従います。

    ![[S S O Setup ]\(S S O の設定\) ページを示すスクリーンショット。ここで、説明されている値を入力できます。](./media/smallimprovements-tutorial/certificate.png)  

    a. **[HTTP Endpoint]\(HTTP エンドポイント\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. ダウンロードした証明書をメモ帳で開き、その内容をコピーして、 **[x509 証明書]** ボックスに貼り付けます。 

    c. ユーザーが SSO およびログイン フォーム認証オプションを使用できるようにするには、 **[Enable access via login/password too (ログイン/パスワードによるアクセスも有効にする)]** オプションをオンにします。  

    d. **[SAML プロンプト]** ボックスに、SSO ログイン ボタンの名前として適切な値を入力します。  

    e. **[保存]** をクリックします。

### <a name="create-small-improvements-test-user"></a>Small Improvements のテスト ユーザーの作成

Azure AD ユーザーが Small Improvements にログインできるようにするには、そのユーザーを Small Improvements にプロビジョニングする必要があります。 Small Improvements の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として Small Improvements 企業サイトにサインオンします。

1. ホーム ページの左側のメニューに移動し、 **[管理]** をクリックします。

1. [ユーザー管理] セクションで、 **[ユーザー ディレクトリ]** ボタンをクリックします。

    ![[管理の概要] の [ユーザー ディレクトリ] が選択された画面のスクリーンショット。](./media/smallimprovements-tutorial/user.png) 

1. **[ユーザーの追加]** をクリックします。

    ![[ユーザーの追加] ボタンのスクリーンショット。](./media/smallimprovements-tutorial/add-user.png) 

1. **[ユーザーの追加]** ダイアログで、次の手順を実行します。 

    ![[ユーザーの追加] ダイアログ ボックスを示すスクリーンショット。ここで、説明されている値を入力できます。](./media/smallimprovements-tutorial/create-user.png)

    a. 「**Britta**」のように、ユーザーの **[名]** を入力します。

    b. 「**Simon**」のように、ユーザーの **[姓]** を入力します。

    c. **brittasimon@contoso.com** のように、ユーザーの **[メール]** を入力します。

    d. **[通知電子メールを送信する]** ボックスに、個人のメッセージを入力することもできます。 通知を送信しない場合は、このチェックボックスをオフにします。

    e. **[ユーザーの作成]** をクリックします。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Small Improvements のサインオン URL にリダイレクトされます。 

* Small Improvements のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Small Improvements] タイルをクリックすると、Small Improvements のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Small Improvements を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
