---
title: 'チュートリアル: Azure AD SSO と Keeper Password Manager の統合'
description: Azure Active Directory と Keeper Password Manager の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/24/2021
ms.author: jeedes
ms.openlocfilehash: 26f56e452b2065fd61180d9e6cc3f757d8392c16
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294848"
---
# <a name="tutorial-azure-ad-sso-integration-with-keeper-password-manager"></a>チュートリアル: Azure AD SSO と Keeper Password Manager の統合

このチュートリアルでは、Keeper Password Manager と Azure Active Directory (Azure AD) を統合する方法について説明します。 Keeper Password Manager と Azure AD を統合すると、次のことができます。

* Keeper Password Manager にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Keeper Password Manager に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Keeper Password Manager でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Keeper Password Manager では、SP Initiated SSO がサポートされます。
* Keeper Password Manager では、[**自動化された** ユーザー プロビジョニングとプロビジョニング解除](keeper-password-manager-digitalvault-provisioning-tutorial.md) (推奨) がサポートされます。
* Keeper Password Manager では、Just In Time ユーザー プロビジョニングがサポートされます。

## <a name="add-keeper-password-manager-from-the-gallery"></a>ギャラリーから Keeper Password Manager を追加する

Azure AD への Keeper Password Manager の統合を構成するには、ギャラリーからマネージド SaaS (サービスとしてのソフトウェア) アプリの一覧にこのアプリケーションを追加します。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左側のペインで、 **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Keeper Password Manager**」と入力します。
1. 結果のパネルから **[Keeper Password Manager]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager"></a>Keeper Password Manager のための Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Keeper Password Manager 用に Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Keeper Password Manager の関連ユーザーとの間にリンク関係を確立する必要があります。

Keeper Password Manager 用に Azure AD SSO を構成してテストするには、次の手順を行います。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、ユーザーがこの機能を使用できるようにします。

    1. [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。

1. [Keeper Password Manager SSO の構成](#configure-keeper-password-manager-sso) - アプリケーション側で SSO 設定を構成します。
    1. [Keeper Password Manager のテスト ユーザーを作成](#create-a-keeper-password-manager-test-user)して、Keeper Password Manager で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. [SSO をテスト](#test-sso)して、構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Keeper Password Manager** アプリケーション統合ページで、 **[管理]** セクションを見つけます。 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

   ![鉛筆アイコンが強調表示された [SAML でシングル サインオンをセットアップします] のスクリーンショット。](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** に、次のいずれかのパターンを使用して URL を入力します。
    * クラウド SSO の場合: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * オンプレミス SSO の場合: `https://<KEEPER_FQDN>/sso-connect`

    b. **[応答 URL]** に、次のいずれかのパターンを使用して URL を入力します。
    * クラウド SSO の場合: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * オンプレミス SSO の場合: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    c. **[サインオン URL]** に、次のいずれかのパターンを使用して URL を入力します。
    * クラウド SSO の場合: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * オンプレミス SSO の場合: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Keeper Password Manager クライアント サポート チーム](https://keepersecurity.com/contact.html)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Keeper Password Manager アプリケーションでは、特定の形式の SAML アサーションを要求するため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![[ユーザー属性とクレーム] のスクリーンショット。](common/default-attributes.png)

1. さらに、Keeper Password Manager アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 それらを次の表に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ------------| --------- |
    | First | User.givenname |
    | Last (最後へ) | User.surname |
    | Email | User.mail |

5. **[SAML によるシングル サインオンのセットアップ]** の **[SAML 署名証明書]** セクションで、 **[ダウンロード]** を選択します。 これにより、要件に応じたオプションから **フェデレーション メタデータ XML** がダウンロードされ、コンピューターに保存されます。

    ![[ダウンロード] が強調表示された [SAML 署名証明書] のスクリーンショット。](common/metadataxml.png)

6. **[Keeper Password Manager のセットアップ]** で、要件に従って適切な URL をコピーします。

    ![URL が強調表示された [Keeper Password Manager のセットアップ] のスクリーンショット。](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションでは、Azure portal で `B.Simon` というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **名前** には、`B.Simon`を入力します。  
   1. **[ユーザー名]** に「`username@companydomain.extension`」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** を選び、表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Keeper Password Manager へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[Keeper Password Manager ]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** で、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** で、ユーザーの一覧から **[B.Simon]** を選択します。 次に、画面の下部にある **[選択]** を選択します。
1. ユーザーにロールを割り当てる必要がある場合は、 **[ロールの選択]** の一覧から選択できます。 このアプリに対してロールが設定されていない場合は、 **[既定のアクセス]** ロールが選択されます。
1. **[割り当ての追加]** で **[割り当て]** を選択します。

## <a name="configure-keeper-password-manager-sso"></a>Keeper Password Manager の SSO の構成

アプリの SSO を構成するには、[Keeper サポート ガイド](https://docs.keeper.io/sso-connect-guide/)のガイドラインを参照してください。

### <a name="create-a-keeper-password-manager-test-user"></a>Keeper Password Manager のテスト ユーザーの作成

Azure AD ユーザーが Keeper Password Manager にサインインできるようにするには、ユーザーをプロビジョニングする必要があります。 このアプリケーションでは、Just-In-Time ユーザー プロビジョニングがサポートされているので、認証後にユーザーがアプリケーションに自動的に作成されます。 ユーザーを手動で設定する場合は、[Keeper サポート](https://keepersecurity.com/contact.html)にお問い合わせください。

> [!NOTE]
> Keeper Password Manager では、自動ユーザー プロビジョニングもサポートされます。自動ユーザー プロビジョニングの構成方法の詳細については、[こちら](./keeper-password-manager-digitalvault-provisioning-tutorial.md)を参照してください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Keeper Password Manager のサインオン URL にリダイレクトされます。 

* Keeper Password Manager のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 アクセス パネルで [Keeper Password Manager] タイルをクリックすると、Keeper Password Manager のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Keeper Password Manager を構成したら、セッション制御を適用できます。 これにより、組織の機密データを流出と侵入からリアルタイムで保護することができます。 セッション制御は、条件付きアクセスを拡張したものです。 詳細については、[Microsoft Defender for Cloud Apps でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)に関するページをご覧ください。
