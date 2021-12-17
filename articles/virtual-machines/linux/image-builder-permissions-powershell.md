---
title: PowerShell を使用して Azure Image Builder サービスのアクセス許可を構成する
description: PowerShell を使用してアクセス許可と特権を含む Azure VM Image Builder サービスの要件を構成する
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/05/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e310a12248ab0f17c66de2561e090125cbec392e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444537"
---
# <a name="configure-azure-image-builder-service-permissions-using-powershell"></a>PowerShell を使用して Azure Image Builder サービスのアクセス許可を構成する

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: フレキシブル スケール セット 

(AIB) に登録すると、ステージング リソース グループ (IT_*) を作成、管理、削除するためのアクセス許可が AIB サービスに付与され、イメージのビルドに必要なリソースを追加する権限が与えられます。 これは、登録が成功したときに、AIB サービス プリンシパル名 (SPN) がサブスクリプションで使用可能になることによって行われます。

Azure VM Image Builder で、マネージド イメージまたは Azure Compute Gallery (旧称 Shared Image Gallery) にイメージを配布できるようにするには、イメージの読み取りと書き込みのアクセス許可を持つ Azure ユーザー割り当て ID を作成する必要があります。 Azure Storage にアクセスする場合は、プライベートまたはパブリック コンテナーを読み取るためのアクセス許可が必要です。

イメージを構築する前にアクセス許可と特権を設定する必要があります。 以下のセクションでは、PowerShell を使用して考えられるシナリオを構成する方法について詳しく説明します。

## <a name="create-an-azure-user-assigned-managed-identity"></a>Azure ユーザー割り当てマネージド ID を作成する

Azure Image Builder では、[Azure ユーザー割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) を作成する必要があります。 Azure Image Builder では、ユーザー割り当てマネージド ID を使用して、イメージの読み取り、イメージの書き込み、および Azure ストレージ アカウントへのアクセスを行います。 サブスクリプションで特定のアクションを実行できるアクセス許可をその ID に付与します。

> [!NOTE]
> 以前に、Azure Image Builder では、Azure Image Builder のサービス プリンシパル名 (SPN) を使用して、イメージ リソース グループへのアクセス許可を付与していました。 SPN の使用は非推奨になる予定です。 代わりにユーザー割り当てマネージド ID を使用してください。

次の例は、Azure ユーザー割り当てマネージド ID を作成する方法を示しています。 プレースホルダーの設定を置き換えて、変数を設定してください。

| 設定 | Description |
|---------|-------------|
| \<Resource group\> | ユーザー割り当てマネージド ID を作成するリソース グループ。 |

```powershell-interactive
## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

$parameters = @{
    Name = 'aibIdentity'
    ResourceGroupName = '<Resource group>'
}
# create identity
New-AzUserAssignedIdentity @parameters
```

Azure ユーザー割り当て ID の詳細については、ID の作成方法に関する [Azure ユーザー割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) に関するドキュメントを参照してください。

## <a name="allow-image-builder-to-distribute-images"></a>Image Builder にイメージの配布を許可する

Azure Image Builder を使用してイメージ (マネージド イメージまたは Azure Compute Gallery) を配布するには、これらのリソース グループにイメージを挿入することを Azure Image Builder サービスに許可する必要があります。 必要なアクセス許可を付与するには、ユーザー割り当てマネージド ID を作成し、イメージが構築されているリソース グループに対するアクセス許可を付与する必要があります。 Azure Image Builder には、サブスクリプション内の他のリソース グループのリソースにアクセスするアクセス許可が **ありません**。 ビルドが失敗しないようにするには、アクセスを許可する明示的なアクションを実行する必要があります。

イメージを配布するために、リソース グループに対する共同作成者アクセス許可をユーザー割り当てマネージド ID に付与する必要はありません。 ただし、ユーザー割り当てマネージド ID には、配布リソース グループに次の Azure `Actions` アクセス許可が必要です。

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

