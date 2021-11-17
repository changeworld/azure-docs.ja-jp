---
title: 'チュートリアル: Azure AD SSO と VECOS Releezme Locker management system の統合'
description: Azure Active Directory と VECOS Releezme Locker management system の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/20/2021
ms.author: jeedes
ms.openlocfilehash: 74d030d4bf5b8a7d4ba16a8d7cc0421af0110b02
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132340687"
---
# <a name="tutorial-azure-ad-sso-integration-with-vecos-releezme-locker-management-system"></a>チュートリアル: Azure AD SSO と VECOS Releezme Locker management system の統合

このチュートリアルでは、VECOS Releezme Locker management system と Azure Active Directory (Azure AD) を統合する方法について説明します。 VECOS Releezme Locker management system を Azure AD と統合すると、次のことができます。

* VECOS Releezme Locker management system にアクセスできるユーザーを Azure AD で制御できます。 VECOS Releezme Locker Management System へのアクセスが必要なのは、保管ボックスを管理する必要があるユーザー (施設管理者、サービス デスクの従業員など) のみです。
* ユーザーが自分の Azure AD アカウントを使用して VECOS Releezme Locker management system に自動的にサインインできるようにすることができます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* VECOS Releezme Locker management system のシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* VECOS Releezme Locker management system では、**SP** によって開始される SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-vecos-releezme-locker-management-system-from-the-gallery"></a>ギャラリーからの VECOS Releezme Locker management system の追加

Azure AD への VECOS Releezme Locker management system の統合を構成するには、マネージド SaaS アプリの一覧にギャラリーから VECOS Releezme Locker management system を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**VECOS Releezme Locker management system**」と入力します。
1. 結果のパネルから **[VECOS Releezme Locker management system]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-vecos-releezme-locker-management-system"></a>VECOS Releezme Locker management system の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、VECOS Releezme Locker management system に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと VECOS Releezme Locker management system の関連ユーザーとの間にリンク関係を確立する必要があります。

VECOS Releezme Locker management system に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[VECOS Releezme Locker management system SSO の構成](#configure-vecos-releezme-locker-management-system-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[VECOS Releezme Locker management system のテスト ユーザーの作成](#create-vecos-releezme-locker-management-system-test-user)** - VECOS Releezme Locker management system で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **VECOS Releezme Locker management system** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。 

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<baseURL>/`

    b. **[応答 URL]** ボックスに、`https://<baseURL>/Saml2/Acs` のパターンを使用して URL を入力します。
    
    c. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。   
    `https://<baseURL>/sso` (必要に応じて、VECOS によって指定された会社コード値を含む `?companycode=` クエリ パラメーターを追加します)。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は、実際の識別子、応答 URL、サインオン URL で更新してください。 [VECOS Releezme Locker management system サポート チーム](mailto:servicedesk@vecos.com)に連絡して、接続先のリージョンを問い合わせてください。 リージョンによっては、以下とは URL が異なります。

    | **[リージョン]** | **baseURL** |
    |-------|-------|
    | ヨーロッパ| `https://www.releezme.net` |
    | 北米 | `https://na.releezme.net` |
    | アジア太平洋 | `https://au.releezme.net` |

1. **[Set up single sign-on with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

## <a name="configure-vecos-releezme-locker-management-system-roles"></a>VECOS Releezme Locker management system のロールの構成

1. Azure portal で **[アプリの登録]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリの登録一覧で **[VECOS Releezme Locker management system]** を選択します。
1. アプリの登録で、 **[アプリのロール]** を開きます。
1. [アプリのロール] ページで、 **[Create app role]\(アプリのロールの作成\)** をクリックして、新しいアプリのロールを作成します
1. **[表示名]** フィールドに、ロールの名前を入力します (例: `VECOS Company Facility Manager`)。
1. **[Allowed member types]\(許可されるメンバーの種類\)** の値として、 **[ユーザー/グループ]** を選択します。
1. **[値]** フィールドに VECOS Releezme Locker management system のロール名を入力します。 次の表を参照してください。
1. **[適用]** をクリックします。

| Role | ロールの値 | 説明 |
| -- | --------- | ---------- |
| サービス デスク | CompanyServiceDesk | アクセスが制限されているサービス デスク。 ほとんどの場合、読み取り専用アクセス |
| サービス デスク + | CompanyServiceDeskPlus | より多くの読み取りおよび書き込みアクセス権を持つ、サービス デスクの上位バージョン |
| 施設管理者 | CompanyFacilityManager | 会社の設備にアクセスできる設備管理者 |
| 施設管理者 + | CompanyFacilityManagerPlus | 社内で追加のアクセス権を持つ上位の設備管理者。 |
| 管理者 | CompanyAdmin | 会社への完全なアクセス権を持つ管理者 |

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

このセクションでは、VECOS Releezme Locker management system へのアクセス権を付与することによって、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[VECOS Releezme Locker management system]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-vecos-releezme-locker-management-system-sso"></a>VECOS Releezme Locker management system SSO の構成

**VECOS Releezme Locker management system** 側でシングル サインオンを構成するには、**アプリのフェデレーション メタデータ URL** を [VECOS Releezme Locker management system サポート チーム](mailto:servicedesk@vecos.com)に送る必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-vecos-releezme-locker-management-system-test-user"></a>VECOS Releezme Locker management system のテスト ユーザーの作成

このセクションでは、VECOS Releezme Locker management system で Britta Simon というユーザーを作成します。 [VECOS Releezme Locker management system サポート チーム](mailto:servicedesk@vecos.com)と連携し、VECOS Releezme Locker management system プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる VECOS Releezme Locker management system のサインオン URL にリダイレクトされます。 

* VECOS Releezme Locker management system のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [VECOS Releezme Locker management system] タイルをクリックすると、VECOS Releezme Locker management system のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

VECOS Releezme Locker management system を構成すると、組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
