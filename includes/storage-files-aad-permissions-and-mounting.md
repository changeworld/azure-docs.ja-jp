---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 7246a072c1bf2253b822fca53b0b69700c66221d
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565260"
---
## <a name="assign-access-permissions-to-an-identity"></a>ID にアクセス許可を割り当てる

ID ベースの認証を使用して Azure Files リソースにアクセスするには、ID (ユーザー、グループ、またはサービス プリンシパル) が共有レベルで必要なアクセス許可を持っている必要があります。 このプロセスは、ファイル共有に対し特定のユーザーが持っているアクセス権の種類を指定する、Windows 共有のアクセス許可の指定に似ています。 このセクションのガイダンスでは、ID にファイル共有のための読み取り、書き込み、または削除のアクセス許可を割り当てる方法を示します。

ユーザーに共有レベルのアクセス許可を付与するための、3 つの Azure 組み込みロールが導入されました。

- **ストレージ ファイル データ SMB 共有閲覧者**では、SMB 経由の Azure Storage ファイル共有での読み取りアクセスが許可されます。
- **ストレージ ファイル データ SMB 共有共同作成者**では、SMB 経由の Azure Storage ファイル共有での読み取り、書き込み、削除アクセスが許可されます。
- **ストレージ ファイル データの SMB 共有の管理者特権共同作成者**では、SMB 経由の Azure Storage ファイル共有での NTFS アクセス許可の読み取り、書き込み、削除、および変更が許可されます。

> [!IMPORTANT]
> ID にロールを割り当てる機能など、ファイル共有を完全に管理制御するには、ストレージ アカウント キーを使用する必要があります。 Azure AD 資格情報を使用した管理制御はサポートされていません。

Azure portal、PowerShell、または Azure CLI を使用して、共有レベルのアクセス許可を付与するために、組み込みのロールをユーザーの Azure AD ID に割り当てることができます。

> [!NOTE]
> AD を認証に使用する予定がある場合は、AD 資格情報を Azure AD に同期することを忘れないでください。 AD から Azure AD へのパスワード ハッシュ同期は省略可能です。 共有レベルのアクセス許可は、AD から同期されている Azure AD ID に付与されます。

