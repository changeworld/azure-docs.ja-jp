---
title: SMB を使用して Azure Files への Azure Active Directory 認証を有効にする (プレビュー) - Azure Storage
description: Azure Active Directory (Azure AD) Domain Services を使用し SMB (Server Message Block) 経由で ID ベースの認証を有効にする方法 (プレビュー) について説明します。 ドメインに参加している Windows 仮想マシン (VM) は、Azure AD の資格情報を使用して Azure ファイル共有にアクセスできます。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/19/2018
ms.author: tamram
ms.openlocfilehash: 6ee80aa7b7a58e2f02ed36d3c0c4b1a0889a906f
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831468"
---
# <a name="enable-azure-active-directory-authentication-over-smb-for-azure-files-preview"></a>SMB を使用して Azure Files への Azure Active Directory 認証を有効にする (プレビュー)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

SMB を使用した Azure Files への Azure AD 認証の概要については、[SMB を使用した Azure Files への Azure Active Directory 認証 (プレビュー) の概要](storage-files-active-directory-overview.md)に関する記事を参照してください。

## <a name="workflow-overview"></a>ワークフローの概要
SMB を使用した Azure Files への Azure AD 認証を有効にする前に、Azure AD と Azure Storage 環境が正しく構成されていることを確認してください。 [前提条件](#prerequisites)を参照して、必要な手順をすべて実行したことを確認することをお勧めします。 

次に、以下の手順を実行して、Azure AD 資格情報を使用して Azure Files のリソースへのアクセス許可を付与します。 

1. 関連付けられた Azure AD Domain Services デプロイにストレージ アカウントを登録するため、SMB を使用した、ストレージ アカウントの Azure AD 認証を有効にします。
2. Azure AD の ID (ユーザー、グループ、またはサービス プリンシパル) に、共有のアクセス許可を割り当てます。
3. SMB を使用したディレクトリおよびファイルへの NTFS アクセス許可を構成します。
4. ドメインに参加している VM から Azure ファイル共有をマウントします。

次の図は、SMB を使用して Azure Files への Azure AD 認証を有効にするためのエンド ツー エンドのワークフローを示しています。 

![SMB を使用した Azure Files への Azure AD ワークフローを示す図](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>前提条件 
1.  **Azure AD テナントを選択または作成します。**

    SMB を使用した Azure AD 認証には、新規または既存のテナントを使用できます。 アクセスするテナントとファイル共有は、同じサブスクリプションに関連付けられている必要があります。

    新しく Azure AD テナントを作成するには、[Azure AD テナントと Azure AD サブスクリプションを追加](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)できます。 既存の Azure AD テナントがあるが、Azure Files で使用するために新しいテナントを作成する場合は、「[Azure Active Directory テナントを作成する](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)」を参照してください。

2.  **Azure AD テナントで Azure AD Domain Services を有効にします。**

    Azure AD 資格情報を使用した認証をサポートするには、Azure AD テナントの Azure AD Domain Services を有効にする必要があります。 Azure AD テナントの管理者でない場合は、管理者に連絡し、[Azure portal を使用した Azure Active Directory Domain Services の有効化](../../active-directory-domain-services/active-directory-ds-getting-started.md)に関する記事に書かれている手順を実行します。

    通常、Azure AD Domain Services のデプロイが完了するまでには 15 分ほどかかります。 次の手順に進む前に、Azure AD Domain Services の正常性状態が**実行中**と表示されており、パスワード ハッシュ同期が有効になっていることを確認します。

3.  **Azure AD Domain Services を使用している Azure VM ドメインに参加します。**

    VM から Azure AD の資格情報を使用してファイル共有にアクセスするには、VM が Azure AD Domain Services のドメインに参加している必要があります。 VM のドメイン参加方法の詳細については、「[Windows Server 仮想マシンのマネージド ドメインへの参加](../../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal.md)」を参照してください。

    > [!NOTE]
    > SMB を使用した Azure Files の Azure AD 認証は、Windows 7 または Windows Server 2008 R2 以降の OS バージョンで実行されている Azure VM でのみサポートされます。

4.  **Azure ファイル共有を選択または作成します。**

    Azure AD テナントと同じサブスクリプションに関連付けられている新規または既存のファイル共有を選択します。 新しいファイル共有を作成する方法の詳細については、「[Azure Files にファイル共有を作成する](storage-how-to-create-file-share.md)」を参照してください。 

    Azure AD テナントは、SMB を使用した Azure AD のプレビューがサポートされているリージョンにデプロイする必要があります。 プレビューは、米国西部、米国西部 2、米国中南部、米国東部、米国東部 2、米国中部、米国中北部、オーストラリア東部、西ヨーロッパ、北ヨーロッパを除くすべてのパブリック リージョンで利用可能です。

    パフォーマンスを最適化するには、共有にアクセスする予定の VM と同じリージョンにファイル共有を配置することをお勧めします。

5.  **ストレージ アカウント キーを使用して Azure ファイル共有をマウントすることにより、Azure Files の接続を確認します。**

    VM とファイル共有が正しく構成されていることを確認するには、ストレージ アカウント キーを使用してファイル共有をマウントします。 詳細については、「[Windows で Azure ファイル共有をマウントして共有にアクセスする](storage-how-to-use-files-windows.md)」を参照してください。

## <a name="enable-azure-ad-authentication"></a>Azure AD 認証を有効にする
[前提条件](#prerequisites)を満たしたら、SMB を使用した Azure AD 認証を有効にすることができます。

### <a name="step-1-enable-azure-ad-authentication-over-smb-for-your-storage-account"></a>手順 1: SMB を使用した、ストレージ アカウントへの Azure AD 認証を有効にする
SMB を使用した Azure Files への Azure AD 認証を有効にするには、PowerShell または Azure CLI からの Azure ストレージ リソース プロバイダーを使用して、2018 年 8 月 29 日以降に作成されたストレージ アカウントにプロパティを設定できます。 プレビュー リリースでは、Azure portal でプロパティを設定することはできません。 

このプロパティを設定すると、ストレージ アカウントが、関連付けられている Azure AD Domain Services デプロイに登録されます。 登録されると、ストレージ アカウント内のすべての新規および既存のファイル共有に対する、SMB を使用した Azure AD 認証が有効になります。 

SMB を使用した Azure AD 認証を有効にするには、Azure AD テナントに Azure AD Domain Services が正常にデプロイされている必要があることに注意してください。 詳細については、[前提条件](#prerequisites)を参照してください。

**Powershell**  
SMB を使用した Azure AD 認証を有効にするには `AzureRM.Storage 6.0.0-preview` PowerShell モジュールをインストールします。 PowerShell モジュールのインストールの詳細については、[PowerShellGet を使用した Windows への Azure PowerShell のインストール](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)に関する記事を参照してください。

次に、[Set-azurermstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/set-azurermstorageaccount) を呼び出し、**EnableAzureFilesAadIntegrationForSMB** パラメーターを **true** に設定します。 以下の例のプレースホルダーをお客様独自の値に置き換えてください。

```powershell
# Create a new storage account
New-AzureRmStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureFilesAadIntegrationForSMB $true

# Update an existing storage account
# Supported for storage accounts created after August 29, 2018 only
Set-AzureRmStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureFilesAadIntegrationForSMB $true```
```

**CLI**  
Azure CLI 2.0 から SMB を使用した Azure AD 認証を有効にするには、まず、*storage-preview* 拡張機能をインストールします。

```azurecli-interactive
az extension add --name storage-preview
```

次に、[az storage account update](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) を呼び出し、`--file-aad` プロパティを **true** に設定します。 以下の例のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true

# Update an existing storage account
# Supported for storage accounts created after August 29, 2018 only
az storage account update -n <storage-account-name> -g <resource-group-name> --file-aad true
```

### <a name="step-2-assign-access-permissions-to-an-identity"></a>手順 2: ID にアクセス許可を割り当てる 
Azure AD の資格情報を使用して Azure Files のリソースにアクセスするには、ID (ユーザー、グループ、またはサービス プリンシパル) に、共有レベルで必要なアクセス許可が必要です。 以下のステップ バイ ステップ ガイダンスでは、ファイル共有の読み取り、書き込み、または削除操作のアクセス許可を ID に割り当てる方法を示します。

> [!IMPORTANT]
> ID にロールを割り当てる機能など、ファイル共有を完全に管理制御するには、ストレージ アカウント キーを使用する必要があります。 Azure AD 資格情報を使用した管理制御はサポートされていません。 

#### <a name="step-21-define-a-custom-role"></a>手順 2.1: カスタム ロールを定義する
共有レベルのアクセス許可を付与するには、カスタム RBAC ロールを定義してそれを ID に割り当て、特定のファイル共有へのスコープに割り当てます。 このプロセスは、ファイル共有に対し特定のユーザーが持っているアクセス権の種類を指定する、Windows 共有のアクセス許可の指定に似ています。  

次のセクションで示したテンプレートでは、ファイル共有の読み取りまたは変更のアクセス許可を付与します。 カスタム ロールを定義するには、JSON ファイルを作成し、適切なテンプレートをそのファイルにコピーします。 カスタム RBAC ロールの定義の詳細については、「[Azure のカスタム ロール](../../role-based-access-control/custom-roles.md)」を参照してください。

**共有レベルの変更アクセス許可のためのロールの定義**  
次のカスタム ロール テンプレートは、共有レベルの変更アクセス許可を提供し、ID に共有への読み取り、書き込み、削除アクセス許可を付与します。

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read, write and delete access to Azure File Share",
  "Actions": [
    "*"
  ],
  "NotActions": [
      "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [
    "*"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

**共有レベルの読み取りアクセス許可のためのロールの定義**  
次のカスタム ロール テンプレートでは、共有レベルの読み取りアクセス許可を提供し、ID に共有の読み取りアクセス許可を付与します。

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure File Share",
  "Actions": [
    "*/read"
  ],
  "DataActions": [
    "*/read"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

#### <a name="step-22-create-the-custom-role-and-assign-it-to-the-target-identity"></a>手順 2.2: カスタム ロールを作成し、対象の ID に割り当てる
次に、PowerShell または Azure CLI を使用してロールを作成し、Azure AD の ID に割り当てます。 

**Powershell**  
SMB を使用した Azure AD 認証を有効にするには `AzureRM.Storage 6.0.0-preview` PowerShell モジュールをインストールします。 PowerShell モジュールのインストールの詳細については、[PowerShellGet を使用した Windows への Azure PowerShell のインストール](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)に関する記事を参照してください。

次の PowerShell コマンドは、カスタム ロールを作成し、サインイン名に基づいて Azure AD ID にロールを割り当てます。 PowerShell を使用した RBAC ロールの割り当ての詳細については、「[RBAC と Azure PowerShell を使用してアクセスを管理する](../../role-based-access-control/role-assignments-powershell.md)」を参照してください。

次のサンプル スクリプトを実行する際には、プレース ホルダーの値をお客様独自の値に置き換えてください。

```powershell
#Create a custom role based on the sample template above
New-AzureRmRoleDefinition -InputFile "<custom-role-def-json-path>"
#Get the name of the custom role
$FileShareContributorRole = Get-AzureRmRoleDefinition "<role-name>"
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzureRmRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

**CLI**  
次の CLI 2.0 コマンドは、カスタム ロールを作成し、サインイン名に基づいて、Azure AD ID にロールを割り当てます。 Azure CLI を使用した RBAC ロールの割り当ての詳細については、「[RBAC と Azure CLI を使用してアクセスを管理する](../../role-based-access-control/role-assignments-cli.md)」を参照してください。 

次のサンプル スクリプトを実行する際には、プレース ホルダーの値をお客様独自の値に置き換えてください。

```azurecli-interactive
#Create a custom role based on the sample templates above
az role definition create --role-definition "<Custom-role-def-JSON-path>"
#List the custom roles
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
#Assign the custom role to the target identity
az role assignment create --role "<custome-role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

### <a name="step-3-configure-ntfs-permissions-over-smb"></a>手順 3: SMB を使用した NTFS アクセス許可を構成する 
RBAC に共有レベルのアクセス許可を割り当てたら、ルート、ディレクトリ、またはファイル レベルに適切な NTFS アクセス許可を割り当てる必要があります。 共有レベルのアクセス許可は、ユーザーが共有にアクセスできるかどうかを決定する高度なゲートキーパーであり、NTFS アクセス許可はユーザーがディレクトリまたはファイル レベルでどんな操作を実行できるかを規定し、より詳細なレベルで動作すると考えることができます。 

Azure Files では、NTFS の基本的なアクセス許可と詳細なアクセス許可で構成される完全なセットをサポートします。 共有をマウントし、Windows の [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) または [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) コマンドを実行することにより、Azure ファイル共有内のディレクトリとファイルに対する NTFS アクセス許可を表示し構成できます。 

> [!NOTE]
> プレビュー リリースでは、Windows ファイル エクスプローラーを使用したアクセス許可の表示のみがサポートされています。 アクセス許可の編集はまだサポートされていません。

スーパー ユーザー権限を持つ NTFS アクセス許可を構成するには、ドメインに参加している VM からのストレージ アカウント キーを使って共有をマウントする必要があります。 コマンド プロンプトから Azure ファイル共有をマウントし、それに応じて NTFS アクセス許可を構成するには、次のセクションの説明に従ってください。

ファイル共有のルート ディレクトリでは、次のアクセス許可セットがサポートされています。

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT authority \authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

#### <a name="step-31-mount-an-azure-file-share-from-the-command-prompt"></a>手順 3.1: コマンド プロンプトから Azure ファイル共有をマウントする
Windows の **net use** コマンドを使用して Azure ファイル共有をマウントします。 例中のプレースホルダーをお客様独自の値に置き換えてください。 ファイル共有のマウントの詳細については、「[Windows で Azure ファイル共有をマウントして共有を使用する](storage-how-to-use-files-windows.md)」を参照してください。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

#### <a name="step-32-configure-ntfs-permissions-with-icacls"></a>手順 3.2: icacls を使用して NTFS アクセス許可を構成する
ルート ディレクトリを含む、ファイル共有下のすべてのディレクトリとファイル共有に完全なアクセス許可を付与するには、次の Windows コマンドを使用します。 例中のプレースホルダーをお客様独自の値に置き換えてください。

```
icacls <mounted-drive-letter> /grant <user-email>:(f)
```

icacls を使用して NTFS アクセス許可を設定する方法や、サポートされるさまざまな種類のアクセス許可の詳細については、[コマンド ライン リファレンスの icacls ](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)に関する記事を参照してください。

### <a name="step-4-mount-an-azure-file-share-from-a-domain-joined-vm"></a>手順 4: ドメインに参加している VM から Azure ファイル共有をマウントする 
上記の手順が正常に完了したことを確認する準備が整いましたので、Azure AD 資格情報を使用して、ドメインに参加している VM から Azure のファイル共有にアクセスします。 まず、次の図のように、アクセス許可を付与した Azure AD の ID を使用して VM にサインインします。

![ユーザー認証のための Azure AD サインイン画面を示すスクリーン ショット](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

次に、以下のコマンドを使用して Azure ファイル共有をマウントします。 プレースホルダーをお客様独自の値に置き換えてください。 既に認証されているため、ストレージ アカウント キーまたは Azure AD のユーザー名やパスワードを入力する必要はありません。 SMB を使用する Azure AD では、Azure AD の資格情報を使用したシングル サインオン エクスペリエンスがサポートされています。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

これで、SMB を使用した Azure AD 認証を有効にし、ファイル共有へのアクセスを提供するカスタム ロールが Azure AD の ID に割り当てられました。 追加のユーザーにファイル共有へのアクセス許可を付与するには、手順 2 と 3 で説明されている手順を実行します。

## <a name="next-steps"></a>次の手順
Azure Files や、SMB 経由で Azure AD を使用する方法の詳細については、これらのリソースを参照してください。

- [Azure Files の概要](storage-files-introduction.md)
- [SMB を使用して Azure Files への Azure Active Directory 認証を有効にする方法の概要 (プレビュー)](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)