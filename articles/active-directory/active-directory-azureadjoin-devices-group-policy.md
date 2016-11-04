---
title: Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する | Microsoft Docs
description: デバイスを企業ネットワークのドメインに参加させることができるように管理者がグループ ポリシーを構成する方法を説明します。
services: active-directory
documentationcenter: ''
author: femila
manager: swadhwa
editor: ''
tags: azure-classic-portal

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila

---
# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する
Domain Join は、過去 15 年以上にわたり、組織がデバイスを接続して作業を行ってきた従来の方法です。 ユーザーは Windows Server Active Directory (Active Directory) の職場または学校アカウントを使用して自分のデバイスにサインインでき、IT 部門はそれらのデバイスを完全に管理できます。 組織は、通常、イメージ作成方法を利用してユーザーにデバイスをプロビジョニングし、一般的に System Center Configuration Manager (SCCM) またはグループ ポリシーを使用してそのデバイスを管理します。

Windows 10 の Domain Join では、 Azure Active Directory (Azure AD) にデバイスを接続すると、次のような利点があります。

* どこからでも Azure AD リソースにシングル サインオン (SSO) できます
* 職場または学校アカウントを使用して、企業の Windows ストアにアクセスできます (Microsoft アカウントは不要)
* 職場または学校アカウントを使用して、企業に準拠した、デバイス間でのユーザー設定のローミングが可能です (Microsoft アカウントは不要)
* Microsoft Passport と Windows Hello により、職場または学校アカウントの認証が強化され、サインインが便利になります
* 組織でのデバイスのグループ ポリシー設定に準拠しているデバイスのみにアクセスを制限できます

## <a name="prerequisites"></a>前提条件
Domain Join を引き続き利用できます。 ただし、SSO、職場または学校アカウントによる設定のローミング、職場または学校アカウントを使用した Windows ストアへのアクセスに関する Azure AD を利用するには、次のものが必要です。

* Azure AD サブスクリプション
* オンプレミス ディレクトリを Azure AD に拡張するための Azure AD Connect
* ドメイン参加済みデバイスを Azure AD に接続するように設定されたポリシー
* デバイス用の Windows 10 ビルド (ビルド 10551 以降)

Microsoft Passport for Work と Windows Hello を有効にするには、次のものも必要です。

* ユーザー証明書を発行するための公開キー基盤 (PKI)。
* System Center Configuration Manager Version 1509 for Technical Preview。 詳細については、「[Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update)」および [System Center Configuration Manager チームのブログ](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)を参照してください。 これは、Microsoft Passport のキーに基づいてユーザー証明書をデプロイするために必要です。

PKI のデプロイ要件の代替策として、次の操作を実行できます。

* Windows Server 2016 Active Directory ドメイン サービスを使用していくつかのドメイン コントローラーを用意します。

条件付きアクセスを有効にするために、追加でデプロイすることなく "ドメイン参加済み" デバイスへのアクセスを許可するグループ ポリシー設定を作成できます。 デバイスのコンプライアンスに基づいてアクセス制御を管理するには、次のものが必要です。

* System Center Configuration Manager Version 1509 for Technical Preview (Passport のシナリオ用)

## <a name="deployment-instructions"></a>デプロイの手順
### <a name="step-1:-deploy-azure-active-directory-connect"></a>手順 1: Azure Active Directory Connect をデプロイする
Azure AD Connect により、オンプレミスのコンピューターをクラウド内のデバイス オブジェクトとしてプロビジョニングできます。 Azure AD Connect のデプロイについては、「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md#install-azure-ad-connect)」の「Azure AD Connect をインストールする」を参照してください。

* [Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md) (高速インストールではありません) を実行した場合は、次にこの後の「**オンプレミスの Active Directory でサービス接続ポイントを作成する**」の手順に従います。
* Azure AD Connect をインストールする前に Azure AD で構成をフェデレーションした場合は (たとえば、以前に Active Directory フェデレーション サービス (AD FS) をデプロイした場合)、後の「 **AD FS の要求規則を構成する** 」の手順に従います。

#### <a name="create-a-service-connection-point-in-on-premises-active-directory"></a>オンプレミスの Active Directory でサービス接続ポイントを作成する
ドメイン参加済みデバイスでは、Azure デバイス登録サービスへの自動登録の時点で、サービス接続ポイントを使用して Azure AD のテナント情報を探します。

Azure AD Connect サーバーで、次の PowerShell コマンドを実行します。

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


コマンドレット $aadAdminCred = Get-Credential を実行するときは、Get-Credential ポップアップが表示されたら *user@example.com* の形式で資格情報のユーザー名を入力します。

コマンドレット Initialize-ADSyncDomainJoinedComputerSync... を実行するときは、[*connector account name*] を、Active Directory コネクタ アカウントとして使用するドメイン アカウントに置き換えます。

#### <a name="configure-ad-fs-claim-rules"></a>AD FS の要求規則を構成する
AD FS 要求規則を構成すると、AD FS 経由での Kerberos/NTLM を使用した認証がコンピューターに許可されるため、Azure デバイス登録サービスにすぐにコンピューターを登録できます。 この手順を実行しないと、コンピューターは Azure AD への到達が遅くなります (Azure AD Connect の同期時間の影響を受けます)。

> [!NOTE]
> オンプレミスのフェデレーション サーバーとして AD FS を使用していない場合は、ベンダーの指示に従って要求規則を作成します。
> 
> 

AD FS サーバー (または AD FS サーバーに接続されているセッション) で、次の PowerShell コマンドを実行します。

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed
     |   for DomainJoin++. The rules include:
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

> [!NOTE]
> Windows 10 コンピューターでは、Windows 統合認証を使用して、AD FS でホストされているアクティブな WS-Trust エンドポイントに対する認証を実行します。 このエンドポイントが有効になっていることを確認します。 Web 認証プロキシを使用している場合は、このエンドポイントがプロキシ経由で公開されていることも確認します。 これは、adfs/services/trust/13/windowstransport を確認することで行うことができます。 AD FS 管理コンソールの **[サービス]**  >  **[エンドポイント]** に有効と表示される必要があります。
> 
> 

### <a name="step-2:-configure-automatic-device-registration-via-group-policy-in-active-directory"></a>手順 2: Active Directory のグループ ポリシーを使用してデバイスの自動登録を構成する
Active Directory のグループ ポリシーを使用すると、自動的に Azure AD に登録するよう Windows 10 ドメイン参加済みデバイスを構成できます。

> [!NOTE]
> デバイスの自動登録をセットアップする方法の最新手順については、「 [Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の設定方法](active-directory-conditional-access-automatic-device-registration-setup.md)」を参照してください。
> 
> このグループ ポリシー テンプレートの名前は、Windows 10 で変更されました。 Windows 10 コンピューターからグループ ポリシー ツールを実行している場合、ポリシーは次のように表示されます。 <br>
> **ドメインに参加しているコンピューターをデバイスとして登録する**<br>
> ポリシーは次の場所にあります。<br>
> ***コンピューターの構成/ポリシー/管理用テンプレート/Windows コンポーネント/デバイスの登録***
> 
> 

## <a name="additional-information"></a>追加情報
* [エンタープライズ向け Windows 10: デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-user-upgrade.md)
* [Azure AD 参加の使用シナリオについて](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 エクスペリエンスのためのドメイン参加済みデバイスの Azure AD への接続](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD Join の設定](active-directory-azureadjoin-setup.md)

<!--HONumber=Oct16_HO2-->


