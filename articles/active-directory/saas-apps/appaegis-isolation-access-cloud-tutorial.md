---
title: 'チュートリアル: Appaegis Isolation Access Cloud と Azure AD の SSO 統合'
description: Azure Active Directory と Appaegis Isolation Access Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/07/2021
ms.author: jeedes
ms.openlocfilehash: 358dcd9945751a20332063f14d408b12de151fed
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318405"
---
# <a name="tutorial-azure-ad-sso-integration-with-appaegis-isolation-access-cloud"></a>チュートリアル: Appaegis Isolation Access Cloud と Azure AD の SSO 統合

このチュートリアルでは、Appaegis Isolation Access Cloud を Azure Active Directory (Azure AD) と統合する方法について説明します。 Appaegis Isolation Access Cloud を Azure AD と統合すると、次のことができます。

* Appaegis Isolation Access Cloud にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Appaegis Isolation Access Cloud に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Appaegis Isolation Access Cloud でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Appaegis Isolation Access Cloud では、**SP と IDP** によって開始される SSO がサポートされます。
* Appaegis Isolation Access Cloud では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

## <a name="adding-appaegis-isolation-access-cloud-from-the-gallery"></a>ギャラリーからの Appaegis Isolation Access Cloud の追加

Azure AD への Appaegis Isolation Access Cloud の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Appaegis Isolation Access Cloud を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Appaegis Isolation Access Cloud**」と入力します。
1. 結果のパネルから **[Appaegis Isolation Access Cloud]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-appaegis-isolation-access-cloud"></a>Appaegis Isolation Access Cloud の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Appaegis Isolation Access Cloud に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Appaegis Isolation Access Cloud の関連ユーザーとの間にリンク関係を確立する必要があります。

Appaegis Isolation Access Cloud に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Appaegis Isolation Access Cloud の SSO の構成](#configure-appaegis-isolation-access-cloud-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Appaegis Isolation Access Cloud のテスト ユーザーの作成](#create-appaegis-isolation-access-cloud-test-user)** - Appaegis Isolation Access Cloud で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Appaegis Isolation Access Cloud** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    a. **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.appaegis.net` という形式で URL を入力します。

    b. **[リレー状態]** テキスト ボックスに、`<RelayState>` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を、実際のサインオン URL およびリレー状態で更新してください。 これらの値を取得するには、[Appaegis Isolation Access Cloud クライアント サポート チーム](mailto:support@appaegis.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Appaegis Isolation Access Cloud アプリケーションでは、特定の形式の SAML アサーションを受け取るため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Appaegis Isolation Access Cloud アプリケーションでは、さらにいくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 | ソース属性 |
    | ---------| --------- |
    | email | user.userprincipalname |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Appaegis Isolation Access Cloud の設定]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Appaegis Isolation Access Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Appaegis Isolation Access Cloud]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-appaegis-isolation-access-cloud-sso"></a>Appaegis Isolation Access Cloud の SSO の構成

1. Appaegis Isolation Access Cloud 企業サイトに管理者としてログインします。

1. **[Setting]**  >  **[Customization]** に移動して、テキスト ボックスに **テナント ドメイン URL** を入力し、 **[+IdP]** ボタンをクリックします。

    ![アカウントのカスタマイズを示すスクリーンショット。](./media/appaegis-isolation-access-cloud-tutorial/account.png "アカウントのカスタマイズ")

1. **[Identity Provider Details]** ページで、次の手順を実行します。

    ![ID プロバイダーの詳細を示すスクリーンショット。](./media/appaegis-isolation-access-cloud-tutorial/details.png "ID プロバイダー") 

    1. **[Identity Provider]** のドロップダウンから **[Azure AD]** を選択します。

    1. **[ACS URL]** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。

    1. **[Entity ID]** の値をコピーし、その値を Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** テキスト ボックスに貼り付けます。

    1. Azure portal からダウンロードした **フェデレーション メタデータ XML** をメモ帳で開き、 **[SAML ファイルのアップロード]** にそのファイルをアップロードします。

    1. **[Status]** チェック ボックスを有効にし、 **[Save]** をクリックします。

### <a name="create-appaegis-isolation-access-cloud-test-user"></a>Appaegis Isolation Access Cloud のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Appaegis Isolation Access Cloud に作成します。 Appaegis Isolation Access Cloud では、Just-In-Time ユーザー プロビジョニングがサポートされています。これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Appaegis Isolation Access Cloud にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Appaegis Isolation Access Cloud のサインオン URL にリダイレクトされます。  

* Appaegis Isolation Access Cloud のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Appaegis Isolation Access Cloud に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Appaegis Isolation Access Cloud] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Appaegis Isolation Access Cloud に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Appaegis Isolation Access Cloud を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
