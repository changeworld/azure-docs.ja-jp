<properties
	pageTitle="Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の設定方法 | Microsoft Azure"
	description="IT 管理者は、ドメイン参加済みの Windows デバイスをサイレント モードで自動的に Azure Active Directory (Azure AD) に登録できます。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/09/2016"
	ms.author="markvi"/>



# Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の設定方法 

Azure AD への Windows ドメイン参加コンピューターの登録は、[Azure Active Directory デバイスベースの条件付きアクセス](active-directory-conditional-access.md)を有効にするために必要です。

職場または学校のアカウントの使用を許可することによる Azure AD アプリへの SSO エクスペリエンスの向上、デバイス間の設定のエンタープライズ ローミング、ビジネス向け Windows ストアの活用、Windows Hello による強力な認証と便利なサインインなどの更新があります。

> [AZURE.NOTE] Windows 10 November 2015 Update は強化されたユーザー エクスペリエンスの一部をサポートしますが、デバイス ベースの条件付きアクセスを完全にサポートするのは Anniversary Update です。条件付きアクセスの詳細については、[Azure Active Directory ベースの条件付きアクセス](active-directory-conditional-access.md)に関する記事を参照してください。職場における Windows 10 デバイスと、Azure AD に登録することでユーザーが得られるエクスペリエンスの詳細については、「[エンタープライズ向け Windows 10: デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)」を参照してください。


登録は次に示す以前のバージョンの Windows でサポートされています。

- Windows 8.1

- Windows 8.0

- Windows 7

Windows Server のコンピューターをデスクトップとして使用する使用事例 (Windows Server をプライマリ コンピューターとして使用する場合など) では、次のプラットフォームを登録できます。

- Windows Server 2016

- Windows Server 2012 R2

- Windows Server 2012

- Windows Server 2008 R2

次に、組織内の Azure AD への Windows ドメイン参加デバイスの登録を有効にするために、お使いの環境で何を行う必要があるかについて説明します。次の内容を見ていきます。

1. 前提条件

2. デプロイメントと展開

3. トラブルシューティング

4. FAQ

 

## 前提条件 

Azure AD へのドメイン参加デバイスの自動登録を有効にする主な要件は、最新バージョンの Azure AD Connect があることです。

Azure AD Connect のデプロイ方法 (エクスプレスまたはカスタム インストール、あるいはインプレース アップグレード) によっては、次の前提条件が自動的に構成されている場合があります。

1. Azure AD へ登録するコンピューターによる、Azure AD のテナント情報検出用のオンプレミスの Active Directory のサービス接続ポイント (SCP)。

2. 登録時のコンピューター認証用の AD FS 発行変換規則 (フェデレーション構成に適用)。

組織内に Windows 10 以外のドメインに参加しているコンピューターがある場合には、次が必要です。

1. Azure AD でユーザーにデバイスの登録を許可するポリシーが有効になっていることを確認します。

2. 統合 Windows 認証 (WIA) が AD FS の多要素認証 (MFA) の有効な代替として設定されていることを確認します。

 

## Azure AD テナント検出用のサービス接続ポイント 

コンピューターを登録する Azure AD テナントを検出する情報が格納される SCP オブジェクトは、コンピューターが参加しているドメインのフォレストの configuration 名前付けコンテキスト パーティションに存在する必要があります。Active Directory のマルチ フォレスト構成では、コンピューターが参加しているすべてのフォレストに SCP が存在する必要があります。

SCP は、Active Directory で次の場所にある必要があります。

	CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Configuration Naming Context] 

> [AZURE.NOTE] example.com の Active Directory ドメイン名のフォレストでは、configuration 名前付けコンテキストは CN=Configuration,DC=example,DC=com になります

次の PowerShell スクリプトを使用して、オブジェクトが存在することと Azure AD テナントで検出する値を確認できます (例の configuration 名前付けコンテキストを独自の値に置き換えます)。

	$scp = New-Object System.DirectoryServices.DirectoryEntry; 

	$scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com"; 

	$scp.Keywords; 

