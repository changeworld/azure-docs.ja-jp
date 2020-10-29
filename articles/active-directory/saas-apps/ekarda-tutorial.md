---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と ekarda の統合 | Microsoft Docs
description: Azure Active Directory と ekarda の間でシングル サインオンを構成する方法を説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2020
ms.author: jeedes
ms.openlocfilehash: 7bb74732074482c12d3bc760e259bb014ccf006f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2020
ms.locfileid: "92454190"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と ekarda の統合

このチュートリアルでは、ekarda と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と ekarda を統合すると、次のことができます。

* ekarda にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して ekarda に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[シングル サインオン (SSO) とは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な ekarda サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* ekarda によって、SP Initiated SSO と IDP-initiated SSO がサポートされます。
* ekarda によって、Just-In-Time ユーザー プロビジョニングがサポートされます。
* ekarda を構成した後、セッション制御を適用できます。 この予防措置により、組織の機密データを流出と侵入からリアルタイムで保護することができます。 セッション制御は、アプリの条件付きアクセス制御を拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="add-ekarda-from-the-gallery"></a>ギャラリーから ekarda を追加する

Azure AD への ekarda の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に ekarda を追加します。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。

1. 左側のペインで、 **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「 **ekarda** 」と入力します。
1. 結果パネルから **[ekarda]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-ekarda"></a>ekarda の Azure AD シングル サインオンの構成とテスト

**B.Simon** という名前のテスト ユーザーを使用して、ekarda に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと ekarda の関連ユーザーとの間に、リンクされた関係を確立する必要があります。

ekarda に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、ユーザーがこの機能を使用できるようにします。

    1. [Azure AD のテスト ユーザーを作成](#create-an-azure-ad-test-user)して、B.Simon を使って Azure AD のシングル サインオンをテストします。
    1. [Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)て、B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. [ekarda SSO を構成](#configure-ekarda-sso)して、アプリケーション側でシングル サインオン設定を構成します。
    * [ekarda テスト ユーザーを作成](#create-an-ekarda-test-user)し、ekarda で B.Simon に対応するユーザーを作成し、そのユーザーの Azure AD 表現にリンクします。
1. [SSO をテスト](#test-sso)して、構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

Azure portal で次の手順に従って、Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. **ekarda** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンの設定]** ページで、鉛筆アイコンを選択して **[基本的な SAML 構成]** の設定を編集します。

   ![鉛筆アイコンが強調表示された [SAML によるシングル サインオンの設定] ページのスクリーンショット。](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、 **サービス プロバイダーのメタデータ ファイル** が表示される場合は、次の手順を行います。
    1. **[メタデータ ファイルをアップロードする]** を選択します。
    1. フォルダー アイコンを選択してメタデータ ファイルを選択し、 **[アップロード]** を選択します。
    1. メタデータ ファイルが正常にアップロードされると、 **[識別子]** と **[応答 URL]** の値が、ekarda セクションのテキスト ボックスに自動的に設定されます。

    > [!Note]
    > **[識別子]** と **[応答 URL]** の値が自動的に表示されない場合は、要件に応じて手動で値を入力してください。

1. **[基本的な SAML 構成]** セクションに **サービス プロバイダーのメタデータ ファイル** が表示されず、アプリケーションを IDP-initiated モードで構成する場合は、次のフィールドの値を入力します。

    1. **[識別子]** テキスト ボックスに、`https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`のパターンに従って URL を入力します。
    1. **[応答 URL]** テキスト ボックスに、`https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`のパターンに従って URL を入力します。

1. アプリケーションを SP-initiated モードで構成する場合は、 **[追加の URL を設定します]** を選択して、次の手順を行います。

    * **[サインオン URL]** テキスト ボックスに、`https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`のパターンに従って URL を入力します。

    > [!NOTE]
    > 前の 2 つの手順の値は、実際のものではありません。 実際の識別子、応答 URL、サインオン URL の値でこれらの値を更新します。 この値を取得するには、[ekarda クライアント サポート チーム](mailto:contact@ekarda.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML によるシングル サインオンの設定]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** を選択して **証明書 (Base64)** をコンピューターに保存します。

    ![Base64 証明書のダウンロード リンクが強調表示された、[SAML によるシングル サインオンの設定] ページの [SAML 署名証明書] セクションのスクリーンショット。](common/certificatebase64.png)

1. **[ekarda の設定]** セクションで、要件に基づいて適切な URL をコピーします。

    ![URL コピー リンクが強調表示された、[SAML によるシングル サインオンの設定] ページの [SAML 署名証明書] セクションのスクリーンショット。](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal を使用して、B.Simon という名前のテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。

1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、`B.Simon@contoso.com` と入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示される値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に ekarda へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[ekarda]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] が強調表示された [管理] セクションのスクリーンショット。](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] が強調表示された [ユーザーとグループ] セクションのスクリーンショット。](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[B.Simon]** を選択します。 次に、画面の下部にある **[選択]** を選択します。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** を選択します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-ekarda-sso"></a>ekarda SSO の構成

1. 別の Web ブラウザー ウィンドウで、ekarda 企業サイトに管理者としてサインインします。
1. **[管理者]**  >  **[マイ アカウント]** の順に選択します。

    ![[管理者] メニューで [マイ アカウント] が強調表示された ekarda サイト UI のスクリーンショット。](./media/ekarda-tutorial/ekarda.png)

1. ページの下部にある **[SAML 設定]** を見つけます。 このセクションで SAML 統合を構成します。
1. **[SAML 設定]** セクションで、次の手順を行います。

    ![SAML 構成フィールドが強調表示された、ekarda の [SAML 設定] ページのスクリーンショット。](./media/ekarda-tutorial/ekarda1.png)

    1. **[サービス プロバイダーのメタデータ]** リンクを選択し、それをファイルとしてコンピューターに保存します。
    1. **[SAML を有効にする]** チェック ボックスをオンにします。
    1. **[IDP エンティティ ID]** テキスト ボックスに、先ほど Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。
    1. **[IDP ログイン URL]** テキスト ボックスに、先ほど Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。
    1. **[IDP ログアウト URL]** テキスト ボックスに、先ほど Azure portal からコピーした **[ログアウト URL]** の値を貼り付けます。
    1. メモ帳を使用して、Azure portal からダウンロードした **証明書 (Base64)** ファイルを開きます。 その内容を **[IDP x509 証明書]** テキスト ボックスに貼り付けます。
    1. **[オプション]** セクションの **[SLO を有効にする]** チェック ボックスをオンにします。
    1. **[Update]\(更新\)** を選択します。

### <a name="create-an-ekarda-test-user"></a>ekarda テスト ユーザーの作成

このセクションでは、B. Simon という名前のユーザーを ekarda に作成します。 ekarda によって、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションで実行する操作はありません。 ekarda に B. Simon という名前のユーザーがまだ存在しない場合は、認証後に新しく作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、マイ アプリ ポータルを使用して自分の Azure AD のシングル サインオン構成をテストします。

マイ アプリ ポータルで [ekarda] タイルをクリックすると、SSO を設定した ekarda に自動的にサインインします。 マイ アプリ ポータルの詳細については、[マイ アプリ ポータルの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory の統合に関するチュートリアルの一覧](./tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)
* [Azure AD で ekarda を試す](https://aad.portal.azure.com/)
* [ekarda のエンタープライズ eCard ソリューション](https://ekarda.com/ecards-ecards-with-logo-for-business-corporate-enterprise)を使用すると、会社のロゴが入った eCard を顧客や同僚に送信できるように、任意の数のスタッフをプロビジョニングできます。 SSO ソリューションとして ekarda をプロビジョニングする方法の詳細については、[こちら](https://support.ekarda.com/#SSO-Implementation)を参照してください。
* [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)
* [高度な可視性と制御によって ekarda を保護する方法](/cloud-app-security/proxy-intro-aad)