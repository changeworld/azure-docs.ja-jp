---
title: クラシックから Azure Resource Manager への移行に関する技術的な詳細
description: プラットフォームでサポートされている、クラシック デプロイ モデルから Azure Resource Manager へのリソースの移行に関する技術的な詳細
services: virtual-machines-linux
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 29267453-f894-4180-bb67-dce2a0e062bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 41e8ae05e02fa4cd97a4c3338a4292611fb26649
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122013"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細

Azure クラシック デプロイ モデルから、Azure Resource Manager デプロイ モデルへの移行を詳しく見ていきましょう。 Azure Platform 上の 2 つのデプロイメント モデルの間で、どのようにリソースが移行されるかを理解できるように、リソースと機能レベルでリソースについて説明していきます。 詳細については、次のサービス告知記事を参照してください。「[プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」。

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>次のステップ

* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行の概要](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行計画](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行を支援するコミュニティ ツール](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Review most common migration errors](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (移行の一般的なエラーを確認する)
* [クラシックから Azure Resource Manager への IaaS リソースの移行に関してよく寄せられる質問を確認する](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
