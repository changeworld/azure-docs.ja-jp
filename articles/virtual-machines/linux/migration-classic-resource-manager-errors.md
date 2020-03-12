---
title: クラシックから Azure Resource Manager への移行中に発生する一般的なエラー
description: この記事では、Azure サービス管理から Azure Resource Manager スタックへの IaaS リソースの移行中に発生する一般的なエラーと対応策を一覧で示しています。
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: ffeef74179874a0f477ba857ca519ebc67fded64
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944718"
---
# <a name="common-errors-during-classic-to-azure-resource-manager-migration"></a>クラシックから Azure Resource Manager への移行中に発生する一般的なエラー

> [!IMPORTANT]
> 現在、IaaS VM の約 90% が [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) を使用しています。 2020 年 2 月 28 日の時点で、クラシック VM は非推奨とされており、2023 年 3 月 1 日に完全に廃止されます。 この非推奨の[詳細]( https://aka.ms/classicvmretirement)および[それが与える影響](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)について確認してください。

[!INCLUDE [virtual-machines-common-migration-errors](../../../includes/virtual-machines-common-classic-resource-manager-migration-common-errors.md)]

## <a name="next-steps"></a>次のステップ

* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行の概要](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行計画](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行を支援するコミュニティ ツール](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行に関してよく寄せられる質問を確認する](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
