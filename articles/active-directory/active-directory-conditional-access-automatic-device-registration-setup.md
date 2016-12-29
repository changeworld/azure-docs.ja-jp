---
title: "Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の構成方法 | Microsoft Docs"
description: "ドメイン参加済み Windows デバイスを自動的かつサイレントに Azure Active Directory に登録するための設定を行います。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: f26ca6e30e3070b2d2ee1861b88c52013158ba03
ms.openlocfilehash: 45f52d4e02896d38726552adbe81261551151683


---
# <a name="how-to-configure-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の構成方法

[Azure Active Directory のデバイスベースの条件付きアクセス](active-directory-conditional-access.md)を使用するためには、コンピューターを Azure Active Directory (Azure AD) に登録する必要があります。 この記事では、ユーザーの組織において、Azure AD による Windows ドメイン参加デバイスの自動登録を構成する手順について説明します。

> [!NOTE]
> 強化された Azure AD のユーザー エクスペリエンスは、Windows 10 November Update で一部対応していますが、デバイス ベースの条件付きアクセスが完全にサポートされるのは Windows 10 Anniversary Update からとなります。 条件付きアクセスの詳細については、[Azure Active Directory デバイスベースの条件付きアクセス](active-directory-conditional-access.md)に関する記事を参照してください。 Windows 10 デバイスの仕事への利用と Windows 10 デバイスを Azure AD に登録する方法について詳しくは、[エンタープライズ向け Windows 10 デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)に関するページをご覧ください。
> 
> 

Windows を実行しているデバイスの場合は、以前のバージョンの Windows を登録することもできます。対応しているバージョンを次に示します。

- Windows 8.1
- Windows 7

Windows Server を実行しているデバイスの場合は、次のプラットフォームを登録できます。

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2



## <a name="prerequisites"></a>前提条件

Azure AD を使用したドメイン参加済みデバイスの自動登録にはまず、最新バージョンの Azure Active Directory Connect (Azure AD Connect) が必要となります。

Azure AD Connect のデプロイ方法 (簡易またはカスタム インストール、あるいはインプレース アップグレード) によっては、次の前提条件が自動的に構成されている場合があります。

- Azure AD へ登録するコンピューターによる、Azure AD のテナント情報検出用の**オンプレミスの Active Directory のサービス接続ポイント**。
 
- 登録時のコンピューター認証用の **Active Directory フェデレーション サービス (AD FS) 発行変換規則** (フェデレーション構成に適用)。

ドメインに参加していない Windows 10 デバイスが組織内にある場合、次の手順を実行します。

* ユーザーがデバイスを登録できるように Azure AD のポリシーを設定する
* AD FS の多要素認証に代わる有効な選択肢として統合 Windows 認証 (IWA) を設定する

## <a name="step-1-configure-service-connection-point"></a>手順 1: サービス接続ポイントの構成 

コンピューターのドメインの構成名前付けコンテキストのパーティションに、サービス接続ポイント (SCP) オブジェクトが存在していなければなりません。 サービス接続ポイントには、コンピューターの登録先となる Azure AD テナントに関する検出情報が保持されます。 マルチ フォレストの Active Directory 構成では、ドメイン参加済みコンピューターが存在するすべてのフォレストにサービス接続ポイントが存在している必要があります。

SCP は次の場所にあります。  

**CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[ユーザーの構成名前付けコンテキスト]**

Active Directory ドメイン名が *example.com* であるフォレストの場合、構成名前付けコンテキストは次のようになります。  

**CN=Configuration,DC=example,DC=com**

次の Windows PowerShell スクリプトを使用して、オブジェクトの存在を確認し、検出値を取得することができます。 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com";

    $scp.Keywords;

**$scp.Keywords** の出力は次のような Azure AD テナント情報を示します。

azureADName:microsoft.com  
azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

サービス接続ポイントが存在しない場合は、Azure AD Connect サーバーで次の PowerShell スクリプトを実行して作成してください。

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;



**解説:**

- **$aadAdminCred = Get-Credential** を実行するときに、ユーザー名を入力する必要があります。 ユーザー名には次の形式を使用します。**user@example.com** 


- **Initialize-ADSyncDomainJoinedComputerSync** コマンドレットを実行するときは、[*connector account name*] を、Active Directory コネクタ アカウントで使用するドメイン アカウントに置き換えます。
  
