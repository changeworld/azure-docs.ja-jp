---
title: Azure Automanage 仮想マシンのリージョン間での移動
description: Azure Automanage 仮想マシンをリージョン間で移動する方法について説明します。
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/05/2021
ms.author: alsin
ms.custom: subject-moving-resources
ms.openlocfilehash: 7e880992143bf79a5a99cc9830957f83167b6b46
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808061"
---
# <a name="move-an-azure-automanage-virtual-machine-to-a-different-region"></a>Azure Automanage 仮想マシンの別のリージョンへの移動
この記事では、仮想マシン (VM) を別のリージョンに移動するときに、Automanage を有効にしたままにする方法について説明します。 さまざまな理由で、お使いの仮想マシンを別のリージョンに移動することが必要な場合があります。 たとえば、新しい Azure リージョンを利用するため、内部ポリシーとガバナンスの要件を満たすため、または容量計画の要件に応じるためです。 移動する VM を現在 Automanage で管理しており、移動後も Automanage で管理し続けたいと考えている場合です。

## <a name="prerequisites"></a>前提条件
* お使いのターゲット リージョンで [Automanage がサポート](./automanage-virtual-machines.md#prerequisites)されていることを確認します。
* お使いの Log Analytics ワークスペースのリージョン、Automation アカウントのリージョン、およびお使いのターゲット リージョンのすべてのリージョンが、[こちらの](https://docs.microsoft.com/azure/automation/how-to/region-mappings)リージョン マッピングでサポートされていることを確認します。

## <a name="prepare-your-automanaged-vms-for-moving"></a>自分の Automanage で管理している VM の移動準備
自分の Automanage で管理している VM で Automanage を無効にします。 これを行うには、[Automanage] ブレードでお使いの VM を選択し、[Automanage] ブレードで **[Disable automanagement]** \(自動管理を無効にする\) クリックします。

## <a name="move-your-automanaged-vms-and-re-enable-automanage"></a>自分の Automanage で管理している VM の移動と、Automanage の再有効化
お使いの VM を移動する方法の詳細については、この[記事](https://docs.microsoft.com/azure/resource-mover/tutorial-move-region-virtual-machines)を参照してください。

お使いの VM をリージョンを越えて移動したら、再びそれらで Automanage を有効にできます。 詳細については、[こちら](./automanage-virtual-machines.md#enabling-automanage-for-vms-in-azure-portal)をご覧ください。

## <a name="next-steps"></a>次のステップ
* [Azure Automanage の詳細情報](./automanage-virtual-machines.md)
* [Azure Automanage のよく寄せられる質問を参照](./faq.md)します