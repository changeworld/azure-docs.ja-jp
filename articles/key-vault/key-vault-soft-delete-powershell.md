---
ms.assetid: ''
title: Azure Key Vault - PowerShell で論理的な削除を使用する方法
description: PowerShell コード スニペットを使用した論理的な削除のユース ケースの例
services: key-vault
author: bryanla
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 10/16/2018
ms.author: bryanla
ms.openlocfilehash: 99f81e14ca631eccee154a5658bf717cbe07b3da
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364372"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>PowerShell で Key Vault の論理的な削除を使用する方法

Azure Key Vault の論理的な削除機能を使用すると、削除されたコンテナーとコンテナー オブジェクトを復旧できます。 具体的には、論理的な削除は次のシナリオに対処します。

- キー コンテナーの回復可能な削除のサポート
- キー コンテナー オブジェクトの回復可能な削除のサポート (例: キー、シークレット、証明書)

## <a name="prerequisites"></a>前提条件

- Azure PowerShell 4.0.0 以上: まだセットアップしていない場合は、Azure PowerShell をインストールしてお使いの Azure サブスクリプションに関連付けます。詳しくは、「[Azure PowerShell のインストールおよび構成方法](https://docs.microsoft.com/powershell/azure/overview)」をご覧ください。 

>[!NOTE]
> Key Vault PowerShell 出力書式設定ファイルには古いバージョンがあり、正しいバージョンではなく、これが環境に読み込まれる**可能性があります**。 PowerShell の更新バージョンには出力書式設定に必要な修正が含まれることを想定しており、このトピックはその時点で更新されます。 この出力書式設定に関する問題が発生した場合の現在の対処法は次のとおりです。
> - このトピックで説明されている論理的な削除が有効なプロパティが表示されない場合は、クエリ `$vault = Get-AzureRmKeyVault -VaultName myvault; $vault.EnableSoftDelete` を使用します。


PowerShell における Key Vault の具体的な参照情報については、[Azure Key Vault の PowerShell のリファレンス](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0)に関するページをご覧ください。

## <a name="required-permissions"></a>必要なアクセス許可

Key Vault の操作は、次のようにロールベースのアクセス制御 (RBAC) のアクセス許可で別個に管理されます。

| Operation | 説明 | ユーザーのアクセス許可 |
|:--|:--|:--|
|List|削除されたキー コンテナーの一覧を示します。|Microsoft.KeyVault/deletedVaults/read|
|復旧|削除されたキー コンテナーを復元します。|Microsoft.KeyVault/vaults/write|
|消去|削除されたキー コンテナーとそのコンテンツを永続的に削除します。|Microsoft.KeyVault/locations/deletedVaults/purge/action|

アクセス許可とアクセスの制御について詳しくは、「[キー コンテナーのセキュリティ保護](key-vault-secure-your-key-vault.md)」をご覧ください。

## <a name="enabling-soft-delete"></a>論理的な削除を有効にする

"論理的な削除" を有効にすると、削除されたキー コンテナーや、キー コンテナーに格納されているオブジェクトを復旧できるようになります。

> [!IMPORTANT]
> キー コンテナーに対して '論理的な削除' を有効にした場合、それを取り消すことはできません。 論理的な削除のプロパティを "true" に設定すると、それを変更したり削除することはできなくなります。  

### <a name="existing-key-vault"></a>既存のキー コンテナー

ContosoVault という名前の既存のキー コンテナーの場合、次のようにして論理的な削除を有効にします。 

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzureRmResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>新しいキー コンテナー

新しいキー コンテナーの論理的な削除は、作成時に create コマンドに論理的な削除を有効にするフラグを追加することで有効にできます。

```powershell
New-AzureRmKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>論理的な削除が有効になっていることを確認する

キー コンテナーで論理的な削除が有効になっていることを確認するには、*show* コマンドを実行して 'Soft Delete Enabled?' 属性を探します。

```powershell
Get-AzureRmKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>論理的な削除で保護されているキー コンテナーを削除する

キー コンテナーを削除するコマンドの動作は、論理的な削除が有効になっているかどうかによって変わります。

> [!IMPORTANT]
>論理的な削除が有効になっていない状態でキー コンテナーで次のコマンドを実行すると、このキー コンテナーとそのコンテンツがすべて永続的に削除され、復旧できません。

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>論理的な削除がキー コンテナーを保護する方法

論理的な削除を有効にした場合:

- 削除されたキー コンテナーはそのリソース グループから削除され、予約された名前空間に配置されます。この名前空間はそれが作成された場所に関連付けられています。 
- 削除されたオブジェクト (キー、シークレット、証明書など) にはアクセスできず、それらオブジェクトが含まれるキー コンテナーが削除されている状態にある間はアクセスできません。 
- 削除されたキー コンテナーの DNS 名は予約されているため、新しいキー コンテナーを同じ名前で作成することはできません。  

サブスクリプションに関連付けられている削除された状態のキー コンテナーは、次のコマンドを使用して表示できます。

```powershell
PS C:\> Get-AzureRmKeyVault -InRemovedState 
```

- *Id* は、復旧または消去するときにリソースを識別するために使用できます。 
- *Resource ID* は、そのコンテナーの元のリソース ID です。 このキー コンテナーは削除された状態にあるため、このリソース ID のリソースは存在しません。 
- *Scheduled Purge Date* は、何のアクションも実行しない場合に、そのコンテナーが永続的に削除されるタイミングを示します。 *Scheduled Purge Date* の計算に使用される既定のリテンション期間は 90 日です。

## <a name="recovering-a-key-vault"></a>キー コンテナーの復旧

キー コンテナーを復旧するには、キー コンテナー、リソース グループ、および場所を指定します。 削除されたキー コンテナーの場所とリソース グループは、復旧プロセスに必要となるのでメモしておいてください。

```powershell
Undo-AzureRmKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

キー コンテナーを復旧すると、そのキー コンテナーの元のリソース ID を持つ新しいリソースが作成されます。 元のリソース グループが削除された場合は、復旧を試行する前に、同じ名前のリソース グループを作成する必要があります。

## <a name="key-vault-objects-and-soft-delete"></a>Key Vault のオブジェクトと論理的な削除

次のコマンドを実行すると、論理的な削除が有効になっている、'ContosoVault' という名前のキー コンテナーから、'ContosoFirstKey' キーが削除されます。

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

キー コンテナーで論理的な削除が有効になっている場合、削除されたキーは、削除済みのキーを明示的に一覧表示しないかぎり、削除されたように見えます。 削除された状態のキーに対するほとんどの操作は、削除されたキーの一覧表示、復旧、または消去を除いて失敗します。 

たとえば、次のコマンドを実行すると、'ContosoVault' キー コンテナーの削除済みのキーが一覧表示されます。

```powershell
Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>切り替え状態 

論理的な削除が有効なキー コンテナーでキーを削除すると、切り替えが完了するまでに数秒かかる場合があります。 この切り替えの間、キーがアクティブな状態でも削除された状態でもなくなる場合があります。 

### <a name="using-soft-delete-with-key-vault-objects"></a>キー コンテナー オブジェクトで論理的な削除を使用する

キー コンテナーと同様に、削除されたキー、シークレット、証明書は、復旧または消去しない限り、最大で 90 日間、削除済みの状態で維持されます。 

#### <a name="keys"></a>構成する

論理的に削除されたキーを復旧するには:

```powershell
Undo-AzureKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

論理的に削除されたキーを完全に削除 (消去) するには:

> [!IMPORTANT]
> キーを消去するとキーは永続的に削除され、復旧できなくなります。 

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

**recover** アクションと **purge** アクションには、キー コンテナーのアクセス ポリシーに独自のアクセス許可が関連付けられています。 ユーザーまたはサービス プリンシパルが **recover** アクションまたは **purge** アクションを実行するには、そのキーまたはシークレットに対する適切なアクセス許可が必要です。 既定では、'all' ショートカットを使用してすべてのアクセス許可を付与するときに、**purge** はキー コンテナーのアクセス ポリシーには追加されません。 **purge** のアクセス許可は明示的に付与する必要があります。 

#### <a name="set-a-key-vault-access-policy"></a>キー コンテナーのアクセス ポリシーを設定する

次のコマンドでは、*ContosoVault* のキーに対する複数の操作 (**purge** を含む) を使用するために、user@contoso.com のアクセス許可を付与しています。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> 論理的な削除を有効にした直後の既存のキー コンテナーの場合、**recover** や **purge** のアクセス許可がない場合があります。

#### <a name="secrets"></a>シークレット

キーと同様、シークレットも独自のコマンドで管理されます。

- SQLPassword という名前のシークレットを削除します。 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -name SQLPassword
```

- キー コンテナー内の削除されたシークレットをすべて一覧表示します。 
```powershell
Get-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState
```

- 削除された状態のシークレットを復旧します。 
```powershell
Undo-AzureKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
```

- 削除された状態のシークレットを消去します。 

  > [!IMPORTANT]
  > シークレットを消去するとシークレットは永続的に削除され、復旧できなくなります。

  ```powershell
  Remove-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

## <a name="purging-and-key-vaults"></a>消去とキー コンテナー

### <a name="key-vault-objects"></a>キー コンテナー オブジェクト

キー、シークレット、または証明書は消去すると、それらは永続的に削除され、復旧できなくなります。 ただし、削除されたオブジェクトが含まれるキー コンテナーは、そのキー コンテナー内のすべてのオブジェクトと同様にそのまま残ります。 

### <a name="key-vaults-as-containers"></a>コンテナーとしてのキー コンテナー
キー コンテナーを消去すると、そのすべてのコンテンツ (キー、シークレット、証明書など) が完全に削除されます。 キー コンテナーを消去するには、`Remove-AzureRmKeyVault` コマンドをオプション `-InRemovedState` を使用および `-Location location` 引数を使用して削除されたキー コンテナーの場所を指定して実行します。 削除されたコンテナーの場所は、`Get-AzureRmKeyVault -InRemovedState` コマンドを使用して見つけることができます。

>[!IMPORTANT]
>キー コンテナーを消去するとキー コンテナーは永続的に削除され、復旧できなくなります。

```powershell
Remove-AzureRmKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>必要な消去のアクセス許可
- 削除されたキー コンテナーを消去するには、*Microsoft.KeyVault/locations/deletedVaults/purge/action* 操作に対する RBAC アクセス許可が必要になります。 
- 削除されたキー コンテナーを一覧表示するには、*Microsoft.KeyVault/deletedVaults/read* 操作に対する RBAC アクセス許可が必要になります。 
- 既定では、このアクセス許可を持つのはサブスクリプションの管理者のみです。 

### <a name="scheduled-purge"></a>スケジュールされた消去

削除されたキー コンテナー オブジェクトの一覧には、Key Vault による消去の実行スケジュールも表示されます。 *Scheduled Purge Date* は、何のアクションも実行しない場合に、そのキー コンテナー オブジェクトが永続的に削除されるタイミングを示します。 既定では、削除されたキー コンテナー オブジェクトのリテンション期間は 90 日です。

>[!IMPORTANT]
>*Scheduled Purge Date* フィールドでトリガーされた消去済みのコンテナー オブジェクトは永続的に削除されます。 これは復旧できません。

## <a name="other-resources"></a>その他のリソース

- Key Vault の論理的な削除機能の概要については、「[Azure Key Vault の論理的な削除機能の概要](key-vault-ovw-soft-delete.md)」をご覧ください。
- Azure Key Vault の使用方法の概要については、「[Azure Key Vault の概要](key-vault-get-started.md)」をご覧ください。

