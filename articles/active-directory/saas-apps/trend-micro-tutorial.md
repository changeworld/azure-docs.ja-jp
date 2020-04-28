---
title: チュートリアル:Trend Micro Web Security (TMWS) と Azure Active Directory のシングル サインオン (SSO) 統合 | Microsoft Docs
description: Azure Active Directory と Trend Micro Web Security (TMWS) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 827285d3-8e65-43cd-8453-baeda32ef174
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f4dc7223d64fd299da70375329260f7b4f8b322
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083395"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-securitytmws"></a>チュートリアル:Trend Micro Web Security (TMWS) と Azure Active Directory のシングル サインオン (SSO) 統合

このチュートリアルでは、Azure Active Directory (Azure AD) とTrend Micro Web Security (TMWS) を統合する方法について説明します。 Azure AD と Trend Micro Web Security (TMWS) を統合すると、次のことができます。

* Trend Micro Web Security (TMWS) にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Trend Micro Web Security (TMWS) に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Trend Micro Web Security (TMWS) でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Trend Micro Web Security (TMWS) では、**SP** によって開始される SSO がサポートされます
* Trend Micro Web Security (TMWS) を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-trend-micro-web-securitytmws-from-the-gallery"></a>ギャラリーからの Trend Micro Web Security (TMWS) の追加

Azure AD への Trend Micro Web Security (TMWS) の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから Trend Micro Web Security (TMWS) を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Trend Micro Web Security(TMWS)** 」と入力します。
1. 結果パネルから **[Trend Micro Web Security (TMWS)]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-trend-micro-web-securitytmws"></a>Trend Micro Web Security (TMWS) の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Trend Micro Web Security (TMWS) に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Trend Micro Web Security (TMWS) の関連ユーザーの間で、リンク関係を確立する必要があります。

