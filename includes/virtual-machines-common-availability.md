---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 06b54c3038e8b4f5879a93b696920534c2199008
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414489"
---
この記事では、Azure 仮想マシン (VM) の可用性機能の概要について説明します。

## <a name="high-availability"></a>高可用性

ワークロードは、通常は複数の仮想マシンに分散されて、高いスループットとパフォーマンスを獲得し、更新やその他のイベントによって VM が影響を受ける場合に冗長性を確保します。 

高可用性を実現するために Azure が提供するオプションはいくつかあります。 まず、基本的な構成要素について説明します。 

### <a name="availability-zones"></a>可用性ゾーン

[可用性ゾーン](../articles/availability-zones/az-overview.md)は、VM 上のアプリケーションとデータの可用性を維持するための制御レベルを拡張します。 可用性ゾーンとは、Azure リージョン内の物理的に独立したゾーンのことです。 サポートされている Azure リージョンごとに 3 つの可用性ゾーンがあります。 

可用性ゾーンはそれぞれ異なる電源、ネットワーク、および冷却装置を持ちます。 複数のゾーンにレプリケートされた VM を使用するソリューションを構築することで、1 つのデータセンターで障害が発生してもアプリケーションとデータを保護することができます。 1 つのゾーンが侵害された場合、レプリケートされたアプリとデータが別のゾーンですぐに利用可能になります。 

