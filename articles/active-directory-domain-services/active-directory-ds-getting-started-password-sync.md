<properties
	pageTitle="Azure Active Directory ドメイン サービス プレビュー - 作業の開始 | Microsoft Azure"
	description="Azure Active Directory ドメイン サービスの概要"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="maheshu"/>

# Azure AD Domain Services *(プレビュー)* - 作業の開始

## 手順 5: パスワードの同期を有効にする
Azure AD テナントに対して Azure AD Domain Services を有効にしたら、次の手順は、パスワードの同時を有効にすることです。これにより、ユーザーは、会社の資格情報を使用してドメインにサインインできます。

実行する手順は、組織がクラウド専用 Azure AD テナントであるか、Azure AD Connect を使用してオンプレミスのディレクトリに同期するように設定されているかによって異なります。

### クラウド専用テナントでパスワード同期を有効にする
組織がクラウド専用 Azure AD テナントである場合、Azure AD Domain Services を使用する必要があるユーザーは自分のパスワードを変更する必要があります。この手順により、Azure AD Domain Services が Kerberos/NTLM 認証で必要な資格情報のハッシュが Azure AD 内に生成されます。Azure AD Domain Services を使用する必要があるテナントの全ユーザーのパスワードの有効期限を無効にするか、これらのエンド ユーザーにパスワードを変更するように指示できます。

パスワードを変更するためにエンド ユーザーに提供する必要がある手順を次に示します。

1. 組織の Azure AD アクセス パネル ページに移動します。これは、通常は [http://myapps.microsoft.com](http://myapps.microsoft.com)です。
2. そのページで **[プロファイル]** タブを選択します。
3. そのページで **[パスワードの変更]** タイルをクリックして、パスワードの変更を開始します。

    ![Azure AD Domain Services 用の仮想ネットワークを作成します。](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. **[パスワードの変更]** ページが表示されます。ユーザーは、既存の (古い) パスワードを入力し、操作を進めてパスワードを変更します。

    ![Azure AD Domain Services 用の仮想ネットワークを作成します。](./media/active-directory-domain-services-getting-started/user-change-password2.png)

ユーザーが自分のパスワードを変更すると、新しいパスワードは Azure AD Domain Services にすぐに同期されます。パスワードの同期が完了した後、ユーザーは、新しく変更したパスワードを使用してドメインにログインできます。


### 同期テナントでパスワード同期を有効にする
組織の Azure AD テナントが Azure AD Connect を使用してオンプレミスのディレクトリと同期するように設定されている場合は、NTLM/Kerberos 認証で必要な資格情報のハッシュを同期するように Azure AD Connect を構成する必要があります。これらのハッシュは、既定では Azure AD と同期しません。次の手順に従って、Azure AD テナントとのハッシュの同期を有効にできます。

#### Azure AD Connect をインストールする

Azure AD Connect の GA リリースを、ドメイン参加コンピューターにインストールする必要があります。Azure AD Connect の既存のインスタンスが設定されている場合は、Azure AD Connect の GA ビルドを使用するように更新する必要があります。

  [Azure AD Connect – GA リリースをダウンロードする](http://download.microsoft.com/download/B/0/0/B00291D0-5A83-4DE7-86F5-980BC00DE05A/AzureADConnect.msi)

> [AZURE.WARNING]従来のパスワードの資格情報 (NTLM/Kerberos 認証で必要) で Azure AD テナントとの同期を有効にするには、Azure AD Connect の GA リリースをインストールする必要があります。この機能は、Azure AD Connect の以前のリリースでは利用できません。

Azure AD Connect のインストール手順については、次の記事を参照してください。 - [ の概要](../active-directory/active-directory-aadconnect.md)


#### Azure AD への従来の資格情報の同期を有効にする

Azure AD Domain Services での NTLM 認証に必要な従来の資格情報の同期を有効にします。そのためには、Azure AD Connect がインストールされているコンピューターで次のレジストリ キーを作成します。

次の DWORD レジストリ キーを作成し、1 に設定します。

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\MSOLCoExistence\PasswordSync\EnableWindowsLegacyCredentialsSync

Set its value to 1.
```

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

ディレクトリのサイズ (ユーザーやグループの数など) によっては、Azure AD と Azure AD Domain Services で資格情報が同期されるまで時間がかかります。

<!---HONumber=Oct15_HO4-->