$scp.Keywords の出力は次のような Azure AD テナント情報を示します。

	azureADName:microsoft.com 

	azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47 

SCP が存在しない場合は、Azure AD Connect サーバーで次の PowerShell スクリプトを実行して作成できます。

	Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1"; 

	$aadAdminCred = Get-Credential; 

	Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred; 


> [AZURE.NOTE] $aadAdminCred = Get-Credential を実行するときは、Get-Credential ポップアップが表示されたら user@example.com の形式で資格情報のユーザー名を入力します。コマンドレット Initialize-ADSyncDomainJoinedComputerSync を実行するときは、[connector account name] を、Active Directory コネクタ アカウントとして使用するドメイン アカウントに置き換えます。上記のコマンドレットでは、ドメイン コント ローラー (DC) で Active Directory Web サービス (ADWS) に依存している Active Directory PowerShell モジュールを使用します。ADWS は Windows Server 2008 R2 以上の DC でサポートされています。Windows Server 2008 またはそれ以下の DC のみがある場合は、PowerShell を介して System.DirectoryServices API を使用して SCP を作成し、適切な Keywords の値を割り当てます。

## インスタント コンピューター登録用 AD FS 規則(フェデレーション組織) 

Azure AD のフェデレーション構成では、コンピューターは AD FS (またはオンプレミスのフェデレーション サーバー) に依存し、Azure AD に対して Azure Device Registration Service (Azure DRS) への登録について認証を行います。

> [AZURE.NOTE] 非フェデレーション構成 (つまりユーザー パスワード ハッシュが Azure AD に同期されている) では、Windows 10 および Windows Server 2016 のドメインに参加しているコンピューターは、Azure AD Connect を介して Azure AD で取り上げられたオンプレミスのコンピューター アカウントに書き込まれた資格情報を使用して、Azure DRS に対して認証されます。非フェデレーション構成上の Windows 10/Windows Server 2016 以外のコンピューターの場合、Windows 10/Windows Server 2016 以外のドメインに参加しているコンピューターの登録については、デプロイメントの下のWindows Installer パッケージのセクションを参照してください。また、組織内でデバイス ベースの条件付きアクセスを有効にする必要があるオプションについては、このドキュメントの下を展開してください。

Windows 10 および Windows Server 2016 のコンピューターの場合、Azure AD Connect が Azure AD のデバイス オブジェクトをオンプレミスのコンピューター アカウント オブジェクトに関連付けます。これが機能するには、Azure DRS が登録を完了し最初にデバイス オブジェクトを作成するために、認証中に次の要求が存在する必要があります。

- `http://schemas.microsoft.com/ws/2012/01/accounttype` - ドメイン参加コンピューターとして認証するプリンシパルを特定する値 "DJ" が含まれます。
- `http://schemas.microsoft.com/identity/claims/onpremobjectguid` - オンプレミスのコンピューター アカウントの objectGUID 属性の値が含まれます。
- `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` - オンプレミスのコンピューター アカウントの objectSid 属性の値に対応する、コンピューターのプライマリ SID が含まれます。
- `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` - AD FS またはオンプレミスの STS から発行されたトークンを Azure AD が信頼することを許可する、適切な値が含まれます。これは、コンピューターが AD FS またはオンプレミス STS が存在する Azure AD に接続されているフォレストとは別のフォレストに参加している可能性がある、マルチフォレスト Active Directory 構成で重要です。AD FS のケースでは、値は `http://<domain-name>/adfs/services/trust/` である必要があります。ここで、"<domain-name>" は Azure AD で検証済みのドメイン名です。

これらの規則を手動で作成するには、AD FS でお使いのサーバーに接続されているセッションで次の PowerShell スクリプトを使用します。最初の行は、自身の組織の Azure AD で検証済みのドメイン名に置き換える必要があります。

