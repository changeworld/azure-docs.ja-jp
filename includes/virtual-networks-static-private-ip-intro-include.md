---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 3d0c475cebedf28b41f16fbde312a1aac14ef207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060448"
---
仮想マシン (VM) には、その VM がデプロイされているサブネットに基づいて、指定された範囲のプライベート IP アドレスが自動的に割り当てられます。 VM では、その VM が削除されるまでこのアドレスが保持されます。 Azure は、次に VM に作成されているサブネットから使用可能なプライベート IP アドレスを自動的に割り当てます。 VM に割り当てられているサブネットの特定の IP アドレスが必要な場合は、静的 IP アドレスを割り当てます。