Trend Micro Web Security (TMWS) に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
    1. **[Azure AD でのユーザーとグループの同期設定の構成](#configure-user-and-group-synchronization-settings-in-azure-ad)** - Azure AD でユーザーとグループの同期設定を構成します
1. **[Trend Micro Web Security (TMWS) の SSO の構成](#configure-trend-micro-web-security-sso)** - アプリケーション側でシングル サインオン設定を構成します。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Trend Micro Web Security(TMWS)** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. **[応答 URL]** ボックスに、`https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp` という URL を入力します。

    > [!NOTE]
    > この識別子の値は実際のものではありません。 実際の識別子でこの値を更新します。 これらの値は、Azure AD の **[認証方法]** 画面の **[Service Provider Settings for the Azure Admin Portal]\(Azure 管理ポータルのサービス プロバイダー設定\)** 領域で **[管理]> [ディレクトリ サービス]** から取得できます。

1. Trend Micro Web Security (TMWS) アプリケーションでは、特定の形式の SAML アサーションが使用されるため、カスタム属性のマッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Trend Micro Web Security (TMWS) アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 | ソース属性|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Trend Micro Web Security(TMWS) のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Trend Micro Web Security (TMWS) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Trend Micro Web Security(TMWS)]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Azure AD でのユーザーとグループの同期設定の構成

1. 左側のナビゲーションで、 **[Azure Active Directory]** をクリックします。

1. **[管理]** で、 **[アプリの登録]** をクリックし、 **[すべてのアプリケーション]** 領域で新しいエンタープライズ アプリケーションをクリックします。

1. **[管理]** で、 **[証明書とシークレット]** をクリックします。

1. 表示される [クライアントシークレット] 領域で、 **[新しいクライアント シークレット]** をクリックします。

1. 表示される [クライアント シークレットの追加] 画面で、必要に応じて説明を追加し、このクライアント シークレットの有効期限を選択してから、 **[追加]** をクリックします。 新しく追加されたクライアント シークレットは、[クライアント シークレット] 領域に表示されます。

1. 値を記録します。 この情報は、後で TMWS に入力します。

1. **[管理]** の下にある **[API のアクセス許可]** をクリックします。 

1. 表示される [API のアクセス許可] 画面で、 **[アクセス許可の追加]** をクリックします。

1. 表示される [API アクセス許可の要求] 画面の [Microsoft API] タブで、 **[Microsoft Graph]** をクリックし、 **[アプリケーションの許可]** をクリックします。

1. 次のアクセス許可を見つけて追加します。 

    * Group.Read.All
    * User.Read.All

1. **[アクセス許可の追加]** をクリックします。 設定が正常に保存されたことを確認するメッセージが表示されます。 新しく追加されたアクセス許可が [API のアクセス許可] 画面に表示されます。

1. [同意する] 領域で、 **[<管理者アカウント> に管理者の同意を与えます (既定のディレクトリ)]** をクリックして、 **[はい]** を選択します。 要求されたアクセス許可に対する管理者の同意が正常に付与されたことを確認するメッセージが表示されます。

1. **[Overview]** をクリックします。 

1. 表示される右側のペインで、アプリケーション (クライアント) ID とディレクトリ (テナント) ID を記録します。 この情報は、後で TMWS に入力します。 また、 **[Azure Active Directory] > [管理]** の **[カスタム ドメイン名]** をクリックして、右側のペインでドメイン名を記録することもできます。

## <a name="configure-trend-micro-web-security-sso"></a>Trend Micro Web Security SSO の構成

1. TMWS 管理コンソールにサインインし、 **[Administration]\(管理\)**  >  **[USERS & AUTHENTICATION]\(ユーザーと認証\)**  >  **[Directory Services]\(ディレクトリ サービス\)** に移動します。

1. 画面の上部の領域のここをクリックします。

1. 表示される [Authentication Method]\(認証方法\) 画面で、 **[Azure AD]** をクリックします。

1. **[On]\(オン\)** または **[Off]\(オフ\)** をクリックして、組織の AD ユーザーのデータが TMWS に同期されていない場合にユーザーが TMWS を通じて Web サイトにアクセスできるようにするかどうかを決定します。

    > [!NOTE]
    > Azure AD から同期されていないユーザーは、既知の TMWS ゲートウェイまたは組織の専用ポートを介してのみ認証できます。

1. **[Identity Provider Settings]\(ID プロバイダー設定\)** セクションで、次の手順を実行します。

    a. **[Service URL]\(サービス URL\)** フィールドに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます

    b. **[Logon name attribute]\(ログオン名属性\)** フィールドに、Azure portal から取得したユーザー要求名を **user.onpremisessamaccountname** ソース属性と共に貼り付けます。

    c. **[Public SSL certificate]\(公開 SSL 証明書\)** フィールドで、Azure portal からダウンロードした**証明書 (Base64)** を使用します。

1. **[Synchronization Settings]\(同期の設定\)** セクションで、次の手順を実行します。

    a. **[Tenant]\(テナント\)** フィールドで、Azure portal から取得した **[ディレクトリ (テナント) ID]** または **[カスタム ドメイン名]** の値を使用します。

    b. **[Application ID]\(アプリケーション ID\)** フィールドで、Azure portal から取得した **[アプリケーション (クライアント) ID]** の値を使用します。

    c. **[Client secret]\(クライアント シークレット\)** フィールドで、Azure portal から取得した**クライアント シークレット**を使用します。

    d. **[Synchronization schedule]\(同期スケジュール\)** フィールドで、Azure AD との同期を手動で行うか、スケジュールに従って行うかを選択します。 [Manually]\(手動\) を選択した場合は、Active Directory ユーザー情報に変更があるたびに必ず [Directory Services]\(ディレクトリ サービス\) 画面に戻って手動同期を実行して、TMWS の情報を最新の状態に保つようにしてください。

    e. **[Test Connection]\(接続のテスト\)** をクリックして、Azure AD サービスが正常に接続できるかどうかを確認します。 
    
    f. **[保存]** をクリックします。
 
 > [!NOTE]
 > Azure AD に対して Trend Micro Web Security を構成する方法の詳細については、[こちら](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx)のドキュメントを参照してください。

## <a name="test-sso"></a>SSO のテスト 

Azure AD サービスを正常に構成し、ユーザーの認証方法として Azure AD を指定したら、TMWS プロキシ サーバーにログオンしてセットアップを確認することができます。 Azure AD ログオンによってアカウントが確認されたら、インターネットにアクセスできます。

> [!NOTE]
> TMWS は Azure AD ポータルの [概要] > [シングル サインオン] > [SAML によるシングル サインオンのセットアップ] > [テスト] からの新規エンタープライズ アプリケーションのシングル サインオンのテストをサポートしません。

1. ブラウザーからすべての Cookie をクリアし、ブラウザーを再起動します。 

1. ブラウザーで TMWS プロキシ サーバーにアクセスします。 詳細については、「[PAC ファイルを使用したトラフィックの転送](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B)」を参照してください。

1. インターネットの任意の Web サイトにアクセスします。 TMWS により、TMWS のキャプティブ ポータルに移動します。

1. Active Directory アカウント (形式: domain\sAMAccountName または sAMAccountName@domain)、電子メール アドレス、または UPN を指定し、 **[ログオン]** をクリックします。 TMWS により Azure AD ログオンに転送されます。

1. Azure AD ログオンで、AD アカウントの資格情報を入力します。 TMWS に正常にログオンします。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Trend Micro Web Security (TMWS) を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [高度な可視性とコントロールを使用して Trend Micro Web Security (TMWS) を保護する方法](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