- このコマンドレットには Active Directory PowerShell モジュールが使用されていますが、そのモジュールは、ドメイン コント ローラーの Active Directory Web サービスに依存しています。 Active Directory Web サービスは、Windows Server 2008 R2 以降のドメイン コントローラーでサポートされます。 ドメイン コントローラーが Windows Server 2008 以前のバージョンである場合は、PowerShell から System.DirectoryServices API を使用してサービス接続ポイントを作成したうえで、Keywords の値を代入してください。
 
 



##<a name="step-2-register-your-devices"></a>手順 2: デバイスの登録

デバイスを登録するための適切な方法は、組織がフェデレーションされているかどうかによって異なります。 


### <a name="device-registration-in-non-federated-organizations"></a>非フェデレーション組織でのデバイスの登録

非フェデレーション組織内のデバイスの登録は、次に該当する場合にのみサポートされます。

- デバイスで Windows 10 または Windows Server 2016 を実行している
- デバイスがドメインに参加している
- Azure AD Connect を使用したパスワード同期が有効になっている

これらすべての要件が満たされている場合は、デバイスを登録するために必要な処理はありません。  


### <a name="device-registration-in-federated-organizations"></a>フェデレーション組織でのデバイスの登録

Azure AD のフェデレーション構成では、Azure AD に対するデバイスの認証に AD FS (またはオンプレミスのフェデレーション サーバー) が使用されます。 デバイスは Azure Active Directory Device Registration サービスに登録されます。

Windows 10 コンピューターと Windows Server 2016 コンピューターに関しては、Azure AD 内のデバイス オブジェクトとオンプレミスのコンピューター アカウント オブジェクトとが、Azure AD Connect によって関連付けられます。 Azure AD Device Registration サービスが登録処理を完了してデバイス オブジェクトを作成するためには、認証時に次の要求が存在している必要があります。

- **http://schemas.microsoft.com/ws/2012/01/accounttype** - DJ 値を保持します。プリンシパルの認証子は、DJ 値によってドメイン参加済みコンピューターとして識別されます。

- **http://schemas.microsoft.com/identity/claims/onpremobjectguid** - オンプレミス コンピューター アカウントの **objectGUID** 属性の値を保持します。
 
- **http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid** - コンピューターのプライマリ セキュリティ ID (SID) を保持します。この ID は、オンプレミス コンピューター アカウントの **objectSid** 属性の値と対応します。

- **http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid** - AD FS から発行されたトークンやオンプレミスの Security Token Service (STS) から発行されたトークンを信頼するために Azure AD が使用する値を保持します。 これは、Azure AD にいくつかの確認済みのドメインがある場合に重要です。 AD FS では、**http://\<*domain-name*\>/adfs/services/trust/** を使用してください。**\<domain-name\>** には、Azure AD の確認済みのドメイン名が入ります。

