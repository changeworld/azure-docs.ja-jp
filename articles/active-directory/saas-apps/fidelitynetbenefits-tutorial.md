---
title: 'チュートリアル: Azure AD SSO と Fidelity NetBenefits の統合'
description: Azure Active Directory と Fidelity NetBenefits の間のシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/25/2021
ms.author: jeedes
ms.openlocfilehash: 304bc6d4d3100478ba4fa542c76cd1d2e4ef4aca
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285644"
---
# <a name="tutorial-azure-ad-sso-integration-with-fidelity-netbenefits"></a>チュートリアル: Azure AD SSO と Fidelity NetBenefits の統合

このチュートリアルでは、Fidelity NetBenefits と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Fidelity NetBenefits を統合すると、次のことができます。

* Fidelity NetBenefits にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Fidelity NetBenefits に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Fidelity NetBenefits でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Fidelity NetBenefits では、**IDP** Initiated SSO がサポートされます。

* Fidelity NetBenefits では、**Just In Time** ユーザー プロビジョニングがサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-fidelity-netbenefits-from-the-gallery"></a>ギャラリーからの Fidelity NetBenefits の追加

Azure AD への Fidelity NetBenefits の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Fidelity NetBenefits を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Fidelity NetBenefits**」と入力します。
1. 結果パネルから **[Fidelity NetBenefits]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-fidelity-netbenefits"></a>Fidelity NetBenefits に対する Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Fidelity NetBenefits に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Fidelity NetBenefits の関連ユーザーとの間にリンク関係を確立する必要があります。

Fidelity NetBenefits に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Fidelity NetBenefits の SSO の構成](#configure-fidelity-netbenefits-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Fidelity NetBenefits のテスト ユーザーの作成](#create-fidelity-netbenefits-test-user)** - Fidelity NetBenefits で B.Simon に対応するユーザーを作成し、Azure AD のそのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Fidelity NetBenefits** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子]** ボックスに、次のいずれかの値を入力します。

    テスト環境: `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    運用環境: `urn:sp:fidelity:geninbndnbparts20`

    b. **[応答 URL]** テキストボックスに、実装時に Fidelity から提供された URL を入力するか、担当の Fidelity Client Service Manager に問い合わせてください。

5. Fidelity NetBenefits アプリケーションでは、特定の形式の SAML アサーションが求められます。そのため、カスタム属性マッピングを SAML トークン属性構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 Fidelity NetBenefits アプリケーションでは、**nameidentifier** が **employeeid** にマップされるか、または実際の組織の **nameidentifier** に該当する他の要求にマップされると想定されているため、**[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

    >[!Note]
    >Fidelity NetBenefits では、静的および動的なフェデレーションがサポートされます。 静的の場合は、SAML ベースのジャスト イン タイムのユーザー プロビジョニングが使用されません。一方、動的の場合は、ジャスト イン タイムのユーザー プロビジョニングがサポートされます。 JIT ベースのプロビジョニングを使用する場合、お客様は、ユーザーの生年月日など、要求をいくつか Azure AD で追加する必要があります。こうした詳細は、**Fidelity Client Service Manager** から提供されます。インスタンスに対して、この動的なフェデレーションを有効にする必要があります。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[Fidelity NetBenefits のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Fidelity NetBenefits へのアクセスを許可して、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Fidelity NetBenefits]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-fidelity-netbenefits-sso"></a>Fidelity NetBenefits の SSO の構成

**Fidelity NetBenefits** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [Fidelity NetBenefits サポート チーム](mailto:SSOMaintenance@fmr.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-fidelity-netbenefits-test-user"></a>Fidelity NetBenefits のテスト ユーザーの作成

このセクションでは、Fidelity NetBenefits で Britta Simon というユーザーを作成します。 静的なフェデレーションを作成している場合、Fidelity NetBenefits プラットフォームでユーザーを作成するには、担当の **Fidelity Client Service Manager** と一緒に作業してください。 こうしたユーザーは、シングル サインオンを使用する前に作成し、アクティブにする必要があります。

動的なフェデレーションについては、ジャスト イン タイムプロビジョニングを使用してユーザーが作成されます。 JIT ベースのプロビジョニングを使用する場合、お客様は、ユーザーの生年月日など、要求をいくつか Azure AD で追加する必要があります。こうした詳細は、**Fidelity Client Service Manager** から提供されます。インスタンスに対して、この動的なフェデレーションを有効にする必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Fidelity NetBenefits に自動的にサインインします。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Fidelity NetBenefits] タイルをクリックすると、SSO を設定した Fidelity NetBenefits に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Fidelity NetBenefits を構成すると、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