> [AZURE.NOTE] オンプレミスのフェデレーション サーバーとして AD FS を使用していない場合は、ベンダーの指示に従ってこれらの要求を発行する適切な規則を作成します。

	$validatedDomain = "example.com"      # Replace example.com with your validated domain name in Azure AD 

	$rule1 = '@RuleName = "Issue object GUID" 

		c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

      	c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

      	=> issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

	$rule2 = '@RuleName = "Issue account type for domain joined computers" 

      c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

	$rule3 = '@RuleName = "Pass through primary SID" 

      c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

      c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

      => issue(claim = c2);' 

	$rule4 = '@RuleName = "Issue AccountType with the value User when it’s not a computer account" 

      NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]) 

      => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");' 

	$rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID" 

      c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] && 

      c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));' 

	$rule6 = '@RuleName = "Update issuer for DJ computer auth" 

      c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://$validatedDomain/adfs/services/trust/");' 

	$existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

	$updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5 + $rule6 

	$crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 
 
	Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

> [AZURE.NOTE] Windows 10 および Windows Server 2016 ドメインに参加しているコンピューターでは、Windows 統合認証を使用して、AD FS でホストされているアクティブな WS-Trust エンドポイントに対する認証を実行します。このエンドポイントが有効になっていることを確認します。Web 認証プロキシを使用している場合は、このエンドポイントがプロキシ経由で公開されていることも確認します。エンドポイントは、adfs/services/trust/13/windowstransport です。AD FS 管理コンソールの [サービス] の [エンドポイント] に有効と表示される必要があります。オンプレミスのフェデレーション サーバーとして AD FS を使用していない場合は、ベンダーの指示に従って対応するエンドポイントが有効になっていることを確認します。

 

## AD FS が登録するデバイスの認証をサポートするように設定されていることを確認する

デバイスの登録のために統合 Windows 認証 (WIA) が AD FS の多要素認証 (MFA) の有効な代替として設定されていることを確認する必要があります。

これには、認証メソッドを渡す発行変換規則が必要です。

1. AD FS 管理コンソールを開き、**[AD FS] > [信頼関係] > [証明書利用者信頼]** に移動します。

2. [Microsoft Office 365 ID プラットフォーム] 証明書利用者信頼オブジェクトを右クリックし、**[要求規則の編集]** を選択します。

2.	**[発行変換規則]** タブで、**[規則の追加]** を選択します。

3.	**[要求規則テンプレート]** の一覧から、**[カスタムの規則を使用して要求を送信する]** を選択します。

4.	**[次へ]** を選択します。

4.	**[要求規則名]** ボックスに「**Auth Method Claim Rule**」と入力します。

5.	**[要求規則]** ボックスに「`c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
=> issue(claim = c);`」と入力します。

6. お使いのフェデレーション サーバーで、Windows PowerShell を開きます。

