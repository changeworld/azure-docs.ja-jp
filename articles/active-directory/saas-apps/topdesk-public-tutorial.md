---
title: 'チュートリアル: Azure Active Directory と TOPdesk - Public の統合 | Microsoft Docs'
description: Azure Active Directory と TOPdesk - Public の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 54afb9b36df429702ee9a17c30fbad43c53e3b91
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282460"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>チュートリアル: Azure Active Directory と TOPdesk - Public の統合

このチュートリアルでは、TOPdesk - Public と Azure Active Directory (Azure AD) を統合する方法について説明します。 TOPdesk - Public と Azure AD を統合すると、次のことができます。

* TOPdesk - Public にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して TOPdesk - Public に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* TOPdesk - Public でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* TOPdesk - Public では、**SP** Initiated SSO がサポートされます。

## <a name="add-topdesk---public-from-the-gallery"></a>ギャラリーからの TOPdesk - Public の追加

Azure AD への TOPdesk - Public の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから TOPdesk - Public を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**TOPdesk - Public**」と入力します。
1. 結果のパネルから **TOPdesk - Public** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-topdesk---public"></a>TOPdesk - Public の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、TOPdesk - Public に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと TOPdesk - Public の関連ユーザーとの間にリンク関係を確立する必要があります。

TOPdesk - Public に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[TOPdesk - Public の SSO の構成](#configure-topdesk---public-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[TOPdesk - Public のテスト ユーザーの作成](#create-topdesk---public-test-user)** - TOPdesk - Public で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **TOPdesk - Public** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4.  **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル** がある場合は、次の手順に従います。

    >[!NOTE]
    >チュートリアルで後述する **[Configure TOPdesk - Public Single Sign-On]**(TOPdesk - Public シングル サインオンの構成) セクションから **サービス プロバイダー メタデータ ファイル** を取得します。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。
    
    ![メタデータ ファイルをアップロードする](common/upload-metadata.png)

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![メタデータ ファイルを選択する](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子** と **応答 URL** の値が、[基本的な SAML 構成] セクションに自動的に設定されます。

    d. **[サインオン URL]** ボックスに、`https://<companyname>.topdesk.net` という形式で URL を入力します。

    e. **[Identifier URL]\(識別子 URL\)** ボックスに、TOPdesk 構成から取得できる TOPdesk メタデータ URL を入力します。 これには次のパターンを使用する必要があります: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. **[応答 URL]** ボックスに、`https://<companyname>.topdesk.net/tas/public/login/verify` のパターンを使用して URL を入力します。
    
    > [!NOTE] 
    > **[識別子]** と **[応答 URL]** の値が自動的に入力されない場合は、手動で入力する必要があります。 [識別子] については前述のパターンに従い、チュートリアルで後述する **[Configure TOPdesk - Public Single Sign-On]**(TOPdesk - Public シングル サインオンの構成) セクションから [応答 URL] の値を取得します。 この **[サインオン URL]** の値は実際のものとは異なるので、実際のサインオン URL で更新する必要があります。 この値を取得するには、[TOPdesk - Public クライアント サポート チーム](https://my.topdesk.com/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Set up TOPdesk - Public]**(TOPdesk - Public のセットアップ) セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に TOPdesk - Public へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[TOPdesk - Public]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-topdesk---public-sso"></a>TOPdesk - Public の SSO の構成

1. **TOPdesk - Public** 会社サイトに管理者としてサインインします。

2. [**TOPdesk**] メニューで、[**設定**] をクリックします。
   
    ![[設定]](./media/topdesk-public-tutorial/menu.png "設定")

3. [**ログイン設定**] をクリックします。
   
    ![ログイン設定](./media/topdesk-public-tutorial/login.png "[Login Settings]")

4. [**ログイン設定**] メニューを展開し、[**全般**] をクリックします。
   
    ![全般設定](./media/topdesk-public-tutorial/general.png "全般設定")

5. [**SAML ログイン**] 構成セクションの [**公開**] セクションで、次の手順を実行します。
   
    ![技術設定](./media/topdesk-public-tutorial/public.png "技術設定")
   
    a. [**ダウンロード**] をクリックして公開メタデータ ファイルをダウンロードし、コンピューターのローカルに保存します。
   
    b. ダウンロードしたメタデータ ファイルを開いて、**AssertionConsumerService** ノードを探します。

    ![AssertionConsumerService](./media/topdesk-public-tutorial/service.png "AssertionConsumerService")
   
    c. **[AssertionConsumerService]** の値をコピーし、**[基本的な SAML 構成]** セクションの **[応答 URL]** テキストボックスにこの値を貼り付けます。      
   
6. 証明書ファイルを作成するには、次の手順を実行します。
    
    ![[MSSQLSERVER のプロトコルのプロパティ]](./media/topdesk-public-tutorial/certificate-file.png "Certificate")
    
    a. Azure Portal からダウンロードしたメタデータ ファイルを開きます。
    
    b. **RoleDescriptor** ノードを展開します。ここには **fed:ApplicationServiceType** の **xsi:type** があります。
    
    c. **X509Certificate** ノードの値をコピーします。
    
    d. コピーした **X509Certificate** の値をコンピューターのローカルのファイルに保存します。

7. [**公開**] セクションで、[**追加**] をクリックします。
    
    ![SAML ログイン](./media/topdesk-public-tutorial/add.png "[Public]")

8. [**SAML 構成アシスタント**] ダイアログ ページで、次の手順を実行します。
    
    ![SAML 構成アシスタント](./media/topdesk-public-tutorial/configuration.png "[SAML configuration assistant]")
    
    a. Azure Portal からダウンロードしたメタデータ ファイルをアップロードするには、**[Federation Metadata]** で **[Browse]** をクリックします。

    b. 証明書ファイルをアップロードするには、[**証明書 (RSA)**] の下の [**参照**] をクリックします。

    c. TOPdesk サポート チームから取得したロゴ ファイルをアップロードするには、[**ロゴ アイコン**] の下の [**参照**] をクリックします。

    d. **[User name attribute]** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。

    e. [**表示名**] テキストボックスに、構成の名前を入力します。

    f. **[保存]** をクリックします。

### <a name="create-topdesk---public-test-user"></a>TOPdesk - Public のテスト ユーザーの作成

Azure AD ユーザーが TOPdesk - Public にサインインできるようにするには、そのユーザーを TOPdesk - Public にプロビジョニングする必要があります。 TOPdesk - Public の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。

1. **TOPdesk - Public** 企業サイトに管理者としてサインオンします。

2. 上部のメニューで、**[TOPdesk]\>[New]\>[Support Files]\>[Person]** の順にクリックします。
   
    ![Person](./media/topdesk-public-tutorial/files.png "人物")

3. [New Person] ダイアログで、次の手順を実行します。
   
    ![新しいユーザー](./media/topdesk-public-tutorial/new.png "新しいユーザー")
   
    a. [General] タブをクリックします。

    b. **[Surname]** ボックスに、ユーザーの姓を入力します (この例では Simon)。
 
    c. アカウントの [**サイト**] を選択します。
 
    d. **[保存]** をクリックします。

> [!NOTE]
> 他の TOPdesk - Public ユーザー アカウントの作成ツールまたは TOPdesk - Public から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる TOPdesk - Public のサインオン URL にリダイレクトされます。 

* TOPdesk - Public のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [TOPdesk - Public] タイルをクリックすると、TOPdesk - Public のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

TOPdesk - Public を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
