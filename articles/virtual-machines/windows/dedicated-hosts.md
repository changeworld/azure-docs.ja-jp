---
title: 仮想マシン用 Azure 専用ホストの概要
description: Azure 専用ホストを使用して仮想マシンをデプロイする方法の詳細について説明します。
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/25/2019
ms.author: cynthn
ms.openlocfilehash: cfcc8aeb3d1cc1f199b19abc16cc54db416a307e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033373"
---
# <a name="preview-azure-dedicated-hosts"></a>プレビュー:Azure 専用ホスト

Azure 専用ホストは、1 つの Azure サブスクリプションに対して専用の物理サーバーを提供するサービスであり、1 つ以上の仮想マシンをホストできます。 専用ホストは、データ センターで使用されるものと同じ物理サーバーであり、リソースとして提供されます。 リージョン、可用性ゾーン、障害ドメイン内に専用ホストをプロビジョニングできます。 次に、プロビジョニングされたホストに VM を直接配置し、ニーズに最も合った構成にすることができます。

[!INCLUDE [virtual-machines-common-dedicated-hosts-preview](../../../includes/virtual-machines-common-dedicated-hosts-preview.md)]

[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>次の手順

- [Azure PowerShell](dedicated-hosts-powershell.md)、[ポータル](dedicated-hosts-portal.md)、[Azure CLI](../linux/dedicated-hosts-cli.md) を使用して専用ホストをデプロイできます。

- [こちら](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)には、リージョン内の回復性を最大にするためにゾーンと障害ドメインの両方を使用するサンプル テンプレートがあります。
