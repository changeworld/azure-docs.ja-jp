---
title: Batch プールでマネージド ID を構成する
description: Batch プールでユーザー割り当てマネージド ID を有効にする方法と、ノード内でマネージド ID を使用する方法について説明します。
ms.topic: conceptual
ms.date: 02/10/2021
ms.custom: references_regions
ms.openlocfilehash: 4a59383d119f88bb3ee180f629ba0a6ea6ac2f44
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416670"
---
# <a name="configure-managed-identities-in-batch-pools"></a>Batch プールでマネージド ID を構成する

[Azure リソース用のマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用すると、Azure Active Directory (Azure AD) 内の Azure リソースの ID を指定し、それを使用して Azure Active Directory (Azure AD) トークンを取得することにより、開発者は資格情報を管理する必要がなくなります。

このトピックでは、Batch プールでユーザー割り当てマネージド ID を有効にする方法と、ノード内でマネージド ID を使用する方法について説明します。

> [!IMPORTANT]
> ユーザー割り当てマネージド ID を使用する Azure Batch プールに対するサポートは、現在、次のリージョンを対象にパブリック プレビュー段階にあります。米国西部 2、米国中南部、米国東部、US Gov アリゾナ、US Gov バージニア。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="create-a-user-assigned-identity"></a>ユーザー割り当て ID を作成する

まず、[ユーザー割り当てマネージド ID](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) を Batch アカウントと同じテナントに作成します。 このマネージド ID は、同じリソース グループ内または同じサブスクリプション内に存在する必要はありません。

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID を使用して Batch プールを作成する

1 つ以上のユーザー割り当てマネージド ID を作成したら、[Batch .NET 管理ライブラリ](/dotnet/api/overview/azure/batch#management-library)を使用して、そのマネージド ID で Batch プールを作成できます。

> [!IMPORTANT]
> マネージド ID を使用するには、[仮想マシンの構成](nodes-and-pools.md#virtual-machine-configuration)を使用してプールを構成する必要があります。

```csharp
var poolParameters = new Pool(name: "yourPoolName")
    {
        VmSize = "standard_d1_v2",
        ScaleSettings = new ScaleSettings
        {
            FixedScale = new FixedScaleSettings
            {
                TargetDedicatedNodes = 1
            }
        },
        DeploymentConfiguration = new DeploymentConfiguration
        {
            VirtualMachineConfiguration = new VirtualMachineConfiguration(
                new ImageReference(
                    "Canonical",
                    "UbuntuServer",
                    "18.04-LTS",
                    "latest"),
                "batch.node.ubuntu 18.04")
        };
        Identity = new BatchPoolIdentity
        {
            Type = PoolIdentityType.UserAssigned,
            UserAssignedIdentities = new Dictionary<string, BatchPoolIdentityUserAssignedIdentitiesValue>
            {
                ["Your Identity Resource Id"] =
                    new BatchPoolIdentityUserAssignedIdentitiesValue()
            }
        }
    };

var pool = await managementClient.Pool.CreateWithHttpMessagesAsync(
    poolName:"yourPoolName",
    resourceGroupName: "yourResourceGroupName",
    accountName: "yourAccountName",
    parameters: poolParameters,
    cancellationToken: default(CancellationToken)).ConfigureAwait(false);    
```

> [!NOTE]
> マネージド ID を使用したプールの作成は、現在、[Batch .NET クライアント ライブラリ](/dotnet/api/overview/azure/batch#client-library)ではサポートされていません。

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>Batch ノードでユーザー割り当てマネージド ID を使用する

プールを作成すると、ユーザー割り当てマネージド ID は、Secure Shell (SSH) またはリモート デスクトップ (RDP) 経由でプール ノードにアクセスできるようになります。 そのマネージド ID で、[マネージド ID がサポートされている Azure リソース](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)に直接アクセスできるように、タスクを構成することもできます。

Batch ノード内では、[Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md) を介してマネージド ID トークンを取得し、それらを使用して Azure AD 認証によって認証を行うことができます。

Windows の場合、認証するアクセス トークンを取得する PowerShell スクリプトは次のとおりです。

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -Method GET -Headers @{Metadata="true"} 
```

Linux の場合、Bash スクリプトは次のようになります。

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -H Metadata:true
```

詳細については、「[Azure VM 上で Azure リソースのマネージド ID を使用してアクセス トークンを取得する方法](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure リソース用マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) 詳細について説明します。
- [ユーザー マネージド ID でカスタマー マネージド キー](batch-customer-managed-key.md)を使用する方法について説明します。
