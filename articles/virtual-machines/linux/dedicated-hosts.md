---
title: 仮想マシン用 Azure 専用ホストの概要
description: Azure 専用ホストを使用して仮想マシンをデプロイする方法の詳細について説明します。
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: ddd08f87fb7847a81b28b18c1dedc1c4f0a7a169
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246208"
---
# <a name="azure-dedicated-hosts"></a>Azure 専用ホスト

Azure 専用ホストは、1 つの Azure サブスクリプションに対して専用の物理サーバーを提供するサービスであり、1 つ以上の仮想マシンをホストできます。 専用ホストは、データ センターで使用されるものと同じ物理サーバーであり、リソースとして提供されます。 リージョン、可用性ゾーン、障害ドメイン内に専用ホストをプロビジョニングできます。 次に、プロビジョニングされたホストに VM を直接配置し、ニーズに最も合った構成にすることができます。



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>次のステップ

- [Azure CLI](dedicated-hosts-cli.md)、[ポータル](dedicated-hosts-portal.md)、[PowerShell](../windows/dedicated-hosts-powershell.md) を使用して専用ホストをデプロイできます。

- 詳細については、[専用ホスト](dedicated-hosts.md)の概要に関するページを参照してください。

- [こちら](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)には、リージョン内の回復性を最大にするためにゾーンと障害ドメインの両方を使用するサンプル テンプレートがあります。

- また、Azure Dedicated Host](../prepay-dedicated-hosts-reserved-instances.md)の [予約インスタンスを使用してコストを節約することもできます。
