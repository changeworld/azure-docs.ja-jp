---
title: 'チュートリアル: Azure Active Directory と SAP Cloud Platform の統合 | Microsoft Docs'
description: Azure Active Directory と SAP Cloud Platform の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: b15c5a9f9f1e4e144caa2ddaa36d42a2a225b31b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97964050"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>チュートリアル: Azure Active Directory と SAP Cloud Platform の統合

このチュートリアルでは、SAP Cloud Platform と Azure Active Directory (Azure AD) を統合する方法について説明します。 SAP Cloud Platform と Azure AD を統合すると、次のことができます。

* SAP Cloud Platform にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SAP Cloud Platform に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

SAP Cloud Platform と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* SAP Cloud Platform でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、SAP Cloud Platform に割り当てた Azure AD ユーザーは、「 [アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

>[!IMPORTANT]
>シングル サインオンをテストするには、独自のアプリケーションをデプロイするか、SAP Cloud Platform アカウントでアプリケーションをサブスクライブする必要があります。 このチュートリアルでは、アプリケーションはアカウントにデプロイされます。
> 

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SAP Cloud Platform では、**SP** によって開始される SSO がサポートされます

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>ギャラリーからの SAP Cloud Platform の追加

Azure AD への SAP Cloud Platform の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP Cloud Platform を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**SAP Cloud Platform**」と入力します。
1. 結果のパネルから **[SAP Cloud Platform]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform"></a>SAP Cloud Platform の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、SAP Cloud Platform に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと SAP Cloud Platform の関連ユーザーとの間にリンク関係を確立する必要があります。

SAP Cloud Platform に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[SAP Cloud Platform の SSO の構成](#configure-sap-cloud-platform-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[SAP Cloud Platform テスト ユーザーの作成](#create-sap-cloud-platform-test-user)** - SAP Cloud Platform で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **SAP Cloud Platform** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    ![[SAP Cloud Platform のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、ユーザーが **SAP Cloud Platform** アプリケーションへのサインオンに使用する URL を入力します。 これは、SAP Cloud Platform アプリケーションで保護されたリソースのアカウント固有の URL です。 URL は次のパターンに基づいています。`https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >これは、ユーザーが認証を必要とする SAP Cloud Platform アプリケーションの URL です。
    > 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`

    b. **[識別子]** ボックスに、次のいずれかの形式で SAP Cloud Platform の URL を入力します。 

    - `https://hanatrial.ondemand.com/<instancename>`
    - `https://hana.ondemand.com/<instancename>`
    - `https://us1.hana.ondemand.com/<instancename>`
    - `https://ap1.hana.ondemand.com/<instancename>`

    c. **[応答 URL]** ボックスに、 のパターンを使用して URL を入力します。

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>`

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 サインオン URL と識別子を取得するには、[SAP Cloud Platform クライアント サポート チーム](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html)に問い合わせてください。 応答 URL については、チュートリアルの後半で説明されている信頼管理セクションから入手できます。
    > 
4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

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

このセクションでは、B.Simon に SAP Cloud Platform へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SAP Cloud Platform]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-sap-cloud-platform-sso"></a>SAP Cloud Platform の SSO の構成

1. 別の Web ブラウザー ウィンドウで、SAP Cloud Platform コックピット (`https://account.<landscape host>.ondemand.com/cockpit`) にサインインします (例: https://account.hanatrial.ondemand.com/cockpit) )。

2. **[Trust (信頼)]** タブをクリックします。
   
    ![信頼](./media/sap-hana-cloud-platform-tutorial/ic790800.png "[Trust (信頼)]")

3. 信頼管理セクションの **[Local Service Provider]\(ローカル サービス プロバイダー\)** で、次の手順に従います。

    ![[Local Service Provider]\(ローカル サービス プロバイダー\) タブが選択され、すべてのテキスト ボックスが強調表示されている [Trust Management]\(信頼管理\) セクションを示すスクリーンショット。](./media/sap-hana-cloud-platform-tutorial/ic793931.png "信頼管理")
   
    a. **[編集]** をクリックします。

    b. **[構成の種類]** として **[カスタム]** を選択します。

    c. **[Local Provider Name (ローカル プロバイダー名)]** は既定値のままにします。 この値をコピーして、SAP Cloud Platform 用の Azure AD 構成の **[識別子]** フィールドに貼り付けます。

    d. **署名キー** と **署名証明書** キーのペアを生成するには、 **[Generate Key Pair (キー ペアの生成)]** をクリックします。

    e. **[Principal Propagation (プリンシパル伝達)]** で **[無効]** を選択します。

    f. **[Force Authentication (強制認証)]** で **[無効]** を選択します。

    g. **[保存]** をクリックします。

4. **[Local Service Provider]\(ローカル サービス プロバイダー\)** の設定を保存したら、次を実行して応答 URL を取得します。
   
    ![メタデータの取得](./media/sap-hana-cloud-platform-tutorial/ic793930.png "[Get Metadata (メタデータの取得)]")

    a. **[Get Metadata]\(メタデータの取得\)** をクリックして、SAP Cloud Platform メタデータ ファイルをダウンロードします。

    b. ダウンロードした SAP Cloud Platform のメタデータ XML ファイルを開き、**ns3:AssertionConsumerService** タグを見つけます。
 
    c. **Location** 属性の値をコピーして、SAP Cloud Platform 用の Azure AD 構成の **[応答 URL]** フィールドに貼り付けます。

5. **[信頼できる ID プロバイダー]** タブをクリックし、 **[Add Trusted Identity Provider (信頼できる ID プロバイダーの追加)]** をクリックします。
   
    ![[Trusted Identity Provider]\(信頼できる ID プロバイダー\) タブが選択されている [Trust Management]\(信頼管理\) ページを示すスクリーンショット。](./media/sap-hana-cloud-platform-tutorial/ic790802.png "信頼管理")
   
    >[!NOTE]
    >信頼できる ID プロバイダーの一覧を管理するには、ローカル サービス プロバイダーのセクションでカスタム構成タイプを選んでおく必要があります。 既定の構成タイプでは、SAP ID サービスに対する編集不可能で暗黙的な信頼があります。 [なし] では、いずれの信頼設定もありません。
    > 
    > 

6. **[全般]** タブをクリックし、 **[参照]** をクリックして、ダウンロードしたメタデータ ファイルをアップロードします。
    
    ![信頼管理](./media/sap-hana-cloud-platform-tutorial/ic793932.png "信頼管理")
    
    >[!NOTE]
    >メタデータ ファイルをアップロードすると、 **[シングル サインオン URL]** 、 **[Single Logout URL]\(シングル ログアウト URL\)** 、 **[署名証明書]** の値が自動的に設定されます。
    > 
     
7. **[属性]** タブをクリックします。

8. **[属性]** タブで、次の手順に従います。
    
    ![属性](./media/sap-hana-cloud-platform-tutorial/ic790804.png "属性") 

    a. **[Add Assertion-Based Attribute (アサーション ベースの属性の追加)]** をクリックして、次のアサーション ベースの属性を追加します。
       
    | アサーション属性 | プリンシパル属性 |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |email |
   
     >[!NOTE]
     >属性の構成は、SCP 上のアプリケーションがどのように作成されているかに依存します。つまり、SAML 応答で必要になる属性の種類や、コード内でこの属性にアクセスする際に使用される名前 (プリンシパル属性) によって異なります。
     > 
    
    b. スクリーンショットの **[既定の属性]** に指定されている値はサンプルです。 このシナリオでは必要ありません。  
 
    c. スクリーンショットに表示されている **[プリンシパル属性]** の名前と値は、アプリケーションの開発方法によって異なります。 使用するアプリケーションによって、異なるマッピングが必要になる場合があります。

### <a name="assertion-based-groups"></a>アサーション ベースのグループ

オプションの手順として、Azure Active Directory ID プロバイダーのアサーション ベースのグループを構成できます。

SAP Cloud Platform でグループを使用すると、SAP Cloud Platform アプリケーションで、1 つ以上のロールに 1 人以上のユーザーを動的に割り当てることができます。この割り当ては SAML 2.0 アサーションの属性値によって決定されます。 

たとえば、アサーションに属性 "*contract=temporary*" が含まれている場合、対象となるすべてのユーザーが "*TEMPORARY*" グループに追加されます。 "*TEMPORARY*" グループには、SAP Cloud Platform アカウントにデプロイされた 1 つ以上のアプリケーションの 1 つ以上のロールが含まれます。
 
SAP Cloud Platform アカウントでアプリケーションの 1 つ以上のロールに多くのユーザーを同時に割り当てる場合は、アサーション ベースのグループを使用します。 1 人または少数のユーザーのみを特定のロールに割り当てる場合は、SAP Cloud Platform コックピットの **[承認]** タブで直接割り当てることをお勧めします。

### <a name="create-sap-cloud-platform-test-user"></a>SAP Cloud Platform テスト ユーザーの作成

Azure AD ユーザーが SAP Cloud Platform にログインできるようにするには、SAP Cloud Platform のロールをそのユーザーに割り当てる必要があります。

**ロールをユーザーに割り当てるには、次の手順を実行します。**

1. **SAP Cloud Platform** コックピットにログインします。

2. 次の手順を実行します。
   
    ![承認](./media/sap-hana-cloud-platform-tutorial/ic790805.png "[Authorizations]")
   
    a. **[Authorization]** をクリックします。

    b. **[Users]** タブをクリックします。

    c. **[User]** テキストボックスに、ユーザーのメール アドレスを入力します。

    d. **[Assign]** をクリックしてユーザーをロールに割り当てます。

    e. **[保存]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる SAP Cloud Platform のサインオン URL にリダイレクトされます。 

* SAP Cloud Platform のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [SAP Cloud Platform] タイルをクリックすると、SSO を設定した SAP Cloud Platform に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

SAP Cloud Platform を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。