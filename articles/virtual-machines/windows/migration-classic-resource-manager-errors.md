---
title: クラシックから Azure Resource Manager への移行中に発生する一般的なエラー
description: IaaS リソースを Azure サービス管理から Azure Resource Manager スタックへ移行する間に発生する一般的なエラーと対応策について説明します。
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: b052951b0d601681239701711a6636156ad4ebf1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320032"
---
# <a name="common-errors-during-classic-to-azure-resource-manager-migration"></a>クラシックから Azure Resource Manager への移行中に発生する一般的なエラー

> [!IMPORTANT]
> 現在、IaaS VM の約 90% で [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) が使用されています。 2020 年 2 月 28 日の時点で、クラシック VM は非推奨とされており、2023 年 3 月 1 日に完全に廃止されます。 この非推奨の[詳細]( https://aka.ms/classicvmretirement)および[それが与える影響](../classic-vm-deprecation.md#how-does-this-affect-me)について確認してください。

[!INCLUDE [virtual-machines-common-migration-errors](../../../includes/virtual-machines-common-classic-resource-manager-migration-common-errors.md)]

## <a name="next-steps"></a>次のステップ

* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行の概要](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](../migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行計画](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行を支援するコミュニティ ツール](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行に関してよく寄せられる質問を確認する](../migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
