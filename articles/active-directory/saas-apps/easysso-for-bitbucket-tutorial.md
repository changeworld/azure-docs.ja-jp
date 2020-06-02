---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と EasySSO for BitBucket の統合 | Microsoft Docs
description: Azure Active Directory と EasySSO for BitBucket の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ed96e97-b590-4dca-8d00-36288444c641
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5c89693c49e7a5f24308d82c7277ca0bc3a7ae2
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83852997"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と EasySSO for BitBucket の統合

このチュートリアルでは、EasySSO for BitBucket と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と EasySSO for BitBucket を統合すると、次のことができます。

* EasySSO for BitBucket にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して EasySSO for BitBucket に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* EasySSO for BitBucket でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* EasySSO for BitBucket では、**SP および IDP** Initiated SSO がサポートされます
* EasySSO for BitBucket では、**Just-In-Time** ユーザー プロビジョニングがサポートされます
* EasySSO for BitBucket を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-easysso-for-bitbucket-from-the-gallery"></a>ギャラリーからの EasySSO for BitBucket の追加

Azure AD への EasySSO for BitBucket の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に EasySSO for BitBucket を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**EasySSO for BitBucket**」と入力します。
1. 結果のパネルから **[EasySSO for BitBucket]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-bitbucket"></a>EasySSO for BitBucket の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、EasySSO for BitBucket に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと EasySSO for BitBucket の関連ユーザーとの間にリンク関係を確立する必要があります。

EasySSO for BitBucket で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[EasySSO for BitBucket SSO の構成](#configure-easysso-for-bitbucket-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[EasySSO for BitBucket テスト ユーザーの作成](#create-easysso-for-bitbucket-test-user)** - EasySSO for BitBucket で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **EasySSO for BitBucket** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<server-base-url>/plugins/servlet/easysso/saml` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<server-base-url>/plugins/servlet/easysso/saml` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<server-base-url>/` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 この値を取得するには、[EasySSO for BitBucket クライアント サポート チーム](mailto:support@techtime.co.nz)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. EasySSO for BitBucket アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、EasySSO for BitBucket アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 |  ソース属性|
    | --------------- | --------- |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.42 | User.givenname |
    | urn:oid:2.5.4.4 | User.surname |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname|
    | urn:oid:0.9.2342.19200300.100.1.3 | user.userprincipalname |
    | | |

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

このセクションでは、EasySSO for BitBucket へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[EasySSO for BitBucket]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-easysso-for-bitbucket-sso"></a>EasySSO for BitBucket SSO の構成

1. 管理者権限で EasySSO for BitBucket インスタンスにサインインし、 **[Manage Apps]\(アプリの管理\)** セクションに移動します。

    ![[Manage Apps]\(アプリの管理\)](./media/easysso-for-bitbucket-tutorial/jira-admin-1.png)

1. **[EasySSO]** をクリックします。

    ![[EasySSO]](./media/easysso-for-bitbucket-tutorial/jira-admin-2.png)

1. **[SAML]** オプションを選択します。 これにより、SAML の構成セクションが表示されます。

    ![SAML](./media/easysso-for-bitbucket-tutorial/jira-admin-3.png)

1. 上部の **[Certificates]\(証明書\)** タブを選択すると、次の画面が表示されるので、前の **Azure AD SSO** の構成手順で保存した**証明書 (Base64)** または**メタデータ ファイル**を見つけます。 続行する方法として、次の選択肢があります。

    ![メタデータ URL](./media/easysso-for-bitbucket-tutorial/jira-admin-4.png)

    a. 自分のコンピューター上のローカル ファイルとしてダウンロードした、アプリのフェデレーション **メタデータ ファイル**を使用します。 **[Upload]\(アップロード\)** をクリックし、ご使用のオペレーティング システムに固有のファイルのアップロード ダイアログに従います

    **OR**

    b. (任意のプレーンテキスト エディターで) アプリのフェデレーション **メタデータ ファイル**を開き、ファイルの内容を確認してクリップボードにコピーします。 **[Input]\(入力\)** オプションを選択し、クリップボードの内容をテキスト フィールドに貼り付けます。

    **OR**

    c. すべて手動で構成します。 (任意のプレーンテキスト エディターで) アプリのフェデレーション**証明書 (Base64)** を開き、ファイルの内容を確認してクリップボードにコピーします。 **[IdP Token Signing Certificates]\(IdP トークン署名証明書\)** テキスト フィールドに貼り付けます。 次に、 **[General]\(全般\)** タブに移動し、 **[POST Binding URL]\(POST バインディング URL\)** および **[Entity ID]\(エンティティ ID\)** フィールドに、前に保存した **[ログイン URL]** と **[Azure AD 識別子]** の値をそれぞれ入力します。

1. ページの下部にある **[Save]\(保存\)** ボタンをクリックします。 メタデータ ファイルまたは証明書ファイルの内容が構成フィールドで解析されていることを確認できます。 これで、EasySSO for BitBucket の構成は完了しました。

1. 最適なテスト エクスペリエンスを得るには、 **[Look & Feel]\(外観\)** タブに移動し、 **[SAML Login Button]\(SAML ログイン ボタン\)** オプションをオンにします。 これにより、Jira ログイン画面の独立したボタンが有効になり、Azure AD SAML 統合をエンド ツー エンドでテストできるようになります。 このボタンをオンのままにすることで、運用モードでの配置、色、および翻訳を構成することもできます。

    ![[Look & Feel]\(外観\)](./media/easysso-for-bitbucket-tutorial/jira-admin-5.png)

    > [!NOTE]
    > 問題が発生した場合は、[EasySSO サポート チーム](mailto:support@techtime.co.nz)にお問い合わせください。

### <a name="create-easysso-for-bitbucket-test-user"></a>EasySSO for BitBucket のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを EasySSO for BitBucket に作成します。 EasySSO for BitBucket では Just-In-Time ユーザー プロビジョニングがサポートされており、既定では**無効**になっています。 ユーザー プロビジョニングを有効にするには、EasySSO プラグイン構成の [General]\(全般\) セクションで、 **[Create user on successful login]\(ログイン成功時にユーザーを作成する\)** オプションを明示的にオンにする必要があります。 EasySSO for BitBucket にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

ただし、ユーザーが初めてログインした時点での自動ユーザー プロビジョニングを有効にしない場合は、EasySSO for BitBucket インスタンスが使用するバックエンド ユーザー ディレクトリ (LDAP や Atlassian Crowd など) にユーザーが存在する必要があります。

![ユーザー プロビジョニング](./media/easysso-for-bitbucket-tutorial/jira-admin-6.png)

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [EasySSO for BitBucket] タイルをクリックすると、SSO を設定した EasySSO for BitBucket に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で EasySSO for BitBucket を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって EasySSO for BitBucket を保護する方法](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
