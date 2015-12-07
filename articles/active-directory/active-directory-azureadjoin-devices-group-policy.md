<properties 
	pageTitle="Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する | Microsoft Azure" 
	description="デバイスを企業ネットワークのドメインに参加させることができるように管理者がグループ ポリシーを構成する方法を説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article"

	ms.date="11/19/2015" 

	ms.author="femila"/>

# Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する

ドメイン参加は、過去 15 年以上にわたり、組織が仕事のためにデバイスを接続してきた従来の方法です。この方法により、ユーザーは AD の職場アカウントを使用して自分のデバイスにサインインすることができ、IT 部門はこれらのデバイスを全面的に管理できました。組織は、通常、イメージ作成方法を利用してユーザーにデバイスをプロビジョニングし、一般的に System Center Configuration Manager (SCCM) またはグループ ポリシーを使用してそのデバイスを管理します。

Windows 10 でドメインに参加した場合、Azure AD に接続すると、次のような利点があります。

- どこからでも Azure AD リソースに SSO (シングル サインオン) できます。
- 職場アカウントを使用して企業の Windows ストアにアクセスできます (Microsoft アカウントは不要)。
- 職場アカウントを使用して、企業に準拠した、デバイス間でのユーザー設定のローミングが可能です (Microsoft アカウントは不要)。
- Microsoft Passport と Windows Hello により、職場アカウントの認証が強化され、サインインが便利になります。
- 組織のデバイス ポリシーに準拠して、デバイスへのアクセスを制限できます。

##前提条件

ドメイン参加は、引き続き利用できますが、SSO、職場アカウントによる設定のローミング、職場アカウントを使用した Windows ストアへのアクセスに関する Azure AD の利点を得るには、次のものが必要です。

- Azure AD サブスクリプション。
- オンプレミス ディレクトリを Azure AD に拡張するための Azure AD Connect。
- ドメイン参加済みデバイスを Azure AD に接続するためのポリシー設定。
- デバイス用の Windows 10 ビルド (ビルド 10551 以降)。

Microsoft Passport for Work と Windows Hello を有効にするには、さらに次のものが必要です。

- ユーザー証明書を発行するための PKI インフラストラクチャ。
- System Center Configuration Manager Version 1509 for Technical Preview。詳細については、[Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) に関するページと [System Center Configuration Manager チームのブログ](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)を参照してください。これは、Microsoft Passport のキーに基づいてユーザー証明書をデプロイするために必要です。

PKI のデプロイ要件の代替策として、次の操作を実行できます。

- Windows Server 2016 Active Directory ドメイン サービスを使用していくつかのドメイン コントローラーを用意します。

条件付きアクセスを有効にするために、追加でデプロイすることなく "ドメイン参加済み" デバイスへのアクセスを許可するポリシーを作成できます。デバイスのコンプライアンスに基づいてアクセス制御を管理するには、次のものが必要です。

- System Center Configuration Manager Version 1509 for Technical Preview (Passport のシナリオ用)。

## デプロイの手順


## 手順 1: Azure Active Directory Connect をデプロイする