#### <a name="azure-portal"></a>Azure portal
[Azure portal](https://portal.azure.com) を使用して RBAC ロールを Azure AD ID に割り当てるには、次の手順に従います。

1. Azure portal でファイル共有に移動するか、[ファイル共有を作成](../articles/storage/files/storage-how-to-create-file-share.md)します。
2. **[アクセス制御 (IAM)]** を選択します。
3. **[ロールの割り当てを追加する]** を選択します。
4. **[ロールの割り当ての追加]** ブレードで、 **[ロール]** リストから適切な組み込みロール (ストレージ ファイル データ SMB 共有閲覧者、ストレージ ファイル データ SMB 共有共同作成者) を選択します。 **[アクセスの割り当て先]** は既定の設定のままにしておきます。**Azure AD のユーザー、グループ、サービス プリンシパル**。 名前またはメール アドレスで、ターゲット Azure AD ID を選択します。
5. **[保存]** を選択して、ロールの割り当て操作を完了します。

#### <a name="powershell"></a>PowerShell

次の PowerShell サンプルは、RBAC ロールをサインイン名に基づいて Azure AD ID に割り当てる方法を示しています。 PowerShell を使用した RBAC ロールの割り当ての詳細については、「[RBAC と Azure PowerShell を使用してアクセスを管理する](../articles/role-based-access-control/role-assignments-powershell.md)」を参照してください。

次のサンプル スクリプトを実行する前に、プレースホルダー値 (かっこを含む) を独自の値に置き換えることを忘れないでください。

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
次の CLI 2.0 コマンドでは、RBAC ロールをサインイン名に基づいて Azure AD ID に割り当てる方法が示されています。 Azure CLI を使用した RBAC ロールの割り当ての詳細については、「[RBAC と Azure CLI を使用してアクセスを管理する](../articles/role-based-access-control/role-assignments-cli.md)」を参照してください。 

次のサンプル スクリプトを実行する前に、プレースホルダー値 (かっこを含む) を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>SMB 経由の NTFS アクセス許可を構成する 
RBAC に共有レベルのアクセス許可を割り当てたら、ルート、ディレクトリ、またはファイル レベルに適切な NTFS アクセス許可を割り当てる必要があります。 共有レベルのアクセス許可は、ユーザーが共有にアクセスできるかどうかを決定する高レベルのゲートキーパーと考えてください。 一方、NTFS のアクセス許可は、さらに細かなレベルで動作し、ディレクトリまたはファイル レベルでユーザーが実行できる操作を決定します。

Azure Files では、NTFS の基本的なアクセス許可と詳細なアクセス許可で構成される完全なセットをサポートします。 Azure ファイル共有内のディレクトリとファイルの NTFS アクセス許可を表示および構成するには、共有をマウントしてから、Windows エクスプローラーを使用するか、Windows の [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) または [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) コマンドを実行します。 

スーパーユーザー アクセス許可を持つ NTFS を構成するには、ドメインに参加している VM からのストレージ アカウント キーを使って共有をマウントする必要があります。 コマンド プロンプトから Azure ファイル共有をマウントし、それに応じて NTFS アクセス許可を構成するには、次のセクションの説明に従ってください。

ファイル共有のルート ディレクトリでは、次のアクセス許可セットがサポートされています。

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT authority \authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="configure-ntfs-permissions-with-icacls"></a>icacls を使用して NTFS アクセス許可を構成する
ルート ディレクトリを含む、ファイル共有下のすべてのディレクトリとファイル共有に完全なアクセス許可を付与するには、次の Windows コマンドを使用します。 例中のプレースホルダーをお客様独自の値に置き換えてください。

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

icacls を使用して NTFS アクセス許可を設定する方法や、サポートされるさまざまな種類のアクセス許可の詳細については、[コマンド ライン リファレンスの icacls ](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)に関する記事を参照してください。

### <a name="mount-a-file-share-from-the-command-prompt"></a>コマンド プロンプトからファイル共有をマウントする

Windows の **net use** コマンドを使用して Azure ファイル共有をマウントします。 次の例では、プレースホルダーをお客様独自の値に置き換えてください。 ファイル共有のマウントの詳細については、「[Windows で Azure ファイル共有を使用する](../articles/storage/files/storage-how-to-use-files-windows.md)」を参照してください。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Windows エクスプローラーを使用して NTFS アクセス許可を構成する
Windows エクスプローラーを使用して、ルート ディレクトリを含む、ファイル共有下のすべてのディレクトリとファイル共有に完全なアクセス許可を付与します。

1. Windows エクスプローラーを開き、ファイルまたはディレクトリを右クリックし、 **[プロパティ]** を選択します
2. **[セキュリティ]** タブをクリックします
3. **[編集]** をクリックして、アクセス許可を変更します
4. 既存のユーザーの権限を変更することも、 **[追加]** をクリックして新しいユーザーにアクセス許可を付与することもできます
5. 新しいユーザーを追加するためのプロンプト ウィンドウで、アクセス許可を付与するターゲット ユーザーの名前を **[選択するオブジェクト名を入力してください]** ボックスに入力し、 **[名前の確認]** をクリックして、ターゲット ユーザーの完全な UPN 名を見つけます。
7.  [設定] メニューの **[OK]**
8.  [セキュリティ] タブで、新しく追加したユーザーに付与するすべてのアクセス許可を選択します
9.  **[適用]** をクリックします

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>ドメインに参加している VM からファイル共有をマウントする

次のプロセスでは、ファイル共有とアクセス許可が正しく設定されていることと、ドメインに参加している VM から Azure ファイル共有にアクセスできることを確認します。

次の図のように、アクセス許可を付与した Azure AD の ID を使用して VM にサインインします。 Azure Files の AD 認証を有効にしている場合は、AD の資格情報を使用します。 Azure AD DS 認証の場合、Azure AD の資格情報を使用してログインします。

![ユーザー認証のための Azure AD サインイン画面を示すスクリーン ショット](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

以下のコマンドを使用して Azure ファイル共有をマウントします。 プレースホルダーをお客様独自の値に置き換えてください。 認証済みなので、ストレージ アカウント キー、AD の資格情報、または Azure AD の資格情報を指定する必要はありません。 シングル サインオン エクスペリエンスは、AD または Azure AD DS での認証ではサポートされています。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```