![可用性ゾーン](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

可用性ゾーンに [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) または [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) の VM をデプロイする方法の詳細をご覧ください。


### <a name="fault-domains"></a>障害ドメイン

障害ドメインは、オンプレミスのデータ センター内のラックのように、共通の電源やネットワーク スイッチを共有する基盤となるハードウェアの論理グループです。 

### <a name="update-domains"></a>更新ドメイン

更新ドメインは、メンテナンスや再起動が同時に行われる可能性のある、基盤となるハードウェアの論理グループです。 

これにより、Azure プラットフォームに定期メンテナンスを実施している間もアプリケーションのインスタンスが常に 1 つは稼働している状態を確保することができます。 メンテナンス中は更新ドメインの再起動が順番に処理されない場合がありますが、一度に再起動される更新ドメインは 1 つのみです。


## <a name="virtual-machines-scale-sets"></a>Virtual Machines スケール セット 

Azure Virtual Machine Scale Sets では、負荷分散が行われる VM のグループを作成して管理することができます。 需要または定義されたスケジュールに応じて、VM インスタンスの数を自動的に増減させることができます。 スケール セットは、アプリケーションの高可用性を実現します。また、多数の VM の一元的な管理、構成、更新を可能にします。 高可用性アプリケーションを提供し、[99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) に適合するために、1 つのスケール セット内に 2 つ以上の VM を作成することをお勧めします。 スケール セット自体にはコストはかかりません。料金は、作成した各 VM インスタンスに対してのみ発生します。 1 つの VM が [Azure Premium SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) を使用している場合、Azure SLA は計画外メンテナンス イベントに適用されます。 スケール セット内の仮想マシンは、複数の更新ドメインと障害ドメインにデプロイして、データセンターの障害や、計画済みまたは計画外のメンテナンス イベントによる障害に対する可用性と復元性を最大にすることができます。 スケール セット内の仮想マシンは、1 つの可用性ゾーン (リージョン) にデプロイすることもできます。 可用性ゾーンのデプロイ オプションは、オーケストレーション モードによって異なる場合があります。

### <a name="preview-orchestration-mode-preview"></a>プレビュー:オーケストレーション モードのプレビュー
仮想マシン スケール セットでは、オーケストレーション モードを指定できます。  仮想マシン スケール セットのオーケストレーション モード (プレビュー) では、スケール セットがスケール セット構成モデルの外部で明示的に作成された仮想マシンを調整するか、構成モデルに基づいて暗黙的に作成された仮想マシン インスタンスを調整するかを選択できるようになりました。 VM オーケストレーション モデルで明示的に定義された仮想マシンをリージョンまたは可用性ゾーンにグループ化できる、オーケストレーション モードを選択します。 可用性ゾーンにデプロイされた仮想マシンは、VM へのゾーン分離を実現します。これらは、可用性ゾーンの境界にバインドされており、リージョン内の他の可用性ゾーンで発生する可能性があるエラーの対象にはなりません。 

|   | “orchestrationMode”: “VM” (VirtualMachine)| “orchestrationMode”: “ScaleSetVM” (VirtualMachineScaleSetVM) |
|----|----|----|
| VM 構成モデル| なし。 VirtualMachineProfile はスケール セット モデルでは定義されていません。 | 必須。 VirtualMachineProfile はスケール セット モデルに設定されています。 |
| スケール セットへの新しい VM の追加| VM は、VM が作成されるときにスケール セットに明示的に追加されます。 | VM は、VM 構成モデル、インスタンス数、自動スケール規則に基づいて暗黙的に作成され、スケール セットに追加されます。 |
| 可用性ゾーン| 1 つの可用性ゾーンのリージョンのデプロイまたは VM をサポートします| リージョンのデプロイまたは複数の可用性ゾーンをサポートします。ゾーン バランス戦略を定義できます |
| 障害ドメイン| 障害ドメインの数を定義できます。 リージョン サポートに基づく場合は 2 つまたは 3 つ、可用性ゾーンの場合は 5 つです。 割り当てられた VM 障害ドメインは、割り当て解除と再起動を含む VM のライフサイクルで保持されます。 | 非ゾーンのデプロイの場合は 1 つ、2 つ、または 3 つの障害ドメイン、可用性ゾーンのデプロイの場合は 5 つを定義できます。 割り当てられた VM 障害ドメインは VM のライフサイクルで保持されず、仮想マシンには割り当て時に障害ドメインが割り当てられます。 |
| 更新ドメイン| 該当なし。 更新ドメインは自動的に障害ドメインにマップされます| 該当なし。 更新ドメインは自動的に障害ドメインにマップされます |

**障害ドメインと更新ドメイン**

仮想マシン スケール セットを使用すると、障害ドメインと更新ドメインを調整することで、高可用性向けの設計が簡単になります。 スケール セットの障害ドメインの数を定義するだけで済みます。 スケール セットで使用できる障害ドメインの数は、リージョンによって異なる場合があります。 「[リージョンごとの障害ドメイン数](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#number-of-fault-domains-per-region)」を参照してください。


## <a name="availability-sets"></a>可用性セット
可用性セットはデータセンター内の VM の論理グループで、これによって Azure は、冗長性と可用性を提供するためにアプリケーションが構築された方法を理解することができます。 高可用性アプリケーションを提供し、[99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) に適合するために、1 つの可用性セット内に 2 つ以上の VM を作成することをお勧めします。 可用性セット自体にはコストはかかりません。料金は、作成した各 VM インスタンスに対してのみ発生します。 1 つの VM が [Azure Premium SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd) を使用している場合、Azure SLA は計画外メンテナンス イベントに適用されます。

可用性セットでは、VM はこれらの障害ドメイン間で自動的に分散されます。 これにより、物理ハードウェアの障害、ネットワークの停止、または停電が発生した場合の影響を抑えることが可能になります。

[Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md) を使用している VM の場合、VM は管理対象の可用性セットを使用している場合にマネージド ディスクの障害ドメインに合わせて配置されます。 この配置により、VM に接続されたすべてのマネージド ディスクは必ず同じマネージド ディスクの障害ドメイン内にあります。 

管理対象の可用性セットには、マネージド ディスクを持つ VM だけを作成できます。 マネージド ディスクの障害ドメインの数はリージョンによって異なり、マネージド ディスクの障害ドメインはリージョンあたり 2 つまたは 3 つになります。 [Linux VM](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) または [Windows VM](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) のこれらのマネージド ディスク障害ドメインの詳細を参照してください。

![可用性セットの管理](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


また、可用性セット内の VM も、更新ドメイン間で自動的に分散されます。 

![可用性セット](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>次の手順
可用性と冗長性の機能を使って、Azure 環境を構築できるようになりました。 ベスト プラクティスに関する情報については、[Azure の可用性のベスト プラクティス](/azure/architecture/checklist/resiliency-per-service)に関するページをご覧ください。

