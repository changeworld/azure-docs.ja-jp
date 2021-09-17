---
title: Batch プールでマネージド ID を構成する
description: Batch プールでユーザー割り当てマネージド ID を有効にする方法と、ノード内でマネージド ID を使用する方法について説明します。
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 903e173a6028e6bb574dfba618661da802702c2d
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444805"
---
# <a name="configure-managed-identities-in-batch-pools"></a>Batch プールでマネージド ID を構成する

[Azure リソース用のマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用すると、Azure Active Directory (Azure AD) 内の Azure リソースの ID を指定し、それを使用して Azure Active Directory (Azure AD) トークンを取得することにより、開発者は資格情報を管理する必要がなくなります。

このトピックでは、Batch プールでユーザー割り当てマネージド ID を有効にする方法と、ノード内でマネージド ID を使用する方法について説明します。

> [!IMPORTANT]
> マネージド ID を使用するには、[仮想マシンの構成](nodes-and-pools.md#virtual-machine-configuration)を使用してプールを構成する必要があります。
>
> マネージド ID を使用したプールの作成は、[Batch .NET 管理ライブラリ](/dotnet/api/overview/azure/batch#management-library)を使って実行できますが、現在、[Batch .NET クライアント ライブラリ](/dotnet/api/overview/azure/batch#client-library)ではサポートされていません。

## <a name="create-a-user-assigned-identity"></a>ユーザー割り当て ID を作成する

まず、[ユーザー割り当てマネージド ID](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) を Batch アカウントと同じテナントに作成します。 このマネージド ID は、同じリソース グループ内または同じサブスクリプション内に存在する必要はありません。

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID を使用して Batch プールを作成する

1 つ以上のユーザー割り当てマネージド ID を作成したら、[Batch .NET 管理ライブラリ](/dotnet/api/overview/azure/batch#management-library)を使用して、そのマネージド ID で Batch プールを作成できます。

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
        },
        Identity = new BatchPoolIdentity
        {
            Type = PoolIdentityType.UserAssigned,
            UserAssignedIdentities = new Dictionary<string, UserAssignedIdentities>
            {
                ["Your Identity Resource Id"] =
                    new UserAssignedIdentities()
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

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>Batch ノードでユーザー割り当てマネージド ID を使用する

Azure Storage や Azure Container Registry など、他の Azure リソースにアクセスする多くの Azure Batch テクノロジでは、マネージド ID がサポートされています。 Azure Batch でのマネージド ID の使用の詳細については、次のリンクを参照してください。

- [リソース ファイル](resource-files.md)
- [出力ファイル](batch-task-output-files.md#specify-output-files-using-managed-identity)
- [Azure Container Registry](batch-docker-container-workloads.md#managed-identity-support-for-acr)
- [Azure BLOB コンテナー ファイル システム](virtual-file-mount.md#azure-blob-container)

また、マネージド ID が、[マネージド ID をサポートしている Azure リソース](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)に直接アクセスできるように、タスクを手動で構成することもできます。

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
- [Batch プールで証明書の自動ローテーションを有効](automatic-certificate-rotation.md)にする方法について説明します。
