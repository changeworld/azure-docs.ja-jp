---
title: ディスク暗号化が有効になっているプールを作成する
description: ディスク暗号化構成を使用して、プラットフォーム マネージド キーでノードを暗号化する方法について説明します。
author: pkshultz
ms.topic: how-to
ms.date: 04/16/2021
ms.author: peshultz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 01d2ea03768a09c1ad4e019b9e8ed43a26443637
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728520"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>ディスク暗号化が有効になっているプールを作成する

[仮想マシンの構成](nodes-and-pools.md#virtual-machine-configuration)を使用して Azure Batch プールを作成する場合、ディスク暗号化構成を指定することによって、プラットフォーム マネージド キーでプール内のコンピューティング ノードを暗号化できます。

この記事では、ディスク暗号化が有効になっている Batch プールを作成する方法について説明します。

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>ディスク暗号化構成が指定されたプールを使用する理由

Batch プールを使用すると、コンピューティング ノードの OS と一時ディスクにアクセスしてデータを格納することができます。 プラットフォーム マネージド キーを使用してサーバー側のディスクを暗号化すると、オーバーヘッドを軽減しながら簡単にこのデータを保護できます。

Batch は、プールの構成とリージョンのサポートに基づいて、これらのディスク暗号化テクノロジのいずれかをコンピューティング ノードに適用します。

- [プラットフォーム マネージド キーを使用した保存時のマネージド ディスク暗号化](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [プラットフォーム マネージド キーを使用したホストでの暗号化](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)

プール内のノードに適用される暗号化方法を指定することはできません。 代わりに、ノードで暗号化するターゲット ディスクを指定すると、Batch は適切な暗号化方法を選択して、指定されたディスクがコンピューティング ノード上で確実に暗号化されるようにします。

> [!IMPORTANT]
> [カスタム イメージ](batch-sig-images.md)でプールを作成する場合は、Windows VM を使用している場合にのみ、ディスク暗号化を有効にすることができます。

## <a name="azure-portal"></a>Azure portal

Azure portal で Batch プールを作成する場合は、 **[Disk Encryption Configuration]\(ディスク暗号化構成\)** で **[TemporaryDisk]** または **[OsAndTemporaryDisk]** を選択します。

:::image type="content" source="media/disk-encryption/portal-view.png" alt-text="Azure portal のディスク暗号化構成オプションのスクリーンショット。":::

プールが作成されると、プールの **[プロパティ]** セクションに、ディスク暗号化構成ターゲットが表示されます。

:::image type="content" source="media/disk-encryption/configuration-target.png" alt-text="Azure portal のディスク暗号化構成ターゲットを示すスクリーンショット。":::

## <a name="examples"></a>例

次の例は、Batch .NET SDK、Batch REST API、および Azure CLI を使用して、Batch プールの OS と一時ディスクを暗号化する方法を示しています。

### <a name="batch-net-sdk"></a>Batch .NET SDK

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>Batch REST API

REST API URL:

```
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

要求本文:

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS"
        },
        "diskEncryptionConfiguration": {
            "targets": [
                "OsDisk",
                "TemporaryDisk"
            ]
        }
        "nodeAgentSKUId": "batch.node.ubuntu 18.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "taskSlotsPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az batch pool create \
    --id diskencryptionPool \
    --vm-size Standard_DS1_V2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:18.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 18.04" \
    --disk-encryption-targets OsDisk TemporaryDisk
```

## <a name="next-steps"></a>次のステップ

- [Azure Disk Storage のサーバー側暗号化](../virtual-machines/disk-encryption.md)の詳細について学習します。
- Batch の詳細については、「[Batch サービスのワークフローとリソース](batch-service-workflow-features.md)」を参照してください。
