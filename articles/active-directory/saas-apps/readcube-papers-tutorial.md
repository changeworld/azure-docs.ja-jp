---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と ReadCube Papers の統合 | Microsoft Docs'
description: Azure Active Directory と ReadCube Papers の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2021
ms.author: jeedes
ms.openlocfilehash: 173a383d4daf9bed1a6c3cd76b493c0410504f01
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341637"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-readcube-papers"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と ReadCube Papers の統合

このチュートリアルでは、ReadCube Papers と Azure Active Directory (Azure AD) を統合する方法について説明します。 ReadCube Papers を Microsoft Azure Active Directory と統合すると、次のことが可能になります。

* ReadCube Papers にアクセスできるユーザーを Microsoft Azure Active Directory で制御します。
* ユーザーが自分の Microsoft Azure Active Directory アカウントを使用して ReadCube Papers に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* ReadCube Papers でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* ReadCube Papers では、**SP** Initiated SSO がサポートされます。
* ReadCube Papers では、**Just In Time** ユーザー プロビジョニングがサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-readcube-papers-from-the-gallery"></a>ギャラリーからの ReadCube Papers の追加

Microsoft Azure Active Directory への ReadCube Papers の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に ReadCube Papers を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**ReadCube Papers**」と入力します。
1. 結果パネルで **[ReadCube Papers]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-readcube-papers"></a>ReadCube Papers 用の Microsoft Azure Active Directory SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、ReadCube Papers と一緒に Microsoft Azure Active Directory SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと ReadCube Papers の関連ユーザーとの間にリンク関係を確立する必要があります。

Microsoft Azure Active Directory SSO を ReadCube Papers と一緒に構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[ReadCube Papers SSO の構成](#configure-readcube-papers-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[ReadCube Papers のテスト ユーザーの作成](#create-readcube-papers-test-user)** - ReadCube Papers に、Microsoft Azure Active Directory の B.Simon を表すユーザーにリンクされた対応ユーザーを作成します。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **ReadCube Papers アプリケーション統合** ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。
    1. **[Reply URL (ACS URL)]\(応答 URL (ACS URL)\)** テキスト ボックスに、URL として「`https://connect.liblynx.com/saml/module.php/saml/sp/saml2-acs.php/dsrsi`」と入力します。
    2. **[サインオン URL]** ボックスに、URL として「`https://app.readcube.com`」と入力します。

        ![[SAML 構成] ウィンドウの設定例を示すスクリーンショット。](./media/readcube-papers-tutorial/configure-saml.png)

         
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

このセクションでは、B.Simon に ReadCube Papers へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[ReadCube Papers]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-readcube-papers-sso"></a>ReadCube Papers SSO の構成

**ReadCube Papers** 側でシングル サインオンを構成するには、 **[アプリのフェデレーション メタデータ URL]** を [ReadCube Papers サポート チーム](mailto:sso-support@readcube.com)に送る必要があります。 SAML SSO 接続が両方の側で正しく機能するように、この設定を変更します。

### <a name="create-readcube-papers-test-user"></a>ReadCube Papers のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを ReadCube Papers に作成します。 ReadCube Papers では、Just-In-Time ユーザー プロビジョニングがサポートされています。これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ReadCube Papers にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

> [!NOTE]
> テストする前に、[ReadCube Papers のサポートチーム](mailto:sso-support@readcube.com)に、SSO が ReadCube 側で設定されていることを確認してください。

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる ReadCube Papers のサインオン URL にリダイレクトされます。 

* ReadCube Papers のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリ ポータルで [ReadCube Papers] タイルをクリックすると、ReadCube Papers のサインオン URL にリダイレクトされます。 マイ アプリ ポータルの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

ReadCube Papers を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
