---
title: チュートリアル:Azure Active Directory と SharePoint オンプレミスの統合 | Microsoft Docs
description: Azure Active Directory と SharePoint オンプレミスの間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 7e47891a74feb60b0f3e4594bd1621e8b62d64d1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867106"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と SharePoint オンプレミスの統合

このチュートリアルでは、SharePoint オンプレミスと Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と SharePoint オンプレミスを統合すると、次のことができます。

* SharePoint オンプレミスにアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SharePoint オンプレミスに自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure Active Directory と SharePoint オンプレミスの統合を構成するには、次のものが必要です。

* Azure Active Directory のサブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* SharePoint 2013 以降のファーム。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure Active Directory のシングル サインオンを構成してテストします。 Azure Active Directory のユーザーは、SharePoint オンプレミスにアクセスできるようになります。

## <a name="create-the-enterprise-applications-in-azure-portal"></a>Azure portal でエンタープライズ アプリケーションを作成する

Azure AD への SharePoint オンプレミスの統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に SharePoint オンプレミスを追加する必要があります。

ギャラリーから SharePoint オンプレミスを追加するには、次の手順を実行します。

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

 > [!NOTE]
 > この要素を使用できない場合は、左側のナビゲーション ウィンドウの上部にある固定された **[すべてのサービス]** リンクを通して開くこともできます。 以下の概要では、 **[Azure Active Directory]** リンクは **[ID]** セクションに配置されています。または、フィルター テキスト ボックスを使用して検索できます。

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