7. 次のコマンドを入力します。

	`Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

**<RPObjectName>** は、Azure Active Directory 証明書利用者信頼オブジェクトの証明書利用者オブジェクト名です。通常、このオブジェクトは「Microsoft Office 365 ID プラットフォーム」という名前です。




 

## デプロイメントと展開 

前提条件が満たされたら、ドメイン参加コンピューターを Azure AD に登録できます。

Windows 10 Anniversary Update および Windows Server 2016 のドメイン参加コンピューターは、次回の再起動時または Windows へのユーザーのサインイン時に Azure AD に自動的に登録されます。ドメインに参加している新しいコンピューターは、ドメイン参加操作の後の再起動で Azure AD に登録されます。

> [AZURE.NOTE] Windows 10 November 2015 Update ドメイン参加コンピューターは、展開グループ ポリシー オブジェクトが設定されている場合にのみ Azure AD に自動的に登録されます。詳細については、次のセクションを参照してください。

Windows 10/Windows Server 2016 のドメイン参加コンピューターの自動登録の展開を制御するには、グループ ポリシー オブジェクトを使用できます。Windows 10 以外のドメイン参加コンピューターの自動登録の展開には、選択したコンピューターに Windows インストーラー パッケージをデプロイします。

> [AZURE.NOTE] また、展開を制御するグループ ポリシーは Windows 8.1 ドメイン参加コンピューターの登録もトリガーします。Windows 8.1 ドメイン参加コンピューターの登録用のポリシーを使用することも、または 7、8.0 など Windows や Windows Server のバージョンが混在している場合、Windows インストーラー パッケージを使用して Windows 10/Windows Server 2016 以外のすべてのコンピューターの登録を有効にすることもできます。

### 自動登録の展開を制御するグループ ポリシー オブジェクト 

Azure AD へのドメイン参加コンピューターの自動登録の展開を制御するには、グループ ポリシー登録ドメイン参加コンピューターをデバイスとしてコンピューターに登録できます。たとえば、セキュリティ グループに基づいてまたは組織単位 (OU) に対してポリシーをデプロイできます。

ポリシーを設定するには、次の手順を実行します。

1. サーバー マネージャーを開き、**[ツール]、[グループ ポリシーの管理]** に移動します。

2. [グループ ポリシーの管理] から、自動登録を有効にする Windows 10 または Windows Server 2016 のコンピューターのドメインに対応するドメイン ノードに移動します。

3. **[グループ ポリシー オブジェクト]** を右クリックし、**[新規]** を選択します。

4. グループ ポリシー オブジェクトに *Automatic Registration to Azure AD* などの名前を付けます。[OK] をクリックします。

4. 新しいグループ ポリシー オブジェクトを右クリックし、**[編集]** を選択します。

5. **[コンピューターの構成]、[ポリシー]、[管理用テンプレート]、[Windows コンポーネント]、[デバイスの登録]** の順に移動し、**[ドメインに参加しているコンピューターをデバイスとして登録する]** を右クリックして **[編集]** を選択します。

	> [AZURE.NOTE] このグループ ポリシー テンプレートは、以前のバージョンのグループ ポリシー管理コンソールから変更されています。以前のバージョンのコンソールを実行している場合、ポリシーは [コンピューターの構成]、[ポリシー]、[管理用テンプレート]、[Windows コンポーネント]、[社内参加] の下に Automatically workplace join client computers という名前であります。

6. **[有効]** を選択し、**[適用]** をクリックします。

7. **[OK]** をクリックします。

7. グループ ポリシー オブジェクトを選択した場所にリンクします。たとえば、コンピューターがある特定の組織単位 (OU) や、Azure AD に自動的に登録されるコンピューターが含まれる特定のセキュリティ グループなどです。組織内のすべての Windows 10 および Windows Server 2016 のドメイン参加コンピューターに対してこのポリシーを有効にするには、グループ ポリシー オブジェクトをドメインにリンクします。

## Windows 10 コンピューター以外の MSI パッケージ  

Windows 7、Windows 8.0、Windows 8.1、Windows Server 2008 R2、Windows Server 2012、または Windows Server 2012 R2 を実行しているドメイン参加コンピューターを登録するために、Windows インストーラー パッケージ (.msi) をダウンロードできます。

- [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
- [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

System Center Configuration Manager などのソフトウェア配布システムを使用して、このパッケージをデプロイしてください。パッケージは、/quiet パラメーターを使用する、標準のサイレント インストール オプションをサポートしています。System Center Configuration Manager 2016 を使用する場合は、完了した登録を追跡する機能などの追加の特典が用意されています。詳細については、[System Center 2016](https://www.microsoft.com/cloud-platform/system-center-2016) に関するページを参照してください。

インストーラーは、スケジュールされたタスクをシステムに作成します。このタスクは、ユーザーのコンテキストで実行され、Windows へのユーザーのサインインによってトリガーされます。このタスクにより、統合 Windows 認証 を使用して認証した後に、ユーザーの資格情報でデバイスが Azure AD にサイレントに登録されます。スケジュールされたタスクは、タスク スケジューラ ライブラリで、**[Microsoft]、[社内参加]** にあります。



## 関連トピック

- [Azure Active Directory の条件付きアクセス](active-directory-conditional-access.md)

<!---HONumber=AcomDC_0810_2016-->