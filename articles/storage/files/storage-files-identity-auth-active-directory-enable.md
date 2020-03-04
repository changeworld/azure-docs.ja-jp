---
title: Azure Files に対する SMB 経由の Active Directory 認証を有効にする
description: Active Directory を介した Azure ファイル共有に対する SMB 経由の ID ベース認証を有効にする方法を学習します。 ドメインに参加している Windows 仮想マシン (VM) では、AD 資格情報を使用して Azure ファイル共有にアクセスできます。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/23/2020
ms.author: rogarana
ms.openlocfilehash: 9bda3331bc27f387bd05ea0fcd29e7badf6d6a02
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651085"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Azure ファイル共有に対する SMB 経由の Active Directory 認証を有効にする

[Azure Files](storage-files-introduction.md)  では、次の 2 種類の Domain Services を介したサーバー メッセージ ブロック (SMB) 経由の ID ベース認証がサポートされています: Azure Active Directory Domain Services (Azure AD DS) (GA) と Active Directory (AD) (プレビュー)。 この記事では、Azure ファイル共有への認証に Active Directory Domain Services を利用するための、新しく導入された (プレビュー) サポートに焦点を当てています。 Azure ファイル共有に対して Azure AD DS (GA) 認証を有効にすることに関心をお持ちの場合は、[それに関する記事](storage-files-identity-auth-active-directory-domain-service-enable.md)を参照してください。

> [!NOTE]
> Azure ファイル共有では、Azure Active Directory Domain Services (Azure AD DS) または Active Directory (AD) のいずれか 1 つのドメイン サービスに対する認証のみがサポートされます。 
>
> Azure ファイル共有の認証に使用される AD ID は、Azure AD と同期されている必要があります。 パスワード ハッシュの同期はオプションです。 
> 
> AD 認証では、AD で作成されたコンピューター アカウントに対する認証はサポートされていません。 
> 
> AD 認証は、ストレージ アカウントが登録されている 1 つの AD フォレストに対してのみサポートされます。 既定では、単一の AD フォレストからのみ、AD 資格情報を使用して Azure ファイル共有にアクセスできます。 別のフォレストから Azure ファイル共有にアクセスする必要がある場合は、適切なフォレストの信頼が構成されていることを確認してください。詳細については、[FAQ](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) に関する記述を参照してください。  
> 
> SMB アクセスと ACL 永続化のための AD 認証は、Azure File Sync によって管理される Azure ファイル共有でサポートされます。
>
> Azure Files では、AD と RC4-HMAC 暗号化を使用した Kerberos 認証がサポートされています。 AES Kerberos 暗号化はまだサポートされていません。

SMB 経由の Azure ファイル共有に対して AD を有効にすると、AD ドメインに参加しているマシンで、既存の AD 資格情報を使用して Azure ファイル共有をマウントできます。 この機能は、オンプレミス マシンでホストされているか、Azure でホストされている AD 環境で有効にすることができます。

Azure ファイル共有へのアクセスに使用される AD ID は、標準的な[ロールベースのアクセス制御 (RBAC)](../../role-based-access-control/overview.md) モデルを使用して共有レベルのファイル アクセス許可を適用するために、Azure AD に同期する必要があります。 既存のファイル サーバーから引き継がれたファイルまたはディレクトリの [Windows スタイルの DACL](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) は保持され、適用されます。 この機能では、エンタープライズ AD ドメイン インフラストラクチャとのシームレスな統合が提供されます。 オンプレミス ファイル サーバーを Azure ファイル共有に置き換えると、既存のユーザーは、使用されている資格情報を変更することなく、シングル サインオン エクスペリエンスで現在のクライアントから Azure ファイル共有にアクセスできるようになります。  
 
## <a name="prerequisites"></a>前提条件 

Azure ファイル共有に対する AD 認証を有効にする前に、次の前提条件を完了していることを確認します。 

