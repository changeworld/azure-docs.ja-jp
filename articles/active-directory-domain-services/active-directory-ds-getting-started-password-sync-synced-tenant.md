<properties
	pageTitle="Azure AD ドメイン サービス: パスワード同期を有効にする | Microsoft Azure"
	description="Azure Active Directory ドメイン サービスの概要"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/20/2016"
	ms.author="maheshu"/>

# Azure AD Domain Services とのパスワード同期を有効にする
ここまでの作業では、自分の Azure AD テナントの Azure AD Domain Services を有効にしました。次に、Azure AD Domain Services とのパスワードの同期を有効にします。資格情報の同期が設定されると、ユーザーは企業の資格情報を使用して、管理対象ドメインにサインインできます。

実行する手順は、組織がクラウド専用 Azure AD テナントであるか、Azure AD Connect を使用してオンプレミスのディレクトリに同期するように設定されているかによって異なります。

<br>

> [AZURE.SELECTOR]
- [クラウド専用 Azure AD テナント](active-directory-ds-getting-started-password-sync.md)
- [同期された Azure AD テナント](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## タスク 5: AAD ドメイン サービスとのパスワード同期を有効にする (同期された Azure AD テナントの場合)
同期対象の Azure AD テナントは、Azure AD Connect を使用して組織のオンプレミス ディレクトリと同期するように設定されます。Azure AD Connect は、既定では NTLM および Kerberos 資格情報ハッシュを Azure AD と同期しません。Azure AD Domain Services を使用するには、NTLM および Kerberos 認証に必要な資格情報ハッシュを同期するように Azure AD Connect を構成する必要があります。以下の手順では、必要な資格情報ハッシュの Azure AD テナントとの同期を有効にします。


### Azure AD Connect のインストールまたは更新
Azure AD Connect の最新の推奨リリースをドメイン参加コンピューターにインストールします。Azure AD Connect の既存のインスタンスが設定されている場合は、Azure AD Connect の最新バージョンを使用するように更新する必要があります。既に修正されている既知の問題/バグを回避するために、常に最新バージョンの Azure AD Connect を使用してください。

**[Azure AD Connect のダウンロード](http://www.microsoft.com/download/details.aspx?id=47594)**

推奨バージョン: **1.1.281.0** - 2016 年 9 月 7 日公開

  > [AZURE.WARNING] 従来のパスワードの資格情報 (NTLM/Kerberos 認証で必要) で Azure AD テナントとの同期を有効にするには、Azure AD Connect の最新の推奨リリースをインストールする必要があります。この機能は、旧リリースの Azure AD Connect または従来の DirSync ツールでは使用できません。

Azure AD Connect のインストール手順については、次の記事を参照してください。 - [ の概要](../active-directory/active-directory-aadconnect.md)


### NTLM と Kerberos の資格情報ハッシュの Azure AD との同期を有効にする
各 AD フォレストで以下の PowerShell スクリプトを実行して、完全パスワード同期を強制的に適用し、すべてのオンプレミス ユーザーの資格情報ハッシュが Azure AD テナントと同期できるようにします。このスクリプトは、NTLM/Kerberos 認証に必要な資格情報ハッシュが Azure AD テナントと同期されるようにします。

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

ディレクトリのサイズ (ユーザーやグループの数など) によっては、資格情報ハッシュが Azure AD と同期されるまでに時間がかかります。資格情報ハッシュが Azure AD と同期されるとすぐに、Azure AD ドメイン サービスでパスワードを使用できるようになります。


<br>

## 関連コンテンツ

- [AAD ドメイン サービスとのパスワード同期を有効にする (クラウド専用 Azure AD ディレクトリの場合)](active-directory-ds-getting-started-password-sync.md)

- [Azure AD ドメイン サービスで管理されているドメインの管理](active-directory-ds-admin-guide-administer-domain.md)

- [Azure AD ドメイン サービスで管理されているドメインに Windows 仮想マシンを参加させる](active-directory-ds-admin-guide-join-windows-vm.md)

- [Azure AD ドメイン サービスで管理されているドメインに Red Hat Enterprise Linux 仮想マシンを参加させる](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

<!---HONumber=AcomDC_0921_2016-->