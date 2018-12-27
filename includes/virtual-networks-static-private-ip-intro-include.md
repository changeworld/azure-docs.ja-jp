---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 5c1caf87f32ddd827b85263ee634d3e15d821124
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2018
ms.locfileid: "52271016"
---
仮想ネットワーク内の IaaS 仮想マシン (VM) と PaaS ロール インスタンスは、接続されているサブネットに基づいて、指定された範囲のプライベート IP アドレスを自動的に受信します。 VM とロール インスタンスは、使用中止されるまで、そのアドレスを保持します。 VM またはロール インスタンスは、PowerShell、Azure CLI、または Azure ポータルから停止することで使用中止されます。 このような場合、VM またはロール インスタンスを再度開始すると、VM またはロール インスタンスは、利用できる IP アドレスを Azure インフラストラクチャから受信しますが、その IP アドレスは前回受信したものと異なっている可能性があります。 ゲスト オペレーティング システムから VM またはロール インスタンスを停止した場合は、IP アドレスが保持されます。  

VM またはロール インスタンスで静的 IP アドレスの保持が必要になることがあります。たとえば、VM で DNS を実行する場合、または VM がドメイン コントローラーになる場合です。 静的プライベート IP アドレスを設定することで、これを行うことができます。