Azure AD Connect により、オンプレミスのコンピューターをクラウド内のデバイス オブジェクトとしてプロビジョニングできます。Azure AD Connect をデプロイするには、Azure AD Connect によるディレクトリのハイブリッド管理の有効化に関するページを参照してください。

 - [Azure AD Connect のカスタム インストール](https://azure.microsoft.com/ja-JP/documentation/articles/active-directory-aadconnect-get-started-custom/) (高速インストールではありません) を実行した場合は、次の「**オンプレミスの Active Directory でサービス接続ポイント (SCP) を作成する**」の手順に従う必要があります。
 - Azure AD Connect をインストールする前に Azure AD で構成をフェデレーションした場合 (たとえば、以前に Active Directory フェデレーション サービス (AD FS) をデプロイした場合)、以下の「**AD FS の要求規則を構成する**」の手順に従う必要があります。

### オンプレミスの Active Directory でサービス接続ポイント (SCP) を作成する

ドメイン参加済みデバイスでは、Azure デバイス登録サービスへの自動登録の時点で、このオブジェクトを使用して Azure AD のテナント情報を探します。Azure AD Connect サーバーで、次の PowerShell コマンドを実行します。

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

>[AZURE.NOTE] [*connector account name*] を、AD Connector アカウントとして使用したドメイン アカウントに置き換えます。

>[AZURE.NOTE]Get-Credential というポップアップが表示されたときに入力する資格情報のユーザー名は、**user@example.com* 形式にする必要があります。

### AD FS の要求規則を構成する
これにより、AD FS 経由での Kerberos/NTLM を使用した認証がコンピューターに許可されるため、Azure DRS には即座にコンピューターを登録できます。この手順を実行しないと、コンピューターは Azure AD への到達が遅くなります (Azure AD Connect の同期時間の影響を受けます)。

>[AZURE.NOTE]オンプレミスのフェデレーション サーバーとして AD FS を使用していない場合は、ベンダーの指示に従って要求規則を作成してください。

AD FS サーバー (または AD FS サーバーに接続されているセッション) で、次の PowerShell コマンドを実行します。

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed 
     |   for DomainJoin++.  The rules include:
     |   -ObjectGuid
     |   -AccountType
     |   -ObjectSid
     +---------------------------------------------------------------------#>
 
    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules
 
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
 
    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3
 
    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules
 
    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

>[AZURE.NOTE]Windows 10 コンピューターでは、Windows 統合認証を使用して、AD FS でホストされているアクティブな WS-Trust エンドポイントに対する認証を実行します。このエンドポイントが有効になっていることを確認する必要があります。Web 認証プロキシを使用している場合は、このエンドポイントがプロキシ経由で公開されていることも確認する必要があります。これを行うには、AD FS 管理コンソールの [サービス] の [エンドポイント] で、adfs/services/trust/13/windowstransport が有効と表示されていることを確認します。


## 手順 2: Active Directory のグループ ポリシーを使用してデバイスの自動登録を構成する

Active Directory グループ ポリシーを使用すると、自動的に Azure AD に登録するよう Windows 10 ドメイン参加済みデバイスを構成できます。その方法については、次の詳細な手順を参照してください。

1. 	サーバー マネージャーを開き、**[ツール]** > **[グループ ポリシーの管理]** に移動します。
2.	[グループ ポリシーの管理] から、Azure AD 参加を有効にするドメインに対応するドメイン ノードに移動します。
3.	**[グループ ポリシー オブジェクト]** を右クリックし、**[新規]** を選択します。グループ ポリシー オブジェクトに Automatic Azure AD Join などの名前を付けます。**[OK]** をクリックします。
4.	新しいグループ ポリシー オブジェクトを右クリックし、**[編集]** を選択します。
5.	**[コンピューターの構成]** > **[ポリシー]** > **[管理用テンプレート]** > **[Windows コンポーネント]** > **[社内参加]** の順に移動します。
6.	**[クライアント コンピューターを自動的に社内参加させる]** を右クリックして **[編集]** を選択します。
7.	**[有効]** を選択し、**[適用]** をクリックします。**[OK]** をクリックします。
8.	これで、グループ ポリシー オブジェクトを選択した場所にリンクできるようになりました。組織内のすべての Windows 10 ドメイン参加済みデバイスに対してこのポリシーを有効にするには、グループ ポリシーをドメインにリンクします。次に例を示します。
 - Windows 10 ドメイン参加済みコンピューターを配置する AD の 特定の組織単位 (OU)。
 - Azure AD に自動登録される Windows 10 ドメイン参加済みコンピューターを含む特定のセキュリティ グループ。
 
>[AZURE.NOTE]このグループ ポリシー テンプレートの名前は、Windows 10 で変更されました。Windows 10 コンピューターからグループ ポリシー ツールを実行している場合、ポリシーは "**ドメインに参加しているコンピューターをデバイスとして登録する**" と表示されます。<br>また、このポリシーは次の場所にあります。<br>***コンピューターの構成/ポリシー/管理用テンプレート/Windows コンポーネント/デバイスの登録***

 
## 追加情報
* [エンタープライズ向け Windows 10: デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-user-upgrade.md)
* [Azure AD 参加の使用シナリオについて](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 エクスペリエンスのためのドメイン参加済みデバイスの Azure AD への接続](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD 参加の設定](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->