Azure Compute Gallery に配布する場合、さらに次のアクセス許可が必要となります。

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>既存のイメージをカスタマイズするアクセス許可

Azure Image Builder を使用してソース カスタム イメージ (マネージド イメージまたは Azure Compute Gallery) からイメージをビルドするには、これらのリソース グループへのイメージの読み取りを Azure Image Builder サービスに許可する必要があります。 必要なアクセス許可を付与するには、ユーザー割り当てマネージド ID を作成し、イメージが配置されているリソース グループに対するアクセス許可を付与する必要があります。

既存のカスタム イメージから構築する:

```Actions
Microsoft.Compute/galleries/read
```

既存の Azure Compute Gallery バージョンからビルドする:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>VNET 上のイメージをカスタマイズするためのアクセス許可

Azure Image Builder には、サブスクリプションで既存の VNET をデプロイして使用する機能があるため、接続されているリソースへのカスタマイズ アクセスが可能になります。

VM を既存の VNET にデプロイするために、リソース グループに対する共同作成者アクセス許可をユーザー割り当てマネージド ID に付与する必要はありません。 ただし、ユーザー割り当てマネージド ID には、VNET リソース グループに次の Azure `Actions` アクセス許可が必要です。

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Azure ロールの定義を作成する

次の例では、前のセクションで説明したアクションから Azure ロール定義を作成します。 これらの例は、リソース グループ レベルで適用されます。 例が実際の要件に合った細かさかどうかを評価し、テストしてください。 シナリオによっては、特定の Azure Compute Gallery に合わせて調整する必要がある場合があります。

イメージのアクションでは、読み取りと書き込みが許可されています。 環境に適したものを判断します。 たとえば、リソース グループ *example-rg-1* からイメージを読み取り、リソース グループ *example-rg-2* にイメージを書き込むことを Azure Image Builder に許可するロールを作成します。

### <a name="custom-image-azure-role-example"></a>カスタム イメージの Azure ロールの例

次の例では、ソース カスタム イメージを使用および配布する Azure ロールを作成します。 次に、Azure Image Builder のユーザー割り当てマネージド ID にカスタム ロールを付与します。

この例の値の置換を簡単にするために、最初に次の変数を設定します。 プレースホルダーの設定を置き換えて、変数を設定してください。

| 設定 | Description |
|---------|-------------|
| \<Subscription ID\> | Azure サブスクリプション ID |
| \<Resource group\> | カスタム イメージのリソース グループ |

```powershell-interactive
$sub_id = "<Subscription ID>"
# Resource group - image builder will only support creating custom images in the same Resource Group as the source managed image.
$imageResourceGroup = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$role_definition="aibRoleImageCreation.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleImageCreation.json description file. 
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $imageRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $imageResourceGroup
}

New-AzRoleAssignment @parameters
```

### <a name="existing-vnet-azure-role-example"></a>既存の VNET Azure ロールの例

次の例では、既存の VNET イメージを使用および配布する Azure ロールを作成します。 次に、Azure Image Builder のユーザー割り当てマネージド ID にカスタム ロールを付与します。

この例の値の置換を簡単にするために、最初に次の変数を設定します。 プレースホルダーの設定を置き換えて、変数を設定してください。

| 設定 | Description |
|---------|-------------|
| \<Subscription ID\> | Azure サブスクリプション ID |
| \<Resource group\> | VNET リソース グループ |

```powershell-interactive
$sub_id = "<Subscription ID>"
$res_group = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json"
$role_definition="aibRoleNetworking.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same AAD domain
$timeInt=$(get-date -UFormat "%s")
$networkRoleDefName="Azure Image Builder Network Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<vnetRgName>', $res_group) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Networking Role',$networkRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleNetworking.json description file
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Assign the custom role to the user-assigned managed identity for Azure Image Builder
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $networkRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $res_group
}

New-AzRoleAssignment @parameters
```

## <a name="next-steps"></a>次の手順

詳細については、[Azure Image Builder の概要](../image-builder-overview.md)に関する記事を参照してください。
