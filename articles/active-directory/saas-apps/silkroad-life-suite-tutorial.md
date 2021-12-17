---
title: 'チュートリアル: Azure Active Directory と SilkRoad Life Suite の統合 | Microsoft Docs'
description: Azure Active Directory と SilkRoad Life Suite の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2021
ms.author: jeedes
ms.openlocfilehash: 8bef3229ca8ca814e55d9dba34c79646367bbd7c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132304691"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>チュートリアル: Azure Active Directory と SilkRoad Life Suite の統合

このチュートリアルでは、SilkRoad Life Suite と Azure Active Directory (Azure AD) を統合する方法について説明します。 SilkRoad Life Suite を Azure AD と統合すると、次のことができます。

* SilkRoad Life Suite にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SilkRoad Life Suite に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

SilkRoad Life Suite と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SilkRoad Life Suite でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SilkRoad Life Suite では、**SP** Initiated SSO がサポートされます。

## <a name="add-silkroad-life-suite-from-the-gallery"></a>ギャラリーからの SilkRoad Life Suite の追加

Azure AD への SilkRoad Life Suite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SilkRoad Life Suite を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**SilkRoad Life Suite**」と入力します。
1. 結果のパネルから **[SilkRoad Life Suite]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-silkroad-life-suite"></a>SilkRoad Life Suite に対して Azure AD SSO を構成してテストする

**B.Simon** というテスト ユーザーを使用して、SilkRoad Life Suite に対して Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと SilkRoad Life Suite の関連ユーザーとの間にリンク関係を確立する必要があります。