確認済みのドメイン名の詳細については、「[Azure Active Directory へのカスタム ドメイン名の追加](active-directory-add-domain.md)」を参照してください。  
確認済みの会社のドメインの一覧を取得するには、[Get-msoldomain](https://docs.microsoft.com/powershell/msonline/v1/get-msoldomain) コマンドレットを使用できます。 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)


Windows 10 および Windows Server 2016 ドメインに参加しているコンピューターでは、Windows 統合認証を使用して、AD FS でホストされているアクティブな WS-Trust エンドポイントに対する認証を実行します。 このエンドポイントが有効になっていることを確認します。 Web 認証プロキシを使用している場合は、このエンドポイントがプロキシ経由で公開されていることも確認します。 エンドポイントは、**adfs/services/trust/13/windowstransport** です。 

AD FS 管理コンソールの **[サービス] の [エンドポイント]** に有効である必要があります。 オンプレミスのフェデレーション サーバーとして AD FS を使用していない場合は、ベンダーの指示に従って対応するエンドポイントが有効になっていることを確認します。 



> [!NOTE]
> オンプレミスのフェデレーション サーバーに AD FS を使用していない場合は、ご利用のベンダーの指示に従って、これらの要求の発行規則を作成してください。
> 
> 




**AD FS で規則を手動で作成するには、次の手順を実行します。**

- 次の Windows PowerShell スクリプトのいずれかを選択します。 
- サーバーに接続されているセッションで Windows PowerShell スクリプトを実行します。 
- 先頭行を、Azure AD における組織の確認済みドメイン名で置き換えます。




#### <a name="setting-ad-fs-rules-in-a-single-domain-environment"></a>単一ドメイン環境での AD FS 規則の設定

**確認済みのドメインが 1 つ**だけの場合、次のスクリプトを使用して AD FS 規則を追加します。


    <#----------------------------------------------------------------------
    |   Modify the Azure AD Relying Party to include the claims needed
    |   for DomainJoin++. The rules include:
    |   -ObjectGuid
    |   -AccountType
    |   -ObjectSid
    +---------------------------------------------------------------------#>

    $rule1 = '@RuleName = "Issue object GUID" 

    c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

    c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

    $rule2 = '@RuleName = "Issue account type for domain joined computers" 

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

    $rule3 = '@RuleName = "Pass through primary SID" 

    c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

    c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(claim = c2);' 

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 


#### <a name="setting-ad-fs-rules-in-a-multi-domain-environment"></a>複数ドメイン環境での AD FS 規則の設定

検証済みのドメインが複数存在する場合は、次の手順を実行します。

1. Azure AD Connect によって作成された、既存の **IssuerID** 規則を削除します。  
この規則の例を次に示します: c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 


2. このスクリプトを実行します。 

        <#----------------------------------------------------------------------  
        |   Modify the Azure AD Relying Party to include the claims needed  
        |   for DomainJoin++. The rules include:
        |   -ObjectGuid
        |   -AccountType
        |   -ObjectSid
        +---------------------------------------------------------------------#>

        $VerifiedDomain = 'example.com'      # Replace example.com with one of your verified domains

        $rule1 = '@RuleName = "Issue object GUID" 

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

        $rule2 = '@RuleName = "Issue account type for domain joined computers" 

        c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

        $rule3 = '@RuleName = "Pass through primary SID" 

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(claim = c2);' 

        $rule4 = '@RuleName = "Issue AccountType with the value User when its not a computer account" 

        NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]) 

        => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");' 

        $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID" 

        c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));' 

        $rule6 = '@RuleName = "Update issuer for DJ computer auth" 

        c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$VerifiedDomain+'/adfs/services/trust/");' 

        $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

        $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4+ $rule5+  $rule6 

        $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

        Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 



## <a name="step-3-setup-ad-fs-for-authentication-of-device-registration"></a>手順 3: デバイス登録の認証に必要な AD FS の設定

デバイス登録に関して、多要素認証に代わる有効な選択肢として WIA が AD FS で設定されていることを確認してください。 そのためには、この認証方法をパスする発行変換規則を用意しておく必要があります。

1. AD FS 管理コンソールで **[AD FS]** > **[信頼関係]** > **[証明書利用者信頼]** に移動します。
2. [Microsoft Office 365 ID プラットフォーム] 証明書利用者信頼オブジェクトを右クリックし、 **[要求規則の編集]**を選択します。
3. **[発行変換規則]** タブで、**[規則の追加]** を選択します。
4. **[要求規則]** テンプレート一覧から **[カスタム規則を使用して要求を送信]** を選択します。
5. **[次へ]**を選択します。
6. **[要求規則名]** ボックスに「**Auth Method Claim Rule**」と入力します。
7. **[要求規則]** ボックスに、この規則を入力します。  
**c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);**
8. ご利用のフェデレーション サーバーで、次の PowerShell コマンドを入力します。
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

**\<RPObjectName\>** は、Azure AD 証明書利用者信頼オブジェクトの証明書利用者オブジェクト名です。 通常、このオブジェクトは "**Microsoft Office 365 ID プラットフォーム**" という名前です。



##<a name="step-4-deployment-and-rollout"></a>手順 4: デプロイとロールアウト

ドメイン参加済みコンピューターは、以上の前提条件を満たした時点で Azure AD に登録することができます。

ドメイン参加済み Windows 10 Anniversary Update コンピューターおよび Windows Server 2016 コンピューターは、次回デバイスを再起動したときか、ユーザーが Windows にサインインしたときに Azure AD に自動的に登録されます。 新しくドメインに参加したコンピューターは、ドメインへの参加処理の後、そのデバイスを再起動したときに Azure AD に登録されます。

> [!NOTE]
> ドメイン参加済み Windows 10 コンピューターを Azure AD に自動登録するためには、ロールアウト グループ ポリシー オブジェクトが設定されている必要があります。
> 
> 

ドメイン参加済み Windows 10 コンピューターおよび Windows Server 2016 コンピューターについては、自動登録のロールアウトをグループ ポリシー オブジェクトで制御することができます。 

ドメイン参加済みコンピューターが Windows 10 以外である場合は、対象となるコンピューターを選んで Windows インストーラー パッケージをデプロイすることで自動登録をロールアウトすることができます。

