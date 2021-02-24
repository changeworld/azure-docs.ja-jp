---
title: Batch プールの構成を Cloud Services から Virtual Machines に移行する
description: プールの構成を最新の推奨構成に更新する方法について説明します
ms.topic: how-to
ms.date: 1/6/2021
ms.openlocfilehash: 417738be2c69101129079b8ff3a3d80634f9f99c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731501"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>Batch プールの構成を Cloud Services から Virtual Machines に移行する

Batch プールは、[cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) または [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) を使用して作成できます。 "virtualMachineConfiguration" は、すべての Batch 機能をサポートするため、推奨される構成です。 "cloudServiceConfiguration" プールでは、すべての機能はサポートされず、新しい機能は計画されていません。

"cloudServiceConfiguration" プールを使用している場合は、"virtualMachineConfiguration" プールを使用するように移行することを強くお勧めします。 これにより、拡大された [VM シリーズの選択](batch-pool-vm-sizes.md)、Linux VM、[コンテナー](batch-docker-container-workloads.md)、[Azure Resource Manager 仮想ネットワーク](batch-virtual-network.md)、および[ノード ディスクの暗号化](disk-encryption.md)など、すべての Batch 機能を利用できるようになります。

この記事では、"virtualMachineConfiguration" に移行する方法について説明します。

## <a name="new-pools-are-required"></a>新しいプールが必要

既存のアクティブなプールを "cloudServiceConfiguration" から "virtualMachineConfiguration" に更新することはできません。新しいプールを作成する必要があります。 "virtualMachineConfiguration" を使用したプールの作成は、すべての Batch API、コマンドライン ツール、Azure portal、および Batch Explorer UI でサポートされています。

**[.NET](tutorial-parallel-dotnet.md) と [Python](tutorial-parallel-python.md) のチュートリアルでは、"virtualMachineConfiguration" を使用してプールを作成する例を紹介しています。**

## <a name="pool-configuration-differences"></a>プールの構成の違い

プール構成を更新するときは、次の事項を考慮する必要があります。

- "cloudServiceConfiguration" プール ノードは常に Windows OS です。"virtualMachineConfiguration" プールは、Linux と Windows OS のいずれにもできます。
- "cloudServiceConfiguration" プールと比較すると、"virtualMachineConfiguration" プールには、コンテナー サポート、データ　ディスク、ディスク暗号化など、豊富な機能セットが用意されています。
- "virtualMachineConfiguration" プール ノードでは、マネージド OS ディスクが使用されます。 各ノードに使用される[マネージド ディスクの種類](../virtual-machines/disks-types.md)は、プールに対して選択されている VM サイズによって決まります。 "Standard_D2s_v3" など、プールに "s バージョン" の VM サイズが指定されている場合は、Premium SSD が使用されます。 "Standard_D2_v3" など、"s バージョン以外" の VM サイズが指定されている場合は、標準の HDD が使用されます。

   > [!IMPORTANT]
   > Virtual Machines や Virtual Machine Scale Sets と同様に、各ノードで使用される OS マネージド ディスクにはコストがかかります。これは、VM のコストに追加されます。 "cloudServiceConfiguration" ノードでは、ノードのローカル SSD に OS ディスクが作成されるため、OS ディスクのコストは発生しません。

- プールとノードのスタートアップと削除の時間は、"cloudServiceConfiguration" プールと "virtualMachineConfiguration" プールの間で若干異なる場合があります。

## <a name="next-steps"></a>次のステップ

- [プール構成](nodes-and-pools.md#configurations)について学習します。
- [プールのベスト プラクティス](best-practices.md#pools)について学習します。
- [プールの追加](/rest/api/batchservice/pool/add)および[virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) の REST API リファレンス。