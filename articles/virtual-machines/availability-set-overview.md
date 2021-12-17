---
title: 可用性セットの概要
description: Azure の可用性セットについて
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 0a3b85f7fe6a11b461e138b920bfb607bb0ebfcc
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129999114"
---
# <a name="availability-sets-overview"></a>可用性セットの概要

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM

この記事では、Azure 仮想マシン (VM) の可用性機能の概要について説明します。

## <a name="what-is-an-availability-set"></a>可用性セットとは 

可用性セットは VM の論理グループで、これによって Azure は、冗長性と可用性を提供するためにアプリケーションが構築された方法を理解することができます。 高可用性アプリケーションを提供し、[99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) に適合するために、1 つの可用性セット内に 2 つ以上の VM を作成することをお勧めします。 可用性セット自体にはコストはかかりません。料金は、作成した各 VM インスタンスに対してのみ発生します。

## <a name="how-do-availability-sets-work"></a>可用性セットのしくみ
基盤となる Azure プラットフォームにより、可用性セット内の各仮想マシンに **更新ドメイン** と **障害ドメイン** が割り当てられます。 各可用性セットは、最大 3 つの障害ドメインと 20 個の更新ドメインで構成できます。 更新ドメインは、仮想マシンと、同時に再起動できる基礎となる物理ハードウェアのグループを示しています。 5 つの更新ドメインを含む 1 つの可用性セット内に 5 つを超える仮想マシンが構成されている場合、6 番目の仮想マシンは最初の仮想マシンと同じ更新ドメインに配置され、7 番目は 2 番目の仮想マシンと同じ更新ドメインに配置され、それ以降も同様になります。 計画的メンテナンス中は、更新ドメインの再起動が順番に処理されない場合がありますが、一度に再起動される更新ドメインは 1 つのみです。 再起動された更新ドメインには、別の更新ドメインでメンテナンスが開始されるまでに、復旧するための時間として 30 分が与えられます。

障害ドメインは電源とネットワーク スイッチを共有する仮想マシンのグループを定義します。 既定で、可用性セット内に構成された仮想マシンは、最大 3 つの障害ドメイン間で分散されます。 仮想マシンを可用性セットに配置しても、アプリケーションがオペレーティング システムやアプリケーションの障害から保護されるわけではありませんが、潜在的な物理ハードウェア障害、ネットワーク障害、または電力の中断の影響を低下させることができます。

:::image type="content" source="./media/virtual-machines-common-manage-availability/ud-fd-configuration.png" alt-text="さまざまなコンピューティング クラスターが障害ドメインに分けられ、それらの障害ドメイン内に複数の更新ドメインがあることを示す図":::

VM もディスクの障害ドメインに合わせて配置されます。 この配置により、1 つの VM に接続されたすべてのマネージド ディスクが同じ障害ドメイン内にあることが保証されます。 

管理対象の可用性セットには、マネージド ディスクを持つ VM だけを作成できます。 マネージド ディスクの障害ドメインの数はリージョンによって異なり、マネージド ディスクの障害ドメインはリージョンあたり 2 つまたは 3 つになります。 

:::image type="content" source="./media/virtual-machines-common-manage-availability/md-fd-updated.png" alt-text="ディスクおよび VM の障害ドメインがどのように配置されているかを示す図。":::

## <a name="next-steps"></a>次のステップ
可用性と冗長性の機能を使って、Azure 環境を構築できるようになりました。 ベスト プラクティスに関する情報については、[Azure の可用性のベスト プラクティス](/azure/architecture/checklist/resiliency-per-service)に関するページをご覧ください。

