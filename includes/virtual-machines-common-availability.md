---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1582f61befb9b6d71adbfda9482175d67874ffb9
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850652"
---
# <a name="availability-options-for-virtual-machines-in-azure"></a>Azure の仮想マシンの可用性オプション
この記事では、Azure 仮想マシン (VM) の可用性機能の概要について説明します。


## <a name="availability-sets"></a>可用性セット
可用性セットはデータセンター内の VM の論理グループで、これによって Azure は、冗長性と可用性を提供するためにアプリケーションが構築された方法を理解することができます。 高可用性アプリケーションを提供し、[99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) に適合するために、1 つの可用性セット内に 2 つ以上の VM を作成することをお勧めします。 可用性セット自体にはコストはかかりません。料金は、作成した各 VM インスタンスに対してのみ発生します。 1 つの VM が [Azure Premium SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd) を使用している場合、Azure SLA は計画外メンテナンス イベントに適用されます。

可用性セットは、ハードウェアの障害から保護する障害ドメイン (FD) と、更新の安全な適用を可能にする更新ドメイン (UD) という 2 つの追加グループで構成されています。 [Linux VM](../articles/virtual-machines/linux/manage-availability.md) または [Windows VM](../articles/virtual-machines/windows/manage-availability.md) の可用性を管理する方法については、詳細をお読みください。

### <a name="fault-domains"></a>障害ドメイン
障害ドメインは、オンプレミスのデータ センター内のラックのように、共通の電源やネットワーク スイッチを共有する基盤となるハードウェアの論理グループです。 可用性セット内に作成した VM は、Azure プラットフォームにより自動で複数の障害ドメインに分散して配布されます。 これにより、物理ハードウェアの障害、ネットワークの停止、または停電が発生した場合の影響を抑えることが可能になります。

### <a name="update-domains"></a>更新ドメイン
更新ドメインは、メンテナンスや再起動が同時に行われる可能性のある、基盤となるハードウェアの論理グループです。 可用性セット内に作成した VM は、Azure プラットフォームにより自動で複数の更新ドメインに分散して配布されます。 これにより、Azure プラットフォームに定期メンテナンスを実施している間もアプリケーションのインスタンスが常に 1 つは稼働している状態を確保することができます。 計画的メンテナンス中は、更新ドメインの再起動が順番に処理されない場合がありますが、一度に再起動される更新ドメインは 1 つのみです。

![可用性セット](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

### <a name="managed-disk-fault-domains"></a>管理ディスクの障害ドメイン
[Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md) を使用している VM の場合、VM は管理対象の可用性セットを使用している場合にマネージド ディスクの障害ドメインに合わせて配置されます。 この配置により、VM に接続されたすべてのマネージド ディスクは必ず同じマネージド ディスクの障害ドメイン内にあります。 管理対象の可用性セットには、マネージド ディスクを持つ VM だけを作成できます。 マネージド ディスクの障害ドメインの数はリージョンによって異なり、マネージド ディスクの障害ドメインはリージョンあたり 2 つまたは 3 つになります。 [Linux VM](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) または [Windows VM](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) のこれらのマネージド ディスク障害ドメインの詳細を参照してください。

![可用性セットの管理](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

## <a name="availability-zones"></a>可用性ゾーン

[可用性ゾーン](../articles/availability-zones/az-overview.md)は可用性セットに代わる機能で、よりきめ細やかな制御により VM 上のアプリケーションとデータの可用性を維持します。 可用性ゾーンとは、Azure リージョンの物理的に独立したゾーンのことです。 サポートされている Azure リージョンごとに 3 つの可用性ゾーンがあります。 可用性ゾーンはそれぞれ異なる電源、ネットワーク、および冷却装置を持ちます。 複数のゾーンにレプリケートされた VM を使用するソリューションを構築することで、1 つのデータセンターで障害が発生してもアプリケーションとデータを保護することができます。 1 つのゾーンが侵害された場合、レプリケートされたアプリとデータが別のゾーンですぐに利用可能になります。 

![可用性ゾーン](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

可用性ゾーンに [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) または [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) の VM をデプロイする方法の詳細をご覧ください。


## <a name="next-steps"></a>次の手順
可用性と冗長性の機能を使って、Azure 環境を構築できるようになりました。 ベスト プラクティスに関する情報については、[Azure の可用性のベスト プラクティス](../articles/best-practices-availability-checklist.md)に関するページをご覧ください。

