---
title: チュートリアル:Azure Active Directory と Miro の統合 | Microsoft Docs
description: Azure Active Directory と Miro の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/26/2021
ms.author: jeedes
ms.openlocfilehash: 6e2b3c611c5bc06e29fb739f01dc69605509a63a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124795186"
---
# <a name="tutorial-integrate-miro-with-azure-active-directory"></a>チュートリアル:Miro と Azure Active Directory の統合

このチュートリアルでは、Miro と Azure Active Directory (Azure AD) を統合する方法について説明します。 このチュートリアルの別のバージョンについては、help.miro.com をご覧ください。 Miro を Azure AD と統合すると、次のことができます。

* Miro にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Miro に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Miro でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 
* Miro では、**SP Initiated SSO と IDP Initiated SSO** のほか、**Just In Time** ユーザー プロビジョニングがサポートされます。
* Miro では、[**自動化された** ユーザー プロビジョニングとプロビジョニング解除](miro-provisioning-tutorial.md) (推奨) がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-miro-from-the-gallery"></a>ギャラリーから Miro を追加する

Azure AD への Miro の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Miro を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Miro**」と入力します。
1. 結果のパネルから **[Miro]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-miro"></a>Miro の Azure AD SSO の構成とテスト

**B. Simon** というテスト ユーザーを使用して、Miro に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Miro の関連ユーザーとの間にリンク関係を確立する必要があります。

Miro に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Miro の SSO の構成](#configure-miro-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Miro テスト ユーザーの作成](#create-miro-test-user)** - Miro で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Miro** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    **[識別子]** ボックスに、`https://miro.com/` という URL を入力します。

5. **[SP]** 開始モードでアプリケーションを構成する場合は、 **[サインオン URL]** テキスト ボックスに次の URL を入力します: `https://miro.com/sso/login/`

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。 これは、Miro 側で SSO を構成するために必要になります。

   ![証明書のダウンロードのリンク](common/certificatebase64.png "証明書のダウンロードのリンク")

1. **[Miro のセットアップ]** セクションで、ログイン URL の値をコピーします。 これは、Miro 側で SSO を構成するために必要になります。

   ![ログイン URL のコピー](./media/miro-tutorial/login.png "ログイン URL のコピー")

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

このセクションでは、B. Simon に Miro へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Miro]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

* または、アプリケーションの **[プロパティ]** にアクセスし、 **[ユーザーの割り当てが必要]** 
![[割り当ての要件を無効にする]](./media/miro-tutorial/properties.png "割り当ての要件を無効にする") をオフに切り替えます。

## <a name="configure-miro-sso"></a>Miro の SSO の構成

Miro 側にシングル サインオンを構成するには、以前にダウンロードした証明書と、以前にコピーしたログイン URL を使用します。 Miro アカウントの設定で、 **[Security]\(セキュリティ\)** セクションに移動し、 **[Enable SSO/SAML]\(SSO/SAML を有効にする\)** をオンに切り替えます。 

1. **[SAML Sign-in URL]\(SAML サインイン URL\)** フィールドにログイン URL を貼り付けます。
1. 証明書ファイルをテキスト エディターで開き、証明書のシーケンスをコピーします。 **[Key x509 Certificate]\(キー X509 証明書\)** フィールドにシーケンスを貼り付けます。
![Miro の設定](./media/miro-tutorial/security.png "Miro の設定")

1. **[Domains]\(ドメイン\)** フィールドにドメイン アドレスを入力し、 **[Add]\(追加\)** をクリックして、検証手順を実行します。 他にもドメイン アドレスがある場合は、これを繰り返します。 Miro の SSO 機能は、リストにドメインがあるエンド ユーザーに対して動作します。 
![[ドメイン]](./media/miro-tutorial/add-domain.png "Domain")

1. Just in Time プロビジョニング (Miro での登録時にユーザーをサブスクリプションにプルする) を使用するかどうかを決定し、 **[Save]\(保存\)** をクリックして Miro 側での SSO 構成を完了します。
![Just in Time プロビジョニング](./media/miro-tutorial/save-configuration.png "Just in Time プロビジョニング") 

### <a name="create-miro-test-user"></a>Miro テスト ユーザーの作成

このセクションでは、B. Simon というユーザーを Miro に作成します。 Miro では、Just-In-Time ユーザー プロビジョニングがサポートされています。これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Miro にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、B.Simon というテスト ユーザーを使用し、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Miro のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックし、B.Simon としてログインすることを選択します。 SSO を設定した Miro サブスクリプションに自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Miro] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Miro に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。