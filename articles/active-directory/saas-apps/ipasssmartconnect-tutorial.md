---
title: 'チュートリアル: Azure AD SSO と iPass SmartConnect の統合'
description: Azure Active Directory と iPass SmartConnect の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: a2f25f25add82568b33bd5c8bfc9466b0c26580b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128650897"
---
# <a name="tutorial-azure-ad-sso-integration-with-ipass-smartconnect"></a>チュートリアル: Azure AD SSO と iPass SmartConnect の統合

このチュートリアルでは、iPass SmartConnect と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と iPass SmartConnect を統合すると、次のことができます。

* iPass SmartConnect にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して iPass SmartConnect に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、<bpt id="p1">[</bpt>無料アカウント<ept id="p1">](https://azure.microsoft.com/free/)</ept>を取得できます。
* iPass SmartConnect でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* iPass SmartConnect では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。
* iPass SmartConnect では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。
* iPass SmartConnect では、[自動ユーザー プロビジョニング](ipass-smartconnect-provisioning-tutorial.md)がサポートされます。


> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>ギャラリーから iPass SmartConnect を追加する

Azure AD への iPass SmartConnect の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に iPass SmartConnect を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで <bpt id="p1">**</bpt>[Azure Active Directory]<ept id="p1">**</ept> サービスを選択します。
1. <bpt id="p1">**</bpt>[エンタープライズ アプリケーション]<ept id="p1">**</ept> に移動し、 <bpt id="p2">**</bpt>[すべてのアプリケーション]<ept id="p2">**</ept> を選択します。
1. 新しいアプリケーションを追加するには、 <bpt id="p1">**</bpt>[新しいアプリケーション]<ept id="p1">**</ept> を選択します。
1. <bpt id="p1">**</bpt>[ギャラリーから追加する]<ept id="p1">**</ept> セクションで、検索ボックスに「<bpt id="p2">**</bpt>iPass SmartConnect<ept id="p2">**</ept>」と入力します。
1. 結果ウィンドウで <bpt id="p1">**</bpt>[iPass SmartConnect]<ept id="p1">**</ept> を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-ipass-smartconnect"></a>iPass SmartConnect の Azure AD SSO の構成とテスト

<bpt id="p1">**</bpt>B.Simon<ept id="p1">**</ept> というテスト ユーザーを使用して、iPass SmartConnect に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと iPass SmartConnect の関連ユーザーとの間にリンク関係を確立する必要があります。

iPass SmartConnect に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. <bpt id="p1">**</bpt><bpt id="p2">[</bpt>Azure AD SSO の構成<ept id="p2">](#configure-azure-ad-sso)</ept><ept id="p1">**</ept> - ユーザーがこの機能を使用できるようにします。
    1. <bpt id="p1">**</bpt><bpt id="p2">[</bpt>Azure AD のテスト ユーザーの作成<ept id="p2">](#create-an-azure-ad-test-user)</ept><ept id="p1">**</ept> - B.Simon で Azure AD のシングル サインオンをテストします。
    1. <bpt id="p1">**</bpt><bpt id="p2">[</bpt>Azure AD テスト ユーザーの割り当て<ept id="p2">](#assign-the-azure-ad-test-user)</ept><ept id="p1">**</ept> - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. <bpt id="p1">**</bpt><bpt id="p2">[</bpt>iPass SmartConnect の SSO の構成<ept id="p2">](#configure-ipass-smartconnect-sso)</ept><ept id="p1">**</ept> - アプリケーション側でシングル サインオン設定を構成します。
    1. <bpt id="p1">**</bpt><bpt id="p2">[</bpt>iPass SmartConnect のテスト ユーザーの作成<ept id="p2">](#create-ipass-smartconnect-test-user)</ept><ept id="p1">**</ept> - iPass SmartConnect で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. <bpt id="p1">**</bpt><bpt id="p2">[</bpt>SSO のテスト<ept id="p2">](#test-sso)</ept><ept id="p1">**</ept> - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **iPass SmartConnect** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. <bpt id="p1">**</bpt>[シングル サインオン方式の選択]<ept id="p1">**</ept> ページで、 <bpt id="p2">**</bpt>[SAML]<ept id="p2">**</ept> を選択します。
1. <bpt id="p1">**</bpt>[SAML によるシングル サインオンのセットアップ]<ept id="p1">**</ept> ページで、 <bpt id="p2">**</bpt>[基本的な SAML 構成]<ept id="p2">**</ept> の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. <bpt id="p2">**</bpt>IDP<ept id="p2">**</ept> 開始モードでアプリケーションを構成する場合は、Azure と既に統合されているため、 <bpt id="p1">**</bpt>[基本的な SAML 構成]<ept id="p1">**</ept> セクションで実行する必要がある手順はありません。

1. アプリケーションを <bpt id="p2">**</bpt>SP<ept id="p2">**</ept> 開始モードで構成する場合は、 <bpt id="p1">**</bpt>[追加の URL を設定します]<ept id="p1">**</ept> をクリックして次の手順を実行します。

    <bpt id="p1">**</bpt>[サインオン URL]<ept id="p1">**</ept> テキスト ボックスに URL として「<ph id="ph1">`https://om-activation.ipass.com/ClientActivation/ssolanding.go`</ph>」と入力します。

1. <bpt id="p1">**</bpt>[保存]<ept id="p1">**</ept> をクリックします。

1. iPass SmartConnect アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、iPass SmartConnect アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 |  ソース属性|
    | ---------------| ----------|
    | firstName | User.givenname |
    | lastName | User.surname |
    | email | user.userprincipalname |
    | username | user.userprincipalname |

1. <bpt id="p1">**</bpt>[SAML でシングル サインオンをセットアップします]<ept id="p1">**</ept> ページの <bpt id="p2">**</bpt>[SAML 署名証明書]<ept id="p2">**</ept> セクションで、 <bpt id="p3">**</bpt>[フェデレーション メタデータ XML]<ept id="p3">**</ept> を探して <bpt id="p4">**</bpt>[ダウンロード]<ept id="p4">**</ept> を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. <bpt id="p1">**</bpt>[iPass SmartConnect のセットアップ]<ept id="p1">**</ept> セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 <bpt id="p1">**</bpt>[Azure Active Directory]<ept id="p1">**</ept> 、 <bpt id="p2">**</bpt>[ユーザー]<ept id="p2">**</ept> 、 <bpt id="p3">**</bpt>[すべてのユーザー]<ept id="p3">**</ept> の順に選択します。
1. 画面の上部にある <bpt id="p1">**</bpt>[新しいユーザー]<ept id="p1">**</ept> を選択します。
1. <bpt id="p1">**</bpt>[ユーザー]<ept id="p1">**</ept> プロパティで、以下の手順を実行します。
   1. <bpt id="p1">**</bpt>[名前]<ept id="p1">**</ept> フィールドに「<ph id="ph1">`B.Simon`</ph>」と入力します。  
   1. <bpt id="p1">**</bpt>[ユーザー名]<ept id="p1">**</ept> フィールドに「<ph id="ph1">username@companydomain.extension</ph>」と入力します。 たとえば、「 <ph id="ph1">`B.Simon@contoso.com`</ph> 」のように入力します。
   1. <bpt id="p1">**</bpt>[パスワードを表示]<ept id="p1">**</ept> チェック ボックスをオンにし、 <bpt id="p2">**</bpt>[パスワード]<ept id="p2">**</ept> ボックスに表示された値を書き留めます。
   1. <bpt id="p1">**</bpt>Create<ept id="p1">**</ept> をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に iPass SmartConnect へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で <bpt id="p1">**</bpt>[エンタープライズ アプリケーション]<ept id="p1">**</ept> を選択し、 <bpt id="p2">**</bpt>[すべてのアプリケーション]<ept id="p2">**</ept> を選択します。
1. アプリケーションの一覧で <bpt id="p1">**</bpt>[iPass SmartConnect]<ept id="p1">**</ept> を選択します。
1. アプリの概要ページで、 <bpt id="p1">**</bpt>[管理]<ept id="p1">**</ept> セクションを見つけて、 <bpt id="p2">**</bpt>[ユーザーとグループ]<ept id="p2">**</ept> を選択します。
1. <bpt id="p1">**</bpt>[ユーザーの追加]<ept id="p1">**</ept> を選択し、 <bpt id="p3">**</bpt>[割り当ての追加]<ept id="p3">**</ept> ダイアログで <bpt id="p2">**</bpt>[ユーザーとグループ]<ept id="p2">**</ept> を選択します。
1. <bpt id="p1">**</bpt>[ユーザーとグループ]<ept id="p1">**</ept> ダイアログの [ユーザー] の一覧から <bpt id="p2">**</bpt>[B.Simon]<ept id="p2">**</ept> を選択し、画面の下部にある <bpt id="p3">**</bpt>[選択]<ept id="p3">**</ept> ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 <bpt id="p1">**</bpt>[ロールの選択]<ept id="p1">**</ept> ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. <bpt id="p1">**</bpt>[割り当ての追加]<ept id="p1">**</ept> ダイアログで、 <bpt id="p2">**</bpt>[割り当て]<ept id="p2">**</ept> をクリックします。

## <a name="configure-ipass-smartconnect-sso"></a>iPass SmartConnect の SSO の構成

<bpt id="p1">**</bpt>iPass SmartConnect<ept id="p1">**</ept> 側でシングル サインオンを構成するには、ダウンロードした<bpt id="p2">**</bpt>フェデレーション メタデータ XML<ept id="p2">**</ept> と Azure portal からコピーした適切な URL を <bpt id="p3">[</bpt>iPass SmartConnect サポート チーム<ept id="p3">](mailto:help@ipass.com)</ept>に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-ipass-smartconnect-test-user"></a>iPass SmartConnect のテスト ユーザーの作成

このセクションでは、iPass SmartConnect で Britta Simon というユーザーを作成します。 <bpt id="p1">[</bpt>iPass SmartConnect サポート チーム<ept id="p1">](mailto:help@ipass.com)</ept>と協力して、iPass SmartConnect プラットフォームで許可リストに追加する必要のあるユーザーまたはドメインを追加します。 ドメインがチームによって追加された場合、ユーザーは iPass SmartConnect プラットフォームに自動的にプロビジョニングされます。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で <bpt id="p1">**</bpt>[このアプリケーションをテストします]<ept id="p1">**</ept> をクリックします。 これにより、ログイン フローを開始できる iPass SmartConnect のサインオン URL にリダイレクトされます。

* iPass SmartConnect のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した iPass SmartConnect に自動的にサインインします

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリの [iPass SmartConnect] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した iPass SmartConnect に自動的にサインインします。 マイ アプリの詳細については、<bpt id="p1">[</bpt>マイ アプリの概要<ept id="p1">](../user-help/my-apps-portal-end-user-access.md)</ept>に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

iPass SmartConnect を構成したら、組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 <bpt id="p1">[</bpt>Microsoft Cloud App Security でセッション制御を強制する方法<ept id="p1">](/cloud-app-security/proxy-deployment-any-app)</ept>をご覧ください。
