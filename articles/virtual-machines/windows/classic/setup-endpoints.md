---
title: クラシック Windows VM でのエンドポイントのセットアップ | Microsoft Docs
description: Azure の Windows 仮想マシンとの通信を許可するように、Azure Portal でクラシック Windows VM のエンドポイントをセットアップする方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: cca9adb40557cf7bf9e1d4129fc6bd61cbf0df4f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38618241"
---
# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Azure 上でクラシック Windows 仮想マシンにエンドポイントをセットアップする方法
クラシック デプロイ モデルを使用して Azure で作成したすべての Windows 仮想マシンが、プライベート ネットワーク チャネルを介して、同じクラウド サービスまたは仮想ネットワーク内の他の仮想マシンと自動的に通信できます。 ただし、インターネットまたは他の仮想ネットワークにあるコンピューターと通信するには、仮想マシンへの着信ネットワーク トラフィックを転送するエンドポイントが必要になります。 この記事は、 [Linux 仮想マシン](../../linux/classic/setup-endpoints.md) にも利用できます。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

**Resource Manager** デプロイ モデルでは、エンドポイントは**ネットワーク セキュリティ グループ (NSG)** を使用して構成します。 詳しくは、「[Azure Portal を使用して VM への外部アクセスを許可する方法](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」に関する記事をご覧ください。

Azure Portal で Windows 仮想マシンを作成すると、共通エンドポイント (リモート デスクトップや Windows PowerShell リモート処理用のエンドポイントなど) が通常は自動的に作成されます。 必要に応じて、仮想マシンの作成中や作成後に、追加のエンドポイントを構成できます。

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>次の手順
* Azure PowerShell コマンドレットを使用して VM エンドポイントをセットアップするには、「 [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx)」をご覧ください。
* Azure PowerShell コマンドレットを使用してエンドポイントの ACL を管理するには、「 [PowerShell を使用したエンドポイントのアクセス制御リスト (ACL) の管理](../../../virtual-network/virtual-networks-acl-powershell.md)」をご覧ください。
* Resource Manager デプロイ モデルで仮想マシンを作成した場合は、Azure PowerShell を使用して、VM へのトラフィックを制御する[ネットワーク セキュリティ グループを作成](../../../virtual-network/tutorial-filter-network-traffic.md)できます。
