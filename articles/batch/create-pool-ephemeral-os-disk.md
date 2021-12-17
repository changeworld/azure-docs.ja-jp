---
title: エフェメラル OS ディスク ノードを Azure Batch プールに使用する
description: エフェメラル OS ディスク ノードが使用される Batch プールを作成する方法と理由を説明します。
ms.topic: how-to
ms.date: 09/03/2021
ms.openlocfilehash: 760e2848917c6b1c502ac6ba9aae00d5ddd89c8c
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544379"
---
# <a name="use-ephemeral-os-disk-nodes-for-azure-batch-pools"></a>エフェメラル OS ディスク ノードを Azure Batch プールに使用する

Azure 仮想マシン (VM) シリーズには、[エフェメラル OS ディスク](../virtual-machines/ephemeral-os-disks.md)の使用がサポートされているものがあります。これは、ノードの仮想マシンのローカル ストレージ上に OS ディスクを作成します。 既定の Batch プール構成では、ノード OS ディスクに [Azure マネージド ディスク](../virtual-machines/managed-disks-overview.md)を使用します。ここで、マネージド ディスクは物理ディスクのようですが、仮想化されてリモートの Azure Storage で保持されます。

Batch ワークロードの場合、エフェメラル OS ディスクを使用すると、プールに関連するコストの削減、ノードの起動時間を短縮できる可能性、OS ディスクのパフォーマンス向上によるアプリケーション パフォーマンスの改善という主なメリットがあります。 エフェメラル OS ディスクをご自分のワークロードに使用すべきかどうかを判断する際は、以下を考慮してください。

- エフェメラル OS ディスクへの読み取り/書き込みの待ち時間は短いため、アプリケーション パフォーマンスが向上する可能性があります。
- マネージド OS ディスクではそれぞれコストが発生しますが、エフェメラル OS ディスクではストレージ コストが発生しません。
- Batch でサポートされている場合、マネージド ディスクよりもエフェメラル ディスクの方がノードを速く再イメージ化できます。
- エフェメラル OS ディスクが使用されると、ノードの起動時間がわずかに短くなる可能性があります。
- エフェメラル OS ディスクは、永続性と可用性がそれほど高くありません。何らかの理由で VM が削除されると、OS ディスクは失われます。 Batch ワークロードは本質的にステートレスであり、保持されている OS ディスクへの変更に通常依存しないため、エフェメラル OS ディスクはほとんどの Batch ワークロードでの使用に適しています。
- エフェメラル OS ディスクの使用は現在、一部の Azure VM シリーズではサポートされていません。 VM でエフェメラル OS ディスクがサポートされていない場合は、マネージド OS ディスクを使用する必要があります。

> [!NOTE]
> エフェメラル OS ディスクの構成は、"virtualMachineConfiguration" プールにのみ適用され、"cloudServiceConfiguration" プールではサポートされていません。 "cloudServiceConfiguration" プールではすべての機能がサポートされておらず、新しい機能が計画されていないため、Batch プールには "virtualMachineConfiguration" を使用することをお勧めます。 [2024 年 2 月 29 日以降](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/)、新しい 'cloudServiceConfiguration' プールを作成したり、既存のプールに新しいノードを追加したりすることはできなくなります。 詳細については、「[Batch プールの構成を Cloud Services から仮想マシンに移行する](batch-pool-cloud-service-to-virtual-machine-configuration.md)」を参照してください。

## <a name="vm-series-support"></a>VM シリーズのサポート

ある VM シリーズでエフェメラル OS ディスクがサポートされているかどうかを判断するには、各 VM インスタンスに関するドキュメントをチェックします。 たとえば、[Ddv4 と Ddsv4 のシリーズ](../virtual-machines/ddv4-ddsv4-series.md)では、エフェメラル OS ディスクがサポートされています。

または、プログラムでクエリを実行して、'EphemeralOSDiskSupported' 機能をチェックできます。 この機能のクエリを実行する PowerShell コマンドレットのサンプルは、[エフェメラル OS ディスクについてよく寄せられる質問](../virtual-machines/ephemeral-os-disks.md#frequently-asked-questions)で提供されています。

## <a name="create-a-pool-that-uses-ephemeral-os-disks"></a>エフェメラル OS ディスクを使用するプールの作成

`EphemeralOSDiskSettings` プロパティは、既定では設定されていません。 プール ノードでのエフェメラル OS ディスクの使用を構成するには、このプロパティを設定する必要があります。

次の例は、マネージド ディスクではなくエフェメラル OS ディスクがノードで使用される Batch プールを作成する方法を示しています。

### <a name="batch-net-api"></a>Batch .NET API

```csharp
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: nodeAgentSku
        );
virtualMachineConfiguration.OSDisk = new OSDisk();
virtualMachineConfiguration.OSDisk.EphemeralOSDiskSettings = new DiffDiskSettings();
virtualMachineConfiguration.OSDisk.EphemeralOSDiskSettings.Placement = DiffDiskPlacement.CacheDisk;
```

## <a name="next-steps"></a>次のステップ

- [Batch サービスのワークフローと主要なリソース](batch-service-workflow-features.md) (プール、ノード、ジョブ、タスクなど) について学習します。
- [Azure Batch ワークロードに伴う可能性があるコスト](budget.md)について学習します。