4. 検索ボックスに「**SharePoint オンプレミス**」と入力し、結果パネルで **[SharePoint オンプレミス]** を選択します。

    <kbd>![結果リストの SharePoint オンプレミス](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. SharePoint オンプレミスの名前を指定し、 **[追加]** をクリックしてアプリケーションを追加します。

1. 新しいエンタープライズ アプリケーションで [プロパティ] をクリックし、 **[ユーザーの割り当てが必要]** の値を確認します

   <kbd>![結果リストの SharePoint オンプレミス](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

このシナリオでは、この値は **[いいえ]** に設定されています。

## <a name="configure-and-test-azure-ad"></a>Azure AD を構成してテストする

このセクションでは、SharePoint オンプレミスに対する Azure AD シングル サインオンを構成します。
シングル サインオンを機能させるには、Azure AD ユーザーと SharePoint オンプレミス内の関連ユーザー間にリンク関係が確立されている必要があります。

SharePoint オンプレミスに対する Azure Active Directory シングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[SharePoint オンプレミスを構成する](#configure-sharepoint-on-premises)** - アプリケーション側でシングル サインオン設定を構成します。
1. **[Azure portal で Azure AD テスト ユーザーを作成する](#create-an-azure-ad-test-user-in-the-azure-portal)** - シングル サインオン用に Azure AD で新しいユーザーを作成します。
1. **[Azure portal で Azure AD セキュリティ グループを作成する](#create-an-azure-ad-security-group-in-the-azure-portal)** - シングル サインオン用に Azure AD で新しいセキュリティ グループを作成します。
1. **[SharePoint オンプレミスで Azure Active Directory アカウントにアクセス許可を付与する](#grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises)** - Azure AD ユーザーにアクセス許可を付与します。
1. **[SharePoint オンプレミスで Azure AD グループにアクセス許可を付与する](#grant-permissions-to-azure-ad-group-in-sharepoint-on-premises)** - Azure AD グループにアクセス許可を付与します。
1. **[Azure portal で SharePoint オンプレミスへのアクセス権をゲスト アカウントに付与する](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)** - SharePoint オンプレミスの Azure AD でゲスト アカウントにアクセス許可を付与します。
1. **[複数の Web アプリケーション用に信頼できる ID プロバイダーを構成する](#configuring-the-trusted-identity-provider-for-multiple-web-applications)** - 複数の Web アプリケーションに対して同じ信頼できる ID プロバイダーを使用する方法

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

SharePoint オンプレミスで Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) で、Azure AD ディレクトリを開き、 **[エンタープライズ アプリケーション]** をクリックします。**前の手順で作成したエンタープライズ アプリケーションの名前**をクリックし、 **[シングル サインオン]** をクリックします。

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML]** モードをクリックして、シングル サインオンを有効にします。
 
3. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[SharePoint オンプレミスのドメインと URL] のシングル サインオン情報](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. **[識別子]** ボックスに、`urn:<sharepointFarmName>:<federationName>` という形式で URL を入力します。

    1. **[応答 URL]** ボックスに、`https://<YourSharePointSiteURL>/_trust/` のパターンを使用して URL を入力します

    1. **[サインオン URL]** ボックスに、`https://<YourSharePointSiteURL>/` という形式で URL を入力します。
    1. [保存] をクリックします。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

6. **[Set up SharePoint on-premises]\(SharePoint オンプレミスのセットアップ\)** セクションで、要件どおりの適切な URL をコピーします。
    
    1. **ログイン URL**
    
        ログイン URL をコピーし、末尾の **/saml2** を **/wsfed** に置き換えます。結果は **https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed** のようになります (この URL は正確ではありません)

    1. **Azure AD 識別子**
    1. **ログアウト URL**
    > [!NOTE]
    > この URL は SharePoint でそのまま使用することはできません。 **/saml2** を **/wsfed** に置き換える必要があります。 Sharepoint オンプレミス アプリケーションは SAML 1.1 トークンを使用するため、Azure AD では認証の後に SharePoint サーバーからの WS 取り込み要求が必要で、SAML 1.1 トークンを発行します。

### <a name="configure-sharepoint-on-premises"></a>SharePoint オンプレミスを構成する

1. **SharePoint Server 2016 で、信頼できる ID プロバイダーを新しく作成します**

    SharePoint サーバーにサインインし、SharePoint 管理シェルを開きます。 次の値を入力します。
    1. **$realm** (Azure portal の [SharePoint オンプレミス ドメインと URL] セクションの識別子の値)。
    1. **$wsfedurl** (シングル サインオン サービス URL)。
   1. **$filepath** (証明書ファイルをダウンロードしたファイル パス) (Azure portal から)。

    次のコマンドを実行して、信頼できる ID プロバイダーを新しく構成します。

    > [!TIP]
    > PowerShell を使用した経験がない場合、または PowerShell の動作の詳細については、[SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps) に関する記事をご覧ください。


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. **対象のアプリケーションの信頼できる ID プロバイダーを有効にします**

    a. [サーバーの全体管理] で **[Web アプリケーションの管理]** に移動し、Azure AD を使用してセキュリティ保護する Web アプリケーションを選択します。

    b. リボンで、 **[認証プロバイダー]** をクリックして、使用するゾーンを選択します。

    c. **[信頼できる ID プロバイダー]** を選択し、 *[AzureAD]* という名前で先ほど登録した ID プロバイダーを選択します。

    d. **[OK]** をクリックします。

    ![認証プロバイダーを構成する](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Azure portal で Azure AD テスト ユーザーを作成する

このセクションの目的は、Azure portal でテスト ユーザーを作成することです。

1. Azure portal の左側のペインで **[Azure Active Directory]** を選択し、 **[管理]** ペインで **[ユーザー]** を選択します。

2. 次に、 **[すべてのユーザー]** を選択してから、画面の上部にある **[新しいユーザー]** を選択します。

3. **[ユーザーの作成]** オプションを選択し、[User properties]\(ユーザーのプロパティ\) で次の手順を実行します。  
   テナントのサフィックスまたは任意の検証済みドメインを使用して、ご自分の Azure AD にユーザーを作成できる場合があります。 

    a. **[名前]** フィールドにユーザー名を入力します。ここでは **TestUser** を使用しました。
  
    b. **[ユーザー名]** フィールドに「`TestUser@yourcompanydomain.extension`」と入力します。  
    たとえば、TestUser@contoso.com のように指定します。

    ![[ユーザー] ダイアログ ボックス](./media/sharepoint-on-premises-tutorial/user-properties.png)

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

    e. これで、サイトを TestUser@contoso.com と共有し、このユーザーにアクセスを許可することができます。

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Azure portal で Azure AD セキュリティ グループを作成する

1. **[Azure Active Directory] > [グループ]** をクリックします。

2. **[新しいグループ]** をクリックします。

3. **[グループの種類]** 、 **[グループ名]** 、 **[グループの説明]** 、 **[メンバーシップの種類]** を入力します。 矢印をクリックしてメンバーを選択し、グループに追加するメンバーを検索またはクリックします。 **[選択]** をクリックして選択したメンバーを追加した後、 **[作成]** をクリックします。

![Azure AD セキュリティ グループを作成する](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises"></a>SharePoint オンプレミスで Azure Active Directory アカウントにアクセス許可を付与する

オンプレミスの SharePoint で Azure Active Directory ユーザーにアクセス権を付与するには、サイト コレクションを共有するか、サイト コレクションのいずれかのグループに Azure Active Directory ユーザーを追加する必要があります。これで、ユーザーは Azure AD の ID を使用して SharePoint 201x にサインインできるようになります。しかし、ユーザー エクスペリエンスを改善する機会はまだあります。 たとえば、ユーザーを検索すると、ユーザーの選択ウィンドウに複数の検索結果が表示されます。 要求のマッピングで作成された要求の種類ごとに検索結果が表示されます。 ユーザーの選択ウィンドウを使用してユーザーを選択するには、ユーザー名を正確に入力し、**名前**要求結果を選択する必要があります。

![要求検索結果](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

検索条件値の検証機能がないため、スペル ミスが生じたり、ユーザーが誤って正しくない種類の要求を選択したりする可能性があります。 これにより、ユーザーがリソースにアクセスできない可能性があります。

このシナリオの**ユーザー ピッカーを修正する**ために、[AzureCP](https://yvand.github.io/AzureCP/) と呼ばれるオープンソースのソリューションを使用できます。これは、SharePoint 2013、2016、2019 向けのカスタム クレーム プロバイダーを提供するものです。 このソリューションは、Microsoft Graph API を使用してユーザー入力を解決し、検証します。 詳細については [AzureCP](https://yvand.github.io/AzureCP/) を参照してください。

  > [!NOTE]
  > AzureCP がない場合は、Azure AD グループの ID を追加することでグループを追加できますが、これはユーザー フレンドリではなく、信頼性も高くありません。 この場合、次のようになります。  
  >   
  >![Azure AD グループを SharePoint グループに追加する](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-azure-ad-group-in-sharepoint-on-premises"></a>SharePoint オンプレミスで Azure AD グループにアクセス許可を付与する

Azure Active Directory セキュリティ グループを SharePoint オンプレミスに割り当てるには、SharePoint Server 用のカスタム クレーム プロバイダーを使用する必要があります。 この例では、AzureCP を使用しました。

 > [!NOTE]
 > AzureCP は Microsoft 製品ではないか、または Microsoft のテクニカル サポートでサポートされていないことに注意してください。 https://yvand.github.io/AzureCP/ に従って、AzureCP をダウンロードし、オンプレミスの SharePoint ファームにインストールして構成します。 

1. オンプレミスの SharePoint ファームまたは代わりのカスタム クレーム プロバイダー ソリューションで、AzureCP を構成します。 AzureCP の構成手順については、 https://yvand.github.io/AzureCP/Register-App-In-AAD.html を参照してください

1. Azure portal で Azure AD ディレクトリを開きます。 **[エンタープライズ アプリケーション]** をクリックします。**前の手順で作成したエンタープライズ アプリケーションの名前**をクリックし、 **[シングル サインオン]** をクリックします。

1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[ユーザー属性とクレーム]** セクションを編集します。

1. **[グループ要求を追加する]** をクリックします。

1. ユーザーに関連付けられているグループのうちのどのグループを要求で返すかを選択します。ここでは、 **[すべてのグループ]** を選択し、[基になる属性] セクションで **[グループ ID]** を選択して、 **[保存]** をクリックします。

オンプレミスの SharePoint で Azure Active Directory セキュリティ グループへのアクセス権を付与するには、サイト コレクションを共有するか、Azure Active Directory セキュリティ グループをサイト コレクションのいずれかのグループに追加する必要があります。

1. SharePoint サイト コレクションに移動し、サイト コレクションの [サイトの設定] で [People and groups]\(ユーザーとグループ\) をクリックします。 SharePoint グループを選択し、[新規]、[Add Users to this Group]\(ユーザーをこのグループに追加する\) の順にクリックし、グループの名前の入力を開始します。ユーザー ピッカーに、Azure Active Directory セキュリティ グループが表示されます。

    ![Azure AD グループを SharePoint グループに追加する](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Azure portal で SharePoint オンプレミスへのアクセス権をゲスト アカウントに付与する

一貫性のある方法で SharePoint サイトへのアクセス権をゲスト アカウントに付与できるようになりました。 UPN は変更されることがあります。 ユーザー (jdoe@outlook.com) は `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com` のように表されます。 サイトを外部ユーザーと共有しながらシームレスなエクスペリエンスを実現するには、Azure portal の **[ユーザー属性とクレーム]** セクションでいくつかの変更を加える必要があります。

1. Azure portal で Azure AD ディレクトリを開きます。 **[エンタープライズ アプリケーション]** をクリックします。**前の手順で作成したエンタープライズ アプリケーションの名前**をクリックし、 **[シングル サインオン]** をクリックします。

1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[ユーザー属性とクレーム]** セクションを編集します。

1. **[必要な要求]** ゾーンで、 **[一意のユーザー識別子 (名前 ID)]** をクリックします。

1. **[基になる属性]** プロパティを値 **user.localuserprincipalname** に変更し、**保存**します。

    ![[ユーザー属性とクレーム] の最初の設定](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. リボンを使用して **[SAML ベースのサインオン]** に戻ります。これで、 **[ユーザー属性とクレーム]** セクションは次のようになります。 

    ![[ユーザー属性とクレーム] の最終的な設定](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)  

    > [!NOTE]
    > この設定では、姓と名を指定する必要はありません。

1. Azure portal の左側のペインで、 **[Azure Active Directory]** を選択し、 **[ユーザー]** を選択します。

1. **[新しいゲスト ユーザー]** をクリックします

1. **[Invite User]\(ユーザーを招待する\)** を選択し、ユーザーのプロパティを入力して、 **[招待する]** をクリックします。

1. これで、サイトを MyGuestAccount@outlook.com と共有し、このユーザーにアクセスを許可することができます。

    ![ゲスト アカウントとのサイトの共有](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configuring-the-trusted-identity-provider-for-multiple-web-applications"></a>複数の Web アプリケーション用に信頼できる ID プロバイダーを構成する

構成は 1 つの Web アプリケーションに機能しますが、複数の Web アプリケーションに対して同じ信頼できる ID プロバイダーを使用する場合は、追加の構成が必要です。 たとえば、URL `https://sales.contoso.com` を使用するように Web アプリケーションを拡張し、`https://marketing.contoso.com` にユーザーを認証する必要があるとします。 このためには、WReply パラメーターを有効にするように ID プロバイダーを更新し、Azure AD でアプリケーションの登録を更新して応答 URL を追加する必要があります。

1. Azure portal で Azure AD ディレクトリを開きます。 **[エンタープライズ アプリケーション]** をクリックします。**前の手順で作成したエンタープライズ アプリケーションの名前**をクリックし、 **[シングル サインオン]** をクリックします。

2. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** を編集します。

    ![[基本的な SAML 構成] を編集する](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

3. **[応答 URL (Assertion Consumer Service URL)]** で、追加の Web アプリケーションの URL を追加し、 **[保存]** をクリックします。

    ![[基本的な SAML 構成] を編集する](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

4. SharePoint サーバーで **SharePoint 201x 管理シェル**を開き、以前に使用した信頼できる ID トークンの発行者の名前を使用して、次のコマンドを実行します。
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
5. 全体管理では、Web アプリケーションに移動し、既存の信頼できる ID プロバイダーを有効にします。

オンプレミス SharePoint へのアクセス権を内部ユーザーに付与することが必要になるシナリオも考えられます。このシナリオでは、Microsoft Azure Active Directory Connect をデプロイして、オンプレミスのユーザーを Azure Active Directory と同期できるようにする必要があります。この設定は別の記事に含まれています。 

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure Active Directory でのハイブリッド ID とは](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