SilkRoad Life Suite に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[SilkRoad Life Suite の SSO の構成](#configure-silkroad-life-suite-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[SilkRoad Life Suite テスト ユーザーの作成](#create-silkroad-life-suite-test-user)** - SilkRoad Life Suite で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **SilkRoad Life Suite** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル** がある場合は、次の手順に従います。

    > [!NOTE]
    > このチュートリアルで後述する **サービス プロバイダーのメタデータ ファイル** を取得します。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![スクリーンショットは、[メタデータ ファイルをアップロードする] リンクを含む、[基本的な SAML 構成] を示しています。](common/upload-metadata.png)

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![スクリーンショットは、ファイルを選択してアップロードできるダイアログ ボックスを示しています。](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子** と **応答 URL** の値が、[基本的な SAML 構成] セクションに自動的に設定されます。

    > [!Note]
    > **識別子** と **返信 URL** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。

    d. **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.silkroad-eng.com/Authentication/` という形式で URL を入力します。

5. **サービス プロバイダー メタデータ ファイル** がない場合は、 **[基本的な SAML 構成]** セクションで次の手順に従います。

    a。 **[識別子]** ボックスに、次のいずれかのパターンを使用して URL を入力します。

    | 識別子 |
    |-----|
    |`https://<SUBDOMAIN>.silkroad-eng.com/Authentication/SP`|
    |`https://<SUBDOMAIN>.silkroad.com/Authentication/SP`|
    

    b. **[応答 URL]** ボックスに、次のいずれかのパターンを使用して URL を入力します。

    | [応答 URL] |
    |-----|
    |`https://<SUBDOMAIN>.silkroad-eng.com/Authentication/`|
    |`https://<SUBDOMAIN>.silkroad.com/Authentication/`|

    c. **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.silkroad-eng.com/Authentication/` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[SilkRoad Life Suite クライアント サポート チーム](https://www.silkroad.com/locations/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[SilkRoad Life Suite のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に SilkRoad Life Suite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SilkRoad Life Suite]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-silkroad-life-suite-sso"></a>SilkRoad Life Suite の SSO の構成

1. SilkRoad 企業サイトに管理者としてサインインします。

    > [!NOTE]
    > Microsoft Azure AD とフェデレーションを構成するために SilkRoad Life Suite の認証アプリケーションへのアクセス権を取得するには、SilkRoad サポートまたは SilkRoad サービス担当者にお問い合わせください。

1. **[サービス プロバイダー]** に移動して、 **[フェデレーションの詳細]** をクリックします。

    ![[サービス プロバイダー] から [フェデレーションの詳細] を選択した画面のスクリーンショット。](./media/silkroad-life-suite-tutorial/details.png)

1. **[Download Federation Metadata (フェデレーション メタデータのダウンロード)]** をクリックし、メタデータ ファイルをコンピューターに保存します。 ダウンロードしたフェデレーション メタデータは、Azure portal の **[基本的な SAML 構成]** セクションで **[サービス プロバイダー メタデータ ファイル]** として使用します。

    ![[Download Federation Metadata]\(フェデレーション メタデータのダウンロード\) リンクのスクリーンショット。](./media/silkroad-life-suite-tutorial/metadata.png)

1. **SilkRoad** アプリケーションで、 **[Authentication Sources (認証ソース)]** をクリックします。

    ![[Authentication Sources]\(認証ソース\) が選択された画面のスクリーンショット。](./media/silkroad-life-suite-tutorial/sources.png) 

1. **[Add Authentication Source (認証ソースの追加)]** をクリックします。

    ![[Add Authentication Source]\(認証ソースの追加\) リンクが選択された画面のスクリーンショット。](./media/silkroad-life-suite-tutorial/add-source.png)

1. **[Add Authentication Source (認証ソースの追加)]** セクションで、次の手順に従います。

    ![[Create Identity Provider using File Data]\(ファイル データを使用して ID プロバイダーを作成\) ボタンが選択されている [Add Authentication Source]\(認証ソースの追加\) 画面のスクリーンショット。](./media/silkroad-life-suite-tutorial/metadata-file.png)
  
    a. **[Option 2 - Metadata File]\(オプション 2 - メタデータ ファイル\)** の下の **[参照]** をクリックして、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。
  
    b. **[Create Identity Provider using File Data]** をクリックします。

1. **[Authentication Sources (認証ソース)]** セクションで、 **[編集]** をクリックします。

    ![[編集] オプションが選択されている [Authentication Sources]\(認証ソース\) 画面のスクリーンショット。](./media/silkroad-life-suite-tutorial/edit-source.png)

1. **[Edit Authentication Source (認証ソースの編集)]** ダイアログ ボックスで、次の手順を実行します。

    ![[Edit Authentication Source]\(認証ソースの編集\) ダイアログ ボックスのスクリーンショット。ここで、説明されている値を入力できます。](./media/silkroad-life-suite-tutorial/authentication.png)

    a. **[Enabled]** で **[Yes]** を選択します。

    b. **[EntityId]\(エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    c. **[IdP Description]\(IdP の説明\)** ボックスに、構成の説明を入力します (例: **Azure AD の SSO**)。

    d. **[メタデータ ファイル]** ボックスに、Azure Portal からダウンロードした **メタデータ** ファイルをアップロードします。
  
    e. **[IdP Name]\(IdP 名\)** ボックスに、構成の固有の名前を入力します (例: *Azure SP*)。
  
    f. **[Logout Service URL]\(ログアウト サービス URL\)** テキスト ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    g. **[Sign-on service URL]\(サインオン サービス URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    h. **[保存]** をクリックします。

1. その他のすべての認証のソースを無効にします。

    ![[Authentication Sources]\(認証ソース\) 画面のスクリーンショット。ここで、他のソースを無効にすることができます。 ](./media/silkroad-life-suite-tutorial/manage-source.png)

### <a name="create-silkroad-life-suite-test-user"></a>SilkRoad Life Suite のテスト ユーザーの作成

このセクションでは、SilkRoad Life Suite で Britta Simon というユーザーを作成します。 SilkRoad Life Suite プラットフォームにユーザーを追加するには、[SilkRoad Life Suite Client サポート チーム](https://www.silkroad.com/locations/)に問い合わせてください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる SilkRoad Life Suite のサインオン URL にリダイレクトされます。 

* SilkRoad Life Suite のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [SilkRoad Life Suite] タイルをクリックすると、SilkRoad Life Suite のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

SilkRoad Life Suite を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
