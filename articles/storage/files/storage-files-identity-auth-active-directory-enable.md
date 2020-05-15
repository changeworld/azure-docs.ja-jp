---
title: Azure Files に対する SMB 経由の Active Directory 認証を有効にする
description: Active Directory を介した Azure ファイル共有に対する SMB 経由の ID ベース認証を有効にする方法を学習します。 ドメインに参加している Windows 仮想マシン (VM) では、AD 資格情報を使用して Azure ファイル共有にアクセスできます。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: rogarana
ms.openlocfilehash: febb796a47b9f5e78906d513c115b62b35c7c7d5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196511"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>SMB を使用して Azure ファイル共有へのオンプレミスの Active Directory Domain Services 認証を有効にする

[Azure Files](storage-files-introduction.md)  では、次の 2 種類の Domain Services を介したサーバー メッセージ ブロック (SMB) 経由の ID ベース認証がサポートされています: Azure Active Directory Domain Services (Azure AD DS) およびオンプレミスの Active Directory Domain Services (AD DS) (プレビュー)。 この記事では、Azure ファイル共有への認証に Active Directory Domain Services を利用するための、新しく導入された (プレビュー) サポートに焦点を当てています。 Azure ファイル共有に対して Azure AD DS (GA) 認証を有効にすることに関心をお持ちの場合は、[それに関する記事](storage-files-identity-auth-active-directory-domain-service-enable.md)を参照してください。

> [!NOTE]
> Azure ファイル共有では、Azure Active Directory Domain Services (Azure AD DS) またはオンプレミスの Active Directory Domain Services (AD DS) のいずれか 1 つのドメイン サービスに対する認証のみがサポートされます。 
>
> Azure ファイル共有の認証に使用される AD DS ID は、Azure AD と同期されている必要があります。 パスワード ハッシュの同期はオプションです。 
> 
> オンプレミスの AD DS 認証では、AD DS で作成されたコンピューター アカウントに対する認証はサポートされていません。 
> 
> オンプレミスの AD DS 認証は、ストレージ アカウントが登録されている 1 つの AD フォレストに対してのみサポートされます。 既定では、単一のフォレストからのみ、AD DS 資格情報を使用して Azure ファイル共有にアクセスできます。 別のフォレストから Azure ファイル共有にアクセスする必要がある場合は、適切なフォレストの信頼が構成されていることを確認してください。詳細については、[FAQ](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) に関する記述を参照してください。  
> 
> SMB アクセスと ACL 永続化のための AD DS 認証は、Azure File Sync によって管理される Azure ファイル共有でサポートされます。
>
> Azure Files では、AD と RC4-HMAC 暗号化を使用した Kerberos 認証がサポートされています。 AES Kerberos 暗号化はまだサポートされていません。

SMB 経由の Azure ファイル共有に対して AD DS を有効にすると、AD DS に参加しているマシンで、既存の AD 資格情報を使用して Azure ファイル共有をマウントできます。 この機能は、オンプレミス マシンでホストされているか、Azure でホストされている AD DS 環境で有効にすることができます。

Azure ファイル共有へのアクセスに使用される ID は、[ロールベースのアクセス制御 (RBAC)](../../role-based-access-control/overview.md) モデルを使用して共有レベルのファイル アクセス許可を適用するために、Azure AD に同期する必要があります。 既存のファイル サーバーから引き継がれたファイルまたはディレクトリの [Windows スタイルの DACL](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) は保持され、適用されます。 この機能では、エンタープライズ AD DS 環境とのシームレスな統合が提供されます。 オンプレミス ファイル サーバーを Azure ファイル共有に置き換えると、既存のユーザーは、使用されている資格情報を変更することなく、シングル サインオン エクスペリエンスで現在のクライアントから Azure ファイル共有にアクセスできるようになります。  

> [!NOTE]
> 一部の一般的なユース ケースでの Azure Files AD 認証の設定に役立つように、ステップ バイ ステップ ガイダンスを含む [2 本の動画](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos)を公開しました。
> - オンプレミスのファイル サーバーの Azure Files への置き換え (ファイルと AD 認証のためのプライベート リンクに関する設定を含む)
> - Windows Virtual Desktop のプロファイル コンテナーとしての Azure Files の使用 (AD 認証と FsLogix 構成に関する設定を含む)

## <a name="prerequisites"></a>前提条件 

Azure ファイル共有に対する AD DS 認証を有効にする前に、次の前提条件を完了していることを確認します。 