> [!NOTE]
> ドメイン参加済みコンピューターが Windows 8.1 の場合も、同じロールアウト制御用のグループ ポリシーによって登録処理が開始されます。 ドメイン参加済み Windows 8.1 コンピューターの登録には、このポリシーを使用することができます。 Windows 7 や各種バージョンの Windows Server など、Windows のバージョンが混在する場合は、Windows 10 と Windows Server 2016 を除くすべてのコンピューターを Windows インストーラー パッケージを使って登録してもかまいません。
> 
> 

### <a name="create-a-group-policy-object-to-control-the-rollout-of-automatic-registration"></a>自動登録のロールアウトを制御するグループ ポリシー オブジェクトの作成

ドメイン参加済みコンピューターを対象に Azure AD で自動登録のロールアウトを制御する場合は、登録対象のコンピューターに **[ドメインに参加しているコンピューターをデバイスとして登録する]** というグループ ポリシーをデプロイしてください。 たとえば、このポリシーを組織単位やセキュリティ グループにデプロイすることができます。

**ポリシーを設定するには、次の手順を実行します。**

1. サーバー マネージャーを開き、**[ツール]** > **[グループ ポリシーの管理]** に移動します。
2. Windows 10 コンピューターまたは Windows Server 2016 コンピューターの自動登録を有効にするドメインのノードに移動します。
3. **[グループ ポリシー オブジェクト]** を右クリックし、**[新規]** を選択します。
4. グループ ポリシー オブジェクトの名前を入力します。 たとえば、「*Automatic Registration to Azure AD*」と入力します。 **[OK]**を選択します。
5. 新しいグループ ポリシー オブジェクトを右クリックし、**[編集]**を選択します。
6. **[コンピューターの構成]** > **[ポリシー]** > **[管理用テンプレート]** > **[Windows コンポーネント]** > **[デバイスの登録]** に移動します。 **[ドメインに参加しているコンピューターをデバイスとして登録する]** を右クリックし、**[編集]** を選択します。
   
   > [!NOTE]
   > このグループ ポリシー テンプレートは、以前のバージョンのグループ ポリシー管理コンソールから変更されています。 以前のバージョンのコンソールを使用している場合は、**[コンピューターの構成]** > **[ポリシー]** > **[管理用テンプレート]** > **[Windows コンポーネント]** > **[社内参加]** > **[クライアント コンピューターを自動的に社内参加する]** に移動します。
   > 
   > 
7. **[有効]** を選択し、**[適用]** を選択します。
8. **[OK]**を選択します。
9. グループ ポリシー オブジェクトを選択した場所にリンクします。 たとえば、特定の組織単位に関連付けることができます。 また、Azure AD に自動登録するコンピューターの特定のセキュリティ グループに関連付けることもできます。 組織内のすべてのドメイン参加済み Windows 10 コンピューターおよび Windows Server 2016 コンピューターに対してこのポリシーを設定するには、このグループ ポリシー オブジェクトをドメインに関連付けてください。

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>非 Windows 10 コンピューター用の Windows インストーラー パッケージ
フェデレーション環境で Windows 8.1、Windows 7、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 のいずれかを実行しているドメイン参加コンピューターを登録するには、次の Windows インストーラー パッケージ (.msi) ファイルをダウンロードしてインストールします。

* [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
* [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

このパッケージのデプロイには、System Center Configuration Manager などのソフトウェア ディストリビューション システムを使用します。 パッケージは、*quiet* パラメーターを使用する、標準のサイレント インストール オプションをサポートしています。 System Center Configuration Manager 2016 には、完了した登録を追跡する機能など、以前のバージョンにはない利点が追加されています。 詳細については、 [System Center 2016](https://www.microsoft.com/en-us/cloud-platform/system-center)に関するページを参照してください。

インストーラーは、スケジュールされたタスクを、ユーザーのコンテキストで実行されるシステムに作成します。 このタスクは、ユーザーが Windows にサインインするとトリガーされます。 IWA によってユーザーの認証が行われた後、その資格情報を使ってサイレントにデバイスが Azure AD に登録されます。 スケジュールされたタスクを確認するには、**[Microsoft]** > **[社内参加]** に移動し、タスク スケジューラ ライブラリにアクセスしてください。

## <a name="next-steps"></a>次のステップ
* [Azure Active Directory の条件付きアクセス](active-directory-conditional-access.md)




<!--HONumber=Nov16_HO4-->