- AD 環境を選択または作成し、Azure AD に同期します。 

    新規または既存の AD 環境で機能を有効にすることができます。 アクセスに使用される ID は、Azure AD と同期されている必要があります。 アクセスする Azure AD テナントとファイル共有は、同じサブスクリプションに関連付けられている必要があります。 

    AD ドメイン環境をセットアップする場合は、「[Active Directory Domain Services の概要](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)」を参照してください。 Azure AD に AD を同期していない場合は、「[Azure Active Directory でのハイブリッド ID とは](../../active-directory/hybrid/whatis-hybrid-identity.md)」のガイダンスに従って、使用したい認証方法と Azure AD Connect セットアップ オプションを決定してください。 

- オンプレミス マシンまたは Azure VM を AD (AD DS ともいう) ドメインに参加させます。 

    マシンまたは VM から AD 資格情報を使用してファイル共有にアクセスするには、ご利用のデバイスが AD ドメインに参加している必要があります。 AD ドメインに参加させる方法については、「[コンピューターをドメインに参加させる](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)」を参照してください。 

- [サポートされているリージョン](#regional-availability)で Azure ストレージ アカウントを選択または作成します。 

    ファイル共有を含むストレージ アカウントが、Azure AD DS 認証用にまだ構成されていないことを確認します。 ストレージ アカウントで Azure Files Azure AD DS 認証が有効になっている場合は、AD を使用するように変更する前に無効にする必要があります。 これは、Azure AD DS 環境で構成された既存の ACL を、適切なアクセス許可を適用するために再構成する必要があることを意味します。
    
    新しいファイル共有の作成については、 [Azure Files でのファイル共有の作成](storage-how-to-create-file-share.md)に関するページを参照してください。
    
    最適なパフォーマンスが得られるように、共有にアクセスする予定の VM と同じリージョンにストレージ アカウントをデプロイすることをお勧めします。 

- ストレージ アカウント キーを使用して Azure ファイル共有をマウントすることで、接続を確認する。 

    ご利用のデバイスとファイル共有が正しく構成されていることを確認する場合は、ストレージ アカウント キーを使用してファイル共有をマウントしてみてください。 詳細については、「 [Windows で Azure ファイル共有を使用する](storage-how-to-use-files-windows.md)」を参照してください。

## <a name="regional-availability"></a>リージョン別の提供状況

Azure Files AD 認証は、[ほとんどのパブリック リージョン](https://azure.microsoft.com/global-infrastructure/regions/)で利用できます。まだ利用できないリージョンのサブセットは次のとおりです。

- 米国西部
- 米国西部 2
- 米国東部
- 米国東部 2
- 米国中南部
- 西ヨーロッパ
- 北ヨーロッパ

## <a name="workflow-overview"></a>ワークフローの概要

Azure ファイル共有に対して SMB 経由の AD 認証を有効にする前に、 [前提条件](#prerequisites) を参照し、すべての手順を完了したことを確認することをお勧めします。 前提条件では、ご利用の AD、Azure AD、および Azure Storage の各環境が正しく構成されていることを確認します。 

次に、AD 資格情報を使用して Azure Files リソースへのアクセス権を付与します。 

- ストレージ アカウントでの Azure Files AD 認証を有効にします。  

- ターゲットの AD ID と同期している、Azure AD ID (ユーザー、グループ、またはサービス プリンシパル) に、共有のアクセス許可を割り当てます。 

- SMB を使用して、ディレクトリとファイルの ACL を構成します。 

- AD ドメインに参加している VM から Azure ファイル共有をマウントします。 

次の図は、Azure ファイル共有に対する SMB 経由の Azure AD 認証を有効にするためのエンドツーエンド ワークフローを示しています。 

![Files AD ワークフロー図](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Azure ファイル共有に対する SMB 経由の AD 認証は、Windows 7 または Windows Server 2008 R2 より新しい OS バージョンで実行されているマシンまたは VM でのみサポートされます。 

## <a name="enable-ad-authentication-for-your-account"></a>アカウントに対する AD 認証を有効にする 

Azure ファイル共有に対する SMB 経由の AD 認証を有効にするには、最初にストレージ アカウントを AD に登録してから、ストレージ アカウントに必要なドメイン プロパティを設定する必要があります。 機能は、ストレージ アカウントで有効になると、アカウント内のすべての新規および既存のファイル共有に適用されます。 `join-AzStorageAccountForAuth` を使用して機能を有効にします。 エンドツーエンド ワークフローの詳細については、以下のセクションを参照してください。 

> [!IMPORTANT]
> `join-AzStorageAccountForAuth` コマンドレットでは、AD 環境に変更を加えます。 以下の説明をお読みになり、コマンドを実行するための適切なアクセス許可があることと、適用されている変更がコンプライアンスおよびセキュリティ ポリシーに沿っていることを確認するために行われる内容についてよりよく理解してください。 

`join-AzStorageAccountForAuth` コマンドレットでは、指定されたストレージ アカウントに代わってオフライン ドメイン参加に相当することを行います。 これにより、AD ドメインに、[コンピューター アカウント](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)または[サービス ログオン アカウント](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)のいずれかのアカウントが作成されます。 作成された AD アカウントは、AD ドメイン内のストレージ アカウントを表します。 AD アカウントが、パスワードの有効期限を適用する AD 組織単位 (OU) の下で作成されている場合は、パスワードの有効期間の前にパスワードを更新する必要があります。 AD アカウント パスワードの更新に失敗すると、Azure ファイル共有にアクセスするときに認証エラーが発生します。 パスワードを更新する方法については、「[AD アカウント パスワードを更新する](#update-ad-account-password)」を参照してください。

次のスクリプトを使用すると、登録を行い、機能を有効にすることができます。または、スクリプトで行われる操作を手動で行うことができます。 これらの操作については、スクリプトの後のセクションで説明します。 両方を行う必要はありません。

### <a name="1-check-prerequisites"></a>1.前提条件を確認する
- [AzFilesHybrid モジュールをダウンロードして解凍する](https://github.com/Azure-Samples/azure-files-samples/releases)
- ターゲット AD でサービス ログオン アカウントまたはコンピューター アカウントを作成する権限がある AD 資格情報を使用して、AD ドメインに参加しているデバイスにモジュールをインストールして実行します。
-  Azure AD に同期されている AD 資格情報を使用して、スクリプトを実行します。 AD 資格情報には、ストレージ アカウント所有者または共同作成者の RBAC ロールのアクセス許可が必要です。
- ストレージ アカウントが[サポートされているリージョン](#regional-availability)にあることを確認します。

### <a name="2-execute-ad-enablement-script"></a>2.AD 有効化スクリプトを実行する
PowerShell で実行する前に、必ず、以下のパラメーターのプレースホルダーの値を実際の値に置き換えてください。

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope Currentuser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

#Register the target storage account with your active directory environment under the target OU
join-AzStorageAccountForAuth -ResourceGroupName "<resource-group-name-here>" -Name "<storage-account-name-here>" -DomainAccountType "<ServiceLogonAccount|ComputerAccount>" -OrganizationUnitName "<ou-name-here>"
```

次の説明は、`join-AzStorageAccountForAuth` コマンドレットを実行したときに行われるすべてのアクションをまとめたものです。 コマンドを使用しない場合は、これらの手順を手動で行うことができます。

> [!NOTE]
> 前述の AzStorageAccountForAuth スクリプトを既に正常に実行している場合は、次のセクション「3. 機能が有効になっていることを確認する」に進んでください。 以下の操作をもう一度実行する必要はありません。

#### <a name="a-checking-environment"></a>a. 環境の確認

まず、環境が確認されます。 具体的には、[Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) がインストールされているかどうかと、シェルが管理者特権で実行されているかどうかが確認されます。 次に、[Az.Storage 1.11.1-preview module](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) がインストールされているかどうかが確認され、まだの場合はインストールされます。 これらの確認に合格した場合は、AD が調べられ、[コンピューター アカウント](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (既定値)、あるいは "cifs/ここはご利用のストレージ アカウントの名前.file.core.windows.net" などの SPN または UPN を使用して既に作成されている[サービス ログオン アカウント](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)があるかどうかが確認されます。 アカウントが存在しない場合は、以下のセクション b の説明に従って作成します。

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. AD でのストレージ アカウントを表す ID の手動による作成

このアカウントを手動で作成するには、`New-AzStorageAccountKey -KeyName kerb1` を使用して、ストレージ アカウント用の新しい kerberos キーを作成します。 次に、その kerberos キーをアカウントのパスワードとして使用します。 このキーは、設定時にのみ使われ、ストレージ アカウントに対する制御やデータ プレーンの操作には使用できません。

そのキーを作成したら、OU の下にサービスまたはコンピューター アカウントを作成します。 次の指定を使用します。SPN: "cifs/ここはご利用のストレージ アカウントの名前.file.core.windows.net"。パスワード: ストレージ アカウントの Kerberos キー。

OU でパスワードの有効期限が適用されている場合は、パスワードの有効期間の前にパスワードを更新し、Azure ファイル共有にアクセスするときの認証エラーを防ぐ必要があります。 詳細については、「[AD アカウントのパスワードを更新する](#update-ad-account-password)」を参照してください。

新しく作成されたアカウントの SID を保持します。これは次の手順で必要になります。

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. ストレージ アカウントで機能を有効にする

その後、スクリプトにより、ストレージ アカウントに対して機能が有効になります。 このセットアップを手動で実行するには、次のコマンドでドメイン プロパティの構成の詳細をいくつか指定してから実行します。 次のコマンドで必要なストレージ アカウント SID は、(上記のセクション b で) AD で作成した ID の SID です。

```PowerShell
#Set the feature flag on the target storage account and provide the required AD domain information

Set-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>" -EnableActiveDirectoryDomainServiesForFile $true -ActiveDirectoryDomainName "<your-domain-name-here>" -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" -ActiveDirectoryForestName "<your-forest-name-here>" -ActiveDirectoryDomainGuid "<your-guid-here>" -ActiveDirectoryDomainsid "<your-domain-sid-here>" -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```


### <a name="3-confirm-that-the-feature-is-enabled"></a>3.機能が有効になっていることを確認する

次のスクリプトを使用して、ストレージ アカウントで機能が有効になっているかどうかを確認することができます。

```PowerShell
#Get the target storage account
$storageaccount = Get-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>"

#List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

#List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

これで、ストレージ アカウントで機能が正常に有効になりました。 機能が有効になっている場合でも、機能を適切に使用できるようにするために、引き続き追加のアクションを実行する必要があります。

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

これで、SMB 経由の AD 認証が正常に有効になり、AD ID での Azure ファイル共有へのアクセスを提供するカスタム ロールが割り当てられました。 ファイル共有へのアクセスを他のユーザーに許可する場合は、ID を使用するための[アクセス許可の割り当て](#assign-access-permissions-to-an-identity)に関する指示と、「[SMB 経由の NTFS アクセス許可を構成する](#configure-ntfs-permissions-over-smb)」のセクションの手順に従ってください。

## <a name="update-ad-account-password"></a>AD アカウントのパスワードを更新する

パスワードの有効期限を適用する OU の下のストレージ アカウントを表す AD の ID またはアカウントを登録した場合は、パスワードの有効期間の前にパスワードを交換する必要があります。 AD アカウントのパスワードの更新に失敗すると、Azure ファイル共有にアクセスする際に認証エラーが発生します。  

パスワードの交換をトリガーする場合は、AzFilesHybrid モジュールから `Update-AzStorageAccountADObjectPassword` コマンドを実行できます。 コマンドレットでは、ストレージ アカウント キーの交換と同様のアクションが実行されます。 ストレージ アカウントの 2 番目の Kerberos キーが取得され、それを使用して AD の登録済みアカウントのパスワードが更新されます。 次に、ストレージ アカウントのターゲット Kerberos キーが再生成され、AD の登録済みアカウントのパスワードが更新されます。 このコマンドレットは、AD ドメインに参加している環境で実行する必要があります。

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword -RotateToKerbKey kerb2 -ResourceGroupName "your-resource-group-name-here" -StorageAccountName "your-storage-account-name-here"
```

## <a name="next-steps"></a>次のステップ

Azure Files や、SMB 経由での AD の使用方法の詳細については、これらのリソースを参照してください。

- [ SMB アクセスに対する Azure Files ID ベース認証サポートの概要](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