- AD DS 環境を選択または作成し、[Azure AD に同期します](../../active-directory/hybrid/how-to-connect-install-roadmap.md)。 

    新規または既存のオンプレミスの AD DS 環境で機能を有効にすることができます。 アクセスに使用される ID は、Azure AD と同期されている必要があります。 アクセスする Azure AD テナントとファイル共有は、同じサブスクリプションに関連付けられている必要があります。 

    AD ドメイン環境をセットアップする場合は、「[Active Directory Domain Services の概要](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)」を参照してください。 Azure AD に AD を同期していない場合は、「[Azure AD Connect と Azure AD Connect Health のインストール ロードマップ](../../active-directory/hybrid/how-to-connect-install-roadmap.md)」のガイダンスに従って Azure AD Connect を構成および設定します。 

- オンプレミス マシンまたは Azure VM をオンプレミスの AD DS にドメイン参加させます。 

    マシンまたは VM から AD DS 資格情報を使用してファイル共有にアクセスするには、ご利用のデバイスが AD ドメインに参加している必要があります。 ドメインに参加させる方法については、「[コンピューターをドメインに参加させる](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)」を参照してください。 

- Azure ストレージ アカウントを選択または作成します。  最適なパフォーマンスが得られるように、共有にアクセスする予定の VM と同じリージョンにストレージ アカウントをデプロイすることをお勧めします。

    ファイル共有を含むストレージ アカウントが、Azure AD DS 認証用にまだ構成されていないことを確認します。 ストレージ アカウントで Azure Files Azure AD DS 認証が有効になっている場合は、オンプレミスの AD DS を使用するように変更する前に無効にする必要があります。 これは、Azure AD DS 環境で構成された既存の ACL を、適切なアクセス許可を適用するために再構成する必要があることを意味します。
    
    新しいファイル共有の作成については、 [Azure Files でのファイル共有の作成](storage-how-to-create-file-share.md)に関するページを参照してください。

- ストレージ アカウント キーを使用して Azure ファイル共有をマウントすることで、接続を確認する。 

    ご利用のデバイスとファイル共有が正しく構成されていることを確認するには、ストレージ アカウント キーを使用して[ファイル共有のマウント](storage-how-to-use-files-windows.md)を試します。 Azure Files への接続で問題が発生した場合は、[Windows での Azure Files マウント エラーに対して発行したトラブルシューティング ツール](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)を参照してください。 また、ポート 445 がブロックされている場合のシナリオを回避するための[ガイダンス](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access)も提供されています。 

## <a name="regional-availability"></a>リージョン別の提供状況

AD DS (プレビュー) を使用した Azure Files 認証は、[すべてのパブリック リージョンと Azure Gov リージョン](https://azure.microsoft.com/global-infrastructure/locations/)で利用できます。

## <a name="overview"></a>概要

ファイル共有でネットワーク構成を有効にする予定がある場合は、AD DS 認証を有効にする前に、まず[ネットワークに関する検討事項](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview)を評価し、関連する構成を完了することをお勧めします。

Azure ファイル共有の AD DS 認証を有効にすると、オンプレミスの AD DS の資格情報を使用して Azure ファイル共有に対する認証を行うことができます。 さらに、アクセス許可より適切に管理して、きめ細かいアクセス制御を行うことができます。 これを行うには、AD Connect を使用して、オンプレミスの AD DS から Azure AD に ID を同期する必要があります。 オンプレミスの AD DS の資格情報でファイル/共有レベルのアクセスを管理しながら、Azure AD に同期される ID を使用して共有レベルのアクセスを制御します。

次に、以下の手順に従って、AD DS 認証用に Azure Files を設定します。 

1. [ストレージ アカウントでの Azure Files AD DS 認証を有効にします](#1-enable-ad-ds-authentication-for-your-account)

1. [ターゲットの AD ID と同期している、Azure AD ID (ユーザー、グループ、またはサービス プリンシパル) に、共有のアクセス許可を割り当てます](#2-assign-access-permissions-to-an-identity)

1. [SMB を使用して、ディレクトリとファイルの ACL を構成します](#3-configure-ntfs-permissions-over-smb)
 
1. [AD DS に参加している VM に Azure ファイル共有をマウントします](#4-mount-a-file-share-from-a-domain-joined-vm)

1. [AD DS のストレージ アカウント ID のパスワードを更新します](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)

次の図は、Azure ファイル共有に対する SMB 経由の Azure AD 認証を有効にするためのエンドツーエンド ワークフローを示しています。 

![Files AD ワークフロー図](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Azure ファイル共有に対する SMB 経由の AD DS 認証は、Windows 7 または Windows Server 2008 R2 より新しい OS バージョンで実行されているマシンまたは VM でのみサポートされます。 

## <a name="1-enable-ad-ds-authentication-for-your-account"></a>1 アカウントへの AD DS 認証を有効にする 

Azure ファイル共有に対する SMB 経由の AD DS 認証を有効にするには、最初にストレージ アカウントを AD DS に登録してから、ストレージ アカウントに必要なドメイン プロパティを設定する必要があります。 機能は、ストレージ アカウントで有効になると、アカウント内のすべての新規および既存のファイル共有に適用されます。 AzFilesHybrid Powershell モジュールをダウンロードし、`join-AzStorageAccountForAuth` を使用して機能を有効にします。 エンドツーエンド ワークフローの詳細については、このセクションにあるスクリプトを参照してください。 

> [!IMPORTANT]
> `Join-AzStorageAccountForAuth` コマンドレットでは、AD 環境に変更を加えます。 以下の説明をお読みになり、コマンドを実行するための適切なアクセス許可があることと、適用されている変更がコンプライアンスおよびセキュリティ ポリシーに沿っていることを確認するために行われる内容についてよりよく理解してください。 

`Join-AzStorageAccountForAuth` コマンドレットでは、指定されたストレージ アカウントに代わってオフライン ドメイン参加に相当することを行います。 このスクリプトではコマンドレットを使用して、AD ドメインに [コンピューター アカウント](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (既定) または[サービス ログオン アカウント](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)のいずれかのアカウントが作成されます。 この操作を手動で行う場合は、環境に最も適したアカウントを選択する必要があります。

コマンドレットによって作成された AD DS アカウントは、AD ドメイン内のストレージ アカウントを表します。 AD DSアカウントが、パスワードの有効期限を適用する組織単位 (OU) の下で作成されている場合は、パスワードの有効期間の前にパスワードを更新する必要があります。 アカウント パスワードの更新に失敗すると、Azure ファイル共有にアクセスするときに認証エラーが発生します。 パスワードを更新する方法については、[AD DS アカウント パスワードを更新する](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)方法に関する記事を参照してください。

次のスクリプトを使用すると、登録を行い、機能を有効にすることができます。または、スクリプトで行われる操作を手動で行うことができます。 これらの操作については、スクリプトの後のセクションで説明します。 両方を行う必要はありません。

### <a name="11-script-prerequisites"></a>1.1 スクリプトの前提条件
- [AzFilesHybrid モジュールをダウンロードして解凍する](https://github.com/Azure-Samples/azure-files-samples/releases)
- ターゲット AD でサービス ログオン アカウントまたはコンピューター アカウントを作成する権限がある AD DS 資格情報を使用して、オンプレミスの AD DS に参加しているデバイスにモジュールをインストールして実行します。
-  Azure AD に同期されているオンプレミスの AD DS 資格情報を使用して、スクリプトを実行します。 オンプレミスの AD DS 資格情報には、ストレージ アカウント所有者または共同作成者の RBAC ロールのアクセス許可が必要です。
- ストレージ アカウントが[サポートされているリージョン](#regional-availability)にあることを確認します。

### <a name="12-domain-join-your-storage-account"></a>1.2 ストレージ アカウントのドメイン参加
PowerShell で実行する前に、必ず、以下のパラメーターのプレースホルダーの値を実際の値に置き換えてください。
> [!IMPORTANT]
> ドメイン参加コマンドレットは、AD のストレージ アカウント (ファイル共有) を表す AD アカウントを作成します。 コンピューター アカウントまたはサービス ログオン アカウントのどちらとして登録するかを選択できます。詳細については、[FAQ](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) を参照してください。 コンピューター アカウントの場合は、AD で 30 日に設定された既定のパスワードの有効期限が存在します。 同様に、サービス ログオン アカウントでも、既定のパスワードの有効期限が AD ドメインまたは組織単位 (OU) で設定されている可能性があります。
> どちらのアカウントの種類でも、AD 環境でどのようなパスワードの有効期限が構成されているかを確認し、パスワードの有効期間が終了する前に、下の AD アカウントの [AD のストレージ アカウント ID のパスワードを更新する](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)よう計画することを強くお勧めします。 [AD で新しい AD 組織単位 (OU) を作成](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps)し、それぞれ[コンピューター アカウント](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN)またはサービス ログオン アカウントのパスワードの有効期限のポリシーを無効にすることを検討できます。 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" `
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

次の説明は、`Join-AzStorageAccountForAuth` コマンドレットを実行したときに行われるすべてのアクションをまとめたものです。 コマンドを使用しない場合は、これらの手順を手動で行うことができます。

> [!NOTE]
> 前述の `Join-AzStorageAccountForAuth` スクリプトを既に正常に実行している場合は、次のセクション「1.3 機能が有効になっていることを確認する」に進んでください。 以下の操作をもう一度実行する必要はありません。

#### <a name="a-checking-environment"></a>a. 環境の確認

まず、スクリプトによって環境がチェックされます。 具体的には、[Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) がインストールされているかどうかと、シェルが管理者特権で実行されているかどうかが確認されます。 次に、[Az.Storage 1.11.1-preview module](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) がインストールされているかどうかが確認され、まだの場合はインストールされます。 これらの確認に合格した場合は、AD DS が調べられ、[コンピューター アカウント](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (既定値)、あるいは "cifs/ここはご利用のストレージ アカウントの名前.file.core.windows.net" などの SPN または UPN を使用して既に作成されている[サービス ログオン アカウント](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)があるかどうかが確認されます。 アカウントが存在しない場合は、以下のセクション b の説明に従って作成します。

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. AD でのストレージ アカウントを表す ID の手動による作成

このアカウントを手動で作成するには、`New-AzStorageAccountKey -KeyName kerb1` を使用して、ストレージ アカウント用の新しい Kerberos キーを作成します。 次に、その Kerberos キーをアカウントのパスワードとして使用します。 このキーは、設定時にのみ使われ、ストレージ アカウントに対する制御やデータ プレーンの操作には使用できません。

そのキーを作成したら、OU の下にサービスまたはコンピューター アカウントを作成します。 次の指定を使用します。SPN: "cifs/ここはご利用のストレージ アカウントの名前.file.core.windows.net"。パスワード: ストレージ アカウントの Kerberos キー。

OU でパスワードの有効期限が適用されている場合は、パスワードの有効期間の前にパスワードを更新し、Azure ファイル共有にアクセスするときの認証エラーを防ぐ必要があります。 詳細については、[AD DS のストレージ アカウント ID のパスワードを更新する](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)方法に関する記事を参照してください。

新しく作成されたアカウントの SID を保持します。これは次の手順で必要になります。 ストレージ アカウントを表す、作成した ID を Azure AD に同期する必要はありません。

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. ストレージ アカウントで機能を有効にする

その後、スクリプトにより、ストレージ アカウントに対して機能が有効になります。 このセットアップを手動で実行するには、次のコマンドでドメイン プロパティの構成の詳細をいくつか指定してから実行します。 次のコマンドで必要なストレージ アカウント SID は、[前のセクション](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually)の AD DS で作成した ID の SID です。

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```


### <a name="13-confirm-that-the-feature-is-enabled"></a>1.3 機能が有効になっていることを確認する

次のスクリプトを使用して、ストレージ アカウントで機能が有効になっているかどうかを確認することができます。

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

これで、ストレージ アカウントで機能が正常に有効になりました。 機能は有効になりましたが、この機能を使用するために実行する必要がある手順はまだあります。

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

これで、SMB 経由の AD DS 認証が正常に有効になり、AD DS ID での Azure ファイル共有へのアクセスを提供するカスタム ロールが割り当てられました。 ファイル共有へのアクセスを他のユーザーに許可する場合は、ID を使用するための[アクセス許可の割り当て](#2-assign-access-permissions-to-an-identity)に関する指示と、「[SMB 経由の NTFS アクセス許可を構成する](#3-configure-ntfs-permissions-over-smb)」のセクションの手順に従ってください。

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5 AD DS のストレージ アカウント ID のパスワードを更新する

パスワードの有効期限を適用する OU の下のストレージ アカウントを表す AD DS の ID またはアカウントを登録した場合は、パスワードの有効期間の前にパスワードを交換する必要があります。 AD DS アカウントのパスワードの更新に失敗すると、Azure ファイル共有にアクセスする際に認証エラーが発生します。  

パスワードの交換をトリガーする場合は、AzFilesHybrid モジュールから `Update-AzStorageAccountADObjectPassword` コマンドを実行できます。 コマンドレットでは、ストレージ アカウント キーの交換と同様のアクションが実行されます。 ストレージ アカウントの 2 番目の Kerberos キーが取得され、それを使用して AD DS の登録済みアカウントのパスワードが更新されます。 次に、ストレージ アカウントのターゲット Kerberos キーが再生成され、AD DS の登録済みアカウントのパスワードが更新されます。 このコマンドレットは、オンプレミスの AD DS ドメインに参加している環境で実行する必要があります。

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>次のステップ

Azure Files や、SMB 経由での AD の使用方法の詳細については、これらのリソースを参照してください。

- [ SMB アクセスに対する Azure Files ID ベース認証サポートの概要](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
