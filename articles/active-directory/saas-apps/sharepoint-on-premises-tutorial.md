---
title: チュートリアル:Azure Active Directory と SharePoint オンプレミスの統合 | Microsoft Docs
description: Azure Active Directory と SharePoint オンプレミスの間のフェデレーション認証を実装する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
ms.openlocfilehash: d168c2c442fba70d021e90daeecce5844adfb274
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2021
ms.locfileid: "112463430"
---
# <a name="tutorial-implement-federated-authentication-between-azure-active-directory-and-sharepoint-on-premises"></a>チュートリアル: Azure Active Directory と SharePoint オンプレミスの間のフェデレーション認証を実装する

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、Azure Active Directory と SharePoint オンプレミスの間のフェデレーション認証を構成します。 ユーザーが Azure Active Directory にサインインし、その ID を使用して SharePoint オンプレミス サイトにアクセスできるようにすることが目標です。

## <a name="prerequisites"></a>前提条件

構成を実行するには、次のリソースが必要です。
* Azure Active Directory テナント。 アカウントがない場合は、[無料アカウントを作成する](https://azure.microsoft.com/free/)ことができます。
* SharePoint 2013 以降のファーム。

この記事では、次の値を使用しています。
- エンタープライズ アプリケーション名 (Azure AD 内): `SharePoint corporate farm`
- 信頼識別子 (Azure AD 内) と領域 (SharePoint 内): `urn:sharepoint:federation`
- loginUrl (Azure AD へ): `https://login.microsoftonline.com/dc38a67a-f981-4e24-ba16-4443ada44484/wsfed`
- SharePoint サイトの URL: `https://spsites.contoso.local/`
- SharePoint サイトの応答 URL: `https://spsites.contoso.local/_trust/`
- SharePoint の信頼の構成名: `AzureADTrust`
- Azure AD テスト ユーザーの UserPrincipalName: `AzureUser1@demo1984.onmicrosoft.com`

## <a name="configure-an-enterprise-application-in-azure-active-directory"></a>Azure Active Directory でエンタープライズ アプリケーションを構成する

Azure AD でフェデレーションを構成するためには、専用のエンタープライズ アプリケーションを作成する必要があります。 その構成は、アプリケーション ギャラリーにあるあらかじめ構成されているテンプレート `SharePoint on-premises` を使用することで簡単に行うことができます。

### <a name="create-the-enterprise-application"></a>エンタープライズ アプリケーションを作成する

1. [Azure Active Directory ポータル](https://aad.portal.azure.com/)にサインインします。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部の **[新しいアプリケーション]** を選択します。
1. 検索ボックスに、「**SharePoint on-premises**」と入力します。 結果ペインで **[SharePoint on-premises]\(SharePoint オンプレミス\)** を選択します。
1. アプリケーションの名前 (このチュートリアルでは `SharePoint corporate farm`) を指定し、 **[作成]** をクリックしてアプリケーションを追加します。
1. 新しいエンタープライズ アプリケーションで **[プロパティ]** を選択し、 **[ユーザーの割り当てが必要ですか?]** の値を確認します。 このシナリオでは、その値を **[いいえ]** に設定し、 **[保存]** をクリックします。

### <a name="configure-the-enterprise-application"></a>エンタープライズ アプリケーションを構成する

このセクションでは、SAML 認証を構成し、認証の成功時に SharePoint に送信されるクレームを定義します。

1. エンタープライズ アプリケーション `SharePoint corporate farm` の [概要] で、 **[2. シングル サインオンの設定]** を選択し、次のダイアログで **[SAML]** を選択します。
 
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** ペインの **編集** アイコンを選択します。

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    1. **[識別子]** ボックスに、`urn:sharepoint:federation` という値が存在することを確認します。

    1. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します: `https://spsites.contoso.local/_trust/`。

    1. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します: `https://spsites.contoso.local/`。
    
    1. **[保存]** を選択します。

1. **[User Attributes & Claims]\(ユーザー属性とクレーム\)** セクションで、不要な次のクレームの種類を削除します。SharePoint がアクセス許可を付与する際に、これらは使用されません。
    - `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`
    - `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`
    - `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

1. 設定は次のように表示されます。

    ![基本的な SAML 設定](./media/sharepoint-on-premises-tutorial/azure-active-directory-app-saml-ids.png)

1. この情報をコピーします。後で SharePoint で必要になります。

    - **[SAML 署名証明書]** セクションで、**証明書 (Base64)** を **ダウンロード** します。 これは、Azure AD が SAML トークンへの署名に使用する署名証明書の公開キーです。 SharePoint が、受け取った SAML トークンの整合性を検証する際に必要となります。

    - **[Set up SharePoint corporate farm]\(SharePoint 企業ファームのセットアップ\)** セクションで、 **[ログイン URL]** をメモ帳にコピーします。末尾の文字列 **/saml2** は **/wsfed** に置き換えてください。
     
    > [!IMPORTANT]
    > SharePoint によって必要とされる SAML 1.1 トークンが Azure AD から確実に発行されるよう、 **/saml2** は必ず **/wsfed** に置き換えてください。

    - **[Set up SharePoint corporate farm]\(SharePoint 企業ファームのセットアップ\)** セクションで、 **[ログアウト URL]** をコピーします。

## <a name="configure-sharepoint-to-trust-azure-active-directory"></a>Azure Active Directory を信頼するように SharePoint を構成する

### <a name="create-the-trust-in-sharepoint"></a>SharePoint で信頼を作成する

この手順では、SharePoint が Azure AD を信頼するために必要な構成を格納する SPTrustedLoginProvider を作成します。 そのためには、先ほどコピーした Azure AD の情報が必要です。 SharePoint 管理シェルを起動し、次のスクリプトを実行して作成します。

```powershell
# Path to the public key of the Azure AD SAML signing certificate (self-signed), downloaded from the Enterprise application in the Azure AD portal
$signingCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("C:\AAD app\SharePoint corporate farm.cer")
# Unique realm (corresponds to the "Identifier (Entity ID)" in the Azure AD Enterprise application)
$realm = "urn:sharepoint:federation"
# Login URL copied from the Azure AD enterprise application. Make sure to replace "saml2" with "wsfed" at the end of the URL:
$loginUrl = "https://login.microsoftonline.com/dc38a67a-f981-4e24-ba16-4443ada44484/wsfed"

# Define the claim types used for the authorization
$userIdentifier = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
$role = New-SPClaimTypeMapping "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming

# Let SharePoint trust the Azure AD signing certificate
New-SPTrustedRootAuthority -Name "Azure AD signing certificate" -Certificate $signingCert

# Create a new SPTrustedIdentityTokenIssuer in SharePoint
$trust = New-SPTrustedIdentityTokenIssuer -Name "AzureADTrust" -Description "Azure AD" -Realm $realm -ImportTrustCertificate $signingCert -ClaimsMappings $userIdentifier, $role -SignInUrl $loginUrl -IdentifierClaim $userIdentifier.InputClaimType
```

### <a name="configure-the-sharepoint-web-application"></a>SharePoint Web アプリケーションを構成する

この手順では、先ほど作成した Azure AD エンタープライズ アプリケーションを信頼するように SharePoint の Web アプリケーションを構成します。 その際に考慮すべき重要なルールは次のとおりです。
- SharePoint Web アプリケーションの既定のゾーンでは、Windows 認証が有効になっている必要があります。 これは検索クローラーの要件となります。
- Azure AD 認証を使用する SharePoint URL は、HTTPS を使用して設定されている必要があります。

1. Web アプリケーションを作成または拡張します。 この記事では、想定される 2 つの構成について説明します。

    - 既定のゾーンで Windows 認証と Azure AD 認証の両方を使用する新しい Web アプリケーションを作成した場合:

        1. **SharePoint 管理シェル** を起動し、次のスクリプトを実行します。
            ```powershell
            # This script creates a new web application and sets Windows and Azure AD authentication on the Default zone
            # URL of the SharePoint site federated with Azure AD
            $trustedSharePointSiteUrl = "https://spsites.contoso.local/"
            $applicationPoolManagedAccount = "Contoso\spapppool"

            $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$true
            $sptrust = Get-SPTrustedIdentityTokenIssuer "AzureADTrust"
            $trustedAp = New-SPAuthenticationProvider -TrustedIdentityTokenIssuer $sptrust    
            
            New-SPWebApplication -Name "SharePoint - Azure AD" -Port 443 -SecureSocketsLayer -URL $trustedSharePointSiteUrl -ApplicationPool "SharePoint - Azure AD" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp, $trustedAp
            ```
        1. **[SharePoint サーバーの全体管理]** サイトを開きます。
        1. **[システム設定]** で、**[代替アクセス マッピングの構成]** を選択します。 **[代替アクセス マッピング コレクション]** ボックスが開きます。
        1. 新しい Web アプリケーションで表示をフィルター処理し、次のような内容が表示されることを確認します。
    
           ![Web アプリケーションの代替アクセス マッピング](./media/sharepoint-on-premises-tutorial/sp-alternate-access-mappings-new-web-app.png)

    - 新しいゾーンで Azure AD 認証を使用するように既存の Web アプリケーションを拡張した場合:

        1. SharePoint 管理シェルを起動し、次のスクリプトを実行します。

            ```powershell
            # This script extends an existing web application to set Azure AD authentication on a new zone
            # URL of the default zone of the web application
            $webAppDefaultZoneUrl = "http://spsites/"
            # URL of the SharePoint site federated with ADFS
            $trustedSharePointSiteUrl = "https://spsites.contoso.local/"
            $sptrust = Get-SPTrustedIdentityTokenIssuer "AzureADTrust"
            $ap = New-SPAuthenticationProvider -TrustedIdentityTokenIssuer $sptrust
            $wa = Get-SPWebApplication $webAppDefaultZoneUrl
            
            New-SPWebApplicationExtension -Name "SharePoint - Azure AD" -Identity $wa -SecureSocketsLayer -Zone Internet -Url $trustedSharePointSiteUrl -AuthenticationProvider $ap
            ```
        
        1. **[SharePoint サーバーの全体管理]** サイトを開きます。
        1. **[システム設定]** で、**[代替アクセス マッピングの構成]** を選択します。 **[代替アクセス マッピング コレクション]** ボックスが開きます。
        1. 拡張された Web アプリケーションで表示をフィルター処理し、次のような内容が表示されることを確認します。
    
            ![拡張された Web アプリケーションの代替アクセス マッピング](./media/sharepoint-on-premises-tutorial/sp-alternate-access-mappings-extended-zone.png)

Web アプリケーションが作成されたら、ルート サイト コレクションを作成し、自分の Windows アカウントをプライマリ サイト コレクション管理者として追加します。

1. SharePoint サイトの証明書を作成する

    SharePoint URL では HTTPS プロトコル (`https://spsites.contoso.local/`) が使用されるため、対応するインターネット インフォメーション サービス (IIS) サイトで証明書を設定する必要があります。 その手順に従って、自己署名証明書を生成してください。
    
    > [!IMPORTANT]
    > 自己署名証明書はテスト目的にのみ適しています。 運用環境では、代わりに証明機関が発行した証明書を使用することを強くお勧めします。
    
    1. Windows PowerShell コンソールを開きます。
    1. 次のスクリプトを実行して自己署名証明書を生成し、それをコンピューターの MY ストアに追加します。
    
        ```powershell
        New-SelfSignedCertificate -DnsName "spsites.contoso.local" -CertStoreLocation "cert:\LocalMachine\My"
        ```
    
1. IIS サイトで証明書を設定します。
    1. インターネット インフォメーション サービス マネージャー コンソールを開きます。
    1. ツリー ビューでサーバーを展開し、 **[サイト]** を展開し、 **[SharePoint - Azure AD]** サイトを選択して **[バインド]** を選択します。
    1. **https バインド** を選択して、**[編集]** を選択します。
    1. [TLS/SSL 証明書] フィールドで、使用する証明書 (先ほど作成した **spsites.contoso.local** など) を選択し、 **[OK]** を選択します。
    
    > [!NOTE]
    > Web フロント エンド サーバーが複数ある場合、この操作を各サーバーで繰り返す必要があります。

SharePoint と Azure AD の間の信頼に関する基本的な構成は以上です。 Azure Active Directory ユーザーとして SharePoint サイトにサインインする方法を見てみましょう。

## <a name="sign-in-as-a-member-user"></a>メンバー ユーザーとしてサインインする

Azure Active Directory には、[2 種類のユーザー](../external-identities/user-properties.md)が存在します。ゲスト ユーザーとメンバー ユーザーです。 まずは、メンバー ユーザーから見ていきましょう。メンバー ユーザーとは、単に自分の組織に属しているユーザーのことです。

### <a name="create-a-member-user-in-azure-active-directory"></a>Azure Active Directory にメンバー ユーザーを作成する

1. Azure portal の左端のペインで、 **[Azure Active Directory]** を選択します。 **[管理]** ペインで **[ユーザー]** を選択します。

1. 画面の上部で **[すべてのユーザー]**  >  **[新しいユーザー]** を選択します。

1. **[ユーザーの作成]** を選択し、ユーザーのプロパティで次の手順を実行します。

    1. **[名前]** ボックスにユーザー名を入力します。 ここでは、「**TestUser**」を使用しました。
  
    1. **[ユーザー名]** ボックスに「`AzureUser1@<yourcompanytenant>.onmicrosoft.com`」と入力します。 この例では `AzureUser1@demo1984.onmicrosoft.com` としています。

       ![[ユーザー] ダイアログ ボックス](./media/sharepoint-on-premises-tutorial/azure-active-directory-new-user.png)

    1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示される値を書き留めます。

    1. **［作成］** を選択します

    1. これで、サイトを `AzureUser1@demo1984.onmicrosoft.com` と共有し、このユーザーにアクセスを許可することができます。

### <a name="grant-permissions-to-the-azure-active-directory-user-in-sharepoint"></a>SharePoint で Azure Active Directory ユーザーにアクセス許可を付与する

SharePoint ルート サイト コレクションに自分の Windows アカウント (サイト コレクション管理者) としてサインインし、 **[共有]** をクリックします。  
ダイアログには、userprincipalname の正確な値、たとえば「`AzureUser1@demo1984.onmicrosoft.com`」を入力する必要があります。また、**name** クレームの結果を慎重に選択してください (結果にマウスを合わせると、そのクレームの種類が表示されます)。

> [!IMPORTANT]
> 招待したいユーザーの値を正確に入力し、適切なクレームの種類をリストから選択してください。そうしないと、共有がうまく機能しません。

![ユーザー ピッカーの結果 (AzureCP 不使用時)](./media/sharepoint-on-premises-tutorial/sp-people-picker-search-no-azurecp.png)

この制限は、ユーザー ピッカーからの入力が SharePoint によって検証されず、混乱やスペルミスを招いたり、ユーザーが誤って間違ったクレームの種類を選択したりすることがあるためです。  
このシナリオを修正するには、[AzureCP](https://yvand.github.io/AzureCP/) というオープンソース ソリューションを使用して、SharePoint 2019、2016、2013 を Azure Active Directory に接続し、Azure Active Directory テナントに対して入力を解決します。 詳細については、「[AzureCP](https://yvand.github.io/AzureCP/)」を参照してください。

以下に示したのは、AzureCP が構成されている状態で同じ検索を実行した結果です。入力内容に基づいて実際のユーザーが SharePoint から返されます。

![ユーザー ピッカーの結果 (AzureCP 使用時)](./media/sharepoint-on-premises-tutorial/sp-people-picker-search-with-azurecp.png)

> [!IMPORTANT]
> AzureCP は Microsoft 製品ではないため、Microsoft サポートではサポートされません。 オンプレミスの SharePoint ファームで AzureCP をダウンロード、インストール、構成するには、[AzureCP](https://yvand.github.io/AzureCP/) Web サイトを参照してください。 

これで Azure Active Directory ユーザー `AzureUser1@demo1984.onmicrosoft.com` が、自分の ID を使用して SharePoint サイト `https://spsites.contoso.local/` にサインインできるようになりました。

## <a name="grant-permissions-to-a-security-group"></a>セキュリティ グループにアクセス許可を付与する

### <a name="add-the-group-claim-type-to-the-enterprise-application"></a>グループ クレームの種類をエンタープライズ アプリケーションに追加する

1. エンタープライズ アプリケーション `SharePoint corporate farm` の [概要] で、 **[2. シングル サインオンの設定]** を選択します。 

1. グループ クレームが存在しない場合は、 **[User Attributes & Claims]\(ユーザー属性とクレーム\)** セクションで、これらの手順に従います。

    1. **[グループ要求を追加する]** を選択し、 **[セキュリティ グループ]** を選択して、 **[ソース属性]** を **[グループ ID]** に設定します。
    1. **[グループ要求の名前をカスタマイズする]** チェック ボックスをオンにし、 **[グループをロール要求として生成する]** チェック ボックスをオンにして、 **[保存]** をクリックします。
    1. **[User Attributes & Claims]\(ユーザー属性とクレーム\)** は次のようになります。

    ![ユーザーとグループのクレーム](./media/sharepoint-on-premises-tutorial/azure-active-directory-claims-with-group.png)

### <a name="create-a-security-group-in-azure-active-directory"></a>Azure Active Directory でセキュリティ グループを作成する

Azure Active Directory でセキュリティ グループを作成しましょう。

1. **[Azure Active Directory]**  >  **[グループ]** を選択します。

1. **[新しいグループ]** を選びます。

1. **[グループの種類]** (Security)、 **[グループ名]** (`AzureGroup1` など)、 **[メンバーシップの種類]** を入力します。 先ほど作成したユーザーをメンバーとして追加し、 **[作成]** をクリックします。

    ![Azure AD セキュリティ グループを作成する](./media/sharepoint-on-premises-tutorial/azure-active-directory-new-group.png)
  
### <a name="grant-permissions-to-the-security-group-in-sharepoint"></a>SharePoint でセキュリティ グループにアクセス許可を付与する

Azure AD のセキュリティ グループは、その `Id` 属性、つまり GUID (`E89EF0A3-46CC-45BF-93A4-E078FCEBFC45` など) で識別されます。  
カスタム クレーム プロバイダーを使用しなかった場合、ユーザーは、グループの正確な値 (`Id`) をユーザー ピッカーで入力し、対応するクレームの種類を選択しなければなりません。 これはユーザー フレンドリではなく、信頼性も高いとは言えません。  
それを避けるために、この記事では、サードパーティのクレーム プロバイダー [AzureCP](https://yvand.github.io/AzureCP/) を使用することで、グループを SharePoint から簡単に探し出せるようにしています。

![ユーザー ピッカーで Azure AD グループを検索する](./media/sharepoint-on-premises-tutorial/sp-people-picker-search-azure-active-directory-group.png)

## <a name="manage-guest-users-access"></a>ゲスト ユーザー アクセスを管理する

ゲスト アカウントには、次の 2 種類があります。

- B2B ゲスト アカウント: これらのユーザーは、外部の Azure Active Directory テナントに属します。
- MSA ゲスト アカウント: これらのユーザーは、Microsoft ID プロバイダー (Hotmail、Outlook) またはソーシャル アカウント プロバイダー (Google など) に属します。

既定では、Azure Active Directory によって、"一意のユーザー ID" とクレームの "名前" がどちらも `user.userprincipalname` 属性に設定されます。  
あいにくゲスト アカウントでは、この属性は、あいまいさが生じます。次の表をご覧ください。

| Azure AD で設定されたソース属性 | Azure AD で使用される実際のプロパティ (B2B ゲストの場合) | Azure AD で使用される実際のプロパティ (MSA ゲストの場合) | SharePoint が ID の検証に利用できるプロパティ |
|--|--|--|--|
| `user.userprincipalname` | `mail` (例: `guest@PARTNERTENANT`) | `userprincipalname` (例: `guest_outlook.com#EXT#@TENANT.onmicrosoft.com`) | あいまい |
| `user.localuserprincipalname` | `userprincipalname` (例: `guest_PARTNERTENANT#EXT#@TENANT.onmicrosoft.com`) | `userprincipalname` (例: `guest_outlook.com#EXT#@TENANT.onmicrosoft.com`) | `userprincipalname` |

結論として、すべてのゲスト アカウントを確実に同じ属性で識別するためには、`user.userprincipalname` ではなく `user.localuserprincipalname` 属性を使用するように、エンタープライズ アプリケーションの ID クレームを更新する必要があります。

### <a name="update-the-application-to-use-a-consistent-attribute-for-all-guest-users"></a>すべてのゲスト ユーザーに対して一貫した属性を使用するようにアプリケーションを更新する

1. エンタープライズ アプリケーション `SharePoint corporate farm` の [概要] で、 **[2. シングル サインオンの設定]** を選択します。
 
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[User Attributes & Claims]\(ユーザー属性とクレーム\)** ペインの **編集** アイコンを選択します。

1. **[User Attributes & Claims]\(ユーザー属性とクレーム\)** セクションで、これらの手順に従います。

    1. **[一意のユーザー識別子 (名前 ID)]** を選択し、その **[ソース属性]** プロパティを **user.localuserprincipalname** に変更して、 **[保存]** をクリックします。
    
    1. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` を選択し、その **[ソース属性]** プロパティを **user.localuserprincipalname** に変更して、 **[保存]** をクリックします。
    
    1. **[User Attributes & Claims]\(ユーザー属性とクレーム\)** は次のようになります。
    
    ![ゲストのユーザー属性とクレーム](./media/sharepoint-on-premises-tutorial/azure-active-directory-claims-guests.png)

### <a name="invite-guest-users-in-sharepoint"></a>SharePoint でゲスト ユーザーを招待する

> [!NOTE]
> このセクションでは、クレーム プロバイダー AzureCP が使用されていることを前提としています。

上のセクションでは、すべてのゲスト アカウントに対して一貫した属性を使用するようにエンタープライズ アプリケーションを更新しました。  
今度は、その変更を反映し、ゲスト アカウントに `userprincipalname` 属性を使用するように、AzureCP の構成を更新する必要があります。

1. **[SharePoint サーバーの全体管理]** サイトを開きます。
1. **[セキュリティ]** で **[AzureCP global configuration]\(AzureCP グローバル構成\)** を選択します。
1. **[User identifier property]\(ユーザー識別子プロパティ\)** セクションで、 **[User identifier for 'Guest' users]\("ゲスト" ユーザーのユーザー識別子\)** を **UserPrincipalName** に設定します。
1. [OK] をクリックします。

![AzureCP のゲスト アカウントの構成](./media/sharepoint-on-premises-tutorial/sp-azurecp-attribute-for-guests.png)

SharePoint サイトでゲスト ユーザーを招待できるようになりました。

## <a name="configure-the-federation-for-multiple-web-applications"></a>複数の Web アプリケーションに対してフェデレーションを構成する

この構成は 1 つの Web アプリケーションに役立ちますが、複数の Web アプリケーションに対して同一の信頼できる ID プロバイダーを使用する場合は追加の構成が必要になります。 たとえば、別個の Web アプリケーション `https://otherwebapp.contoso.local/` があり、そのアプリケーションの Azure Active Directory 認証を有効にしたいとします。 そのためには、SAML WReply パラメーターを渡すように SharePoint を構成し、該当する URL をエンタープライズ アプリケーションに追加します。

### <a name="configure-sharepoint-to-pass-the-saml-wreply-parameter"></a>SAML WReply パラメーターを渡すように SharePoint を構成する

1. SharePoint サーバーで SharePoint 201x 管理シェルを開き、次のコマンドを実行します。 先ほど使用した信頼できる ID トークン発行者の名前を使用します。

```powershell
$t = Get-SPTrustedIdentityTokenIssuer "AzureADTrust"
$t.UseWReplyParameter = $true
$t.Update()
```

### <a name="add-the-urls-in-the-enterprise-application"></a>エンタープライズ アプリケーションに URL を追加する

1. Azure portal で、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** を選択します。 以前に作成したエンタープライズ アプリケーションの名前を選択し、 **[シングル サインオン]** を選択します。

1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** を編集します。

1. **[応答 URL (Assertion Consumer Service URL)]** セクションに、Azure Active Directory でユーザーをサインインさせるその他すべての Web アプリケーションの URL (例: `https://otherwebapp.contoso.local/`) を追加し、 **[保存]** をクリックします。

![追加の Web アプリケーションを指定する](./media/sharepoint-on-premises-tutorial/azure-active-directory-app-reply-urls.png)