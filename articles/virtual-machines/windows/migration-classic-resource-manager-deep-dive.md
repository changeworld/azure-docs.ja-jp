---
title: クラシックから Azure Resource Manager への移行に関する技術的な詳細
description: プラットフォームでサポートされている、クラシック デプロイ モデルから Azure Resource Manager へのリソースの移行に関する技術的な詳細。
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 5ef2085f41cca71e835e05bce97f787ad50a7a53
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86508510"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細

> [!IMPORTANT]
> 現在、IaaS VM の約 90% で [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) が使用されています。 2020 年 2 月 28 日の時点で、クラシック VM は非推奨とされており、2023 年 3 月 1 日に完全に廃止されます。 この非推奨について[詳細]( https://aka.ms/classicvmretirement)および[それが与える影響](../classic-vm-deprecation.md#how-does-this-affect-me)について確認してください。

Azure クラシック デプロイ モデルから、Azure Resource Manager デプロイ モデルへの移行を詳しく見ていきましょう。 Azure Platform 上の 2 つのデプロイメント モデルの間で、どのようにリソースが移行されるかを理解できるように、リソースと機能レベルでリソースについて説明していきます。 詳細については、次のサービス告知記事を参照してください。「[プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」。

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>次のステップ

* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行の概要](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行計画](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [VPN Gateway クラシックから Resource Manager への移行](../../vpn-gateway/vpn-gateway-classic-resource-manager-migration.md)
* [クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線および関連する仮想ネットワークの移行](../../expressroute/expressroute-migration-classic-resource-manager.md)
* [クラシックから Azure Resource Manager への IaaS リソースの移行を支援するコミュニティ ツール](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Review most common migration errors](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (移行の一般的なエラーを確認する)
* [クラシックから Azure Resource Manager への IaaS リソースの移行に関してよく寄せられる質問を確認する](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
