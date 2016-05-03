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
	ms.date="04/25/2016"
	ms.author="maheshu"/>

# Azure AD ドメイン サービス *(プレビュー)* - Azure AD ドメイン サービスとのパスワード同期を有効にする

## タスク 5: AAD ドメイン サービスとのパスワード同期を有効にする (同期された Azure AD テナントの場合)
Azure AD ディレクトリに対する Azure AD ドメイン サービスを有効にしたら、Azure AD ドメイン サービスとの間でパスワードの同期を有効にします。これにより、ユーザーは、会社の資格情報を使用してドメインにサインインできます。

実行する手順は、組織の Azure AD ディレクトリがクラウド専用であるか、Azure AD Connect を使用してオンプレミスのディレクトリと同期するように設定されているかによって異なります。

<br>

> [AZURE.SELECTOR]
- [クラウド専用 Azure AD ディレクトリ](active-directory-ds-getting-started-password-sync.md)
- [同期された Azure AD ディレクトリ](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### 同期テナント - NTLM と Kerberos の資格情報ハッシュの Azure AD との同期を有効にする
組織の Azure AD テナントが Azure AD Connect を使用してオンプレミスのディレクトリと同期するように設定されている場合は、NTLM/Kerberos 認証で必要な資格情報のハッシュを同期するように Azure AD Connect を構成する必要があります。これらのハッシュは、既定では Azure AD と同期しません。次の手順に従って、Azure AD テナントとのハッシュの同期を有効にできます。

#### Azure AD Connect のインストールまたは更新

Azure AD Connect の最新の推奨リリースを、ドメイン参加コンピューターにインストールする必要があります。Azure AD Connect の既存のインスタンスが設定されている場合は、Azure AD Connect の GA ビルドを使用するように更新する必要があります。既に修正されている既知の問題やバグを避けるために、必ず最新バージョンの Azure AD Connect を使用してください。

**[Azure AD Connect のダウンロード](http://www.microsoft.com/download/details.aspx?id=47594)**

推奨バージョン: **1.1.130.0** (2016 年 4 月 12 日公開)

  > [AZURE.WARNING] 従来のパスワードの資格情報 (NTLM/Kerberos 認証で必要) で Azure AD テナントとの同期を有効にするには、Azure AD Connect の最新の推奨リリースをインストールする必要があります。この機能は、旧リリースの Azure AD Connect または従来の DirSync ツールでは使用できません。

Azure AD Connect のインストール手順については、次の記事を参照してください。 - [ の概要](../active-directory/active-directory-aadconnect.md)


#### Azure AD へのパスワードの完全同期を強制する

パスワードの完全同期を強制し、オンプレミスの全ユーザーの (NTLM/Kerberos 認証で必要な資格情報のハッシュを含む) パスワード ハッシュが Azure AD テナントと同期できるようにするには、各 AD フォレストで、次の PowerShell スクリプトを実行します。

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

ディレクトリのサイズ (ユーザーやグループの数など) によっては、資格情報が Azure AD と同期されるまでに時間がかかります。資格情報ハッシュが Azure AD と同期されるとすぐに、Azure AD ドメイン サービスでパスワードを使用できるようになります。


<br>

## 関連コンテンツ

- [AAD ドメイン サービスとのパスワード同期を有効にする (クラウド専用 Azure AD ディレクトリの場合)](active-directory-ds-getting-started-password-sync.md)

- [Azure AD ドメイン サービスで管理されているドメインの管理](active-directory-ds-admin-guide-administer-domain.md)

- [Azure AD ドメイン サービスで管理されているドメインに Windows 仮想マシンを参加させる](active-directory-ds-admin-guide-join-windows-vm.md)

- [Azure AD ドメイン サービスで管理されているドメインに Red Hat Enterprise Linux 仮想マシンを参加させる](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

<!---HONumber=AcomDC_0427_2016-->