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
ms.date: 10/23/2018
ms.author: cynthn
ms.openlocfilehash: 373003eb8be0482ed96d7d11ecd879237f69b47a
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957167"
---
# <a name="set-up-endpoints-on-a-windows-virtual-machine-by-using-the-classic-deployment-model"></a>クラシック デプロイ モデルを使用して、Windows 仮想マシンでエンドポイントを設定する
クラシック デプロイ モデルを使用して Azure で作成した Windows Virtual Machines (VM) では、プライベート ネットワーク チャネルを介して、同じクラウド サービスまたは仮想ネットワーク内の他の VM と自動的に通信することができます。 しかし、インターネットまたは他の仮想ネットワークにあるコンピューターには、VM への着信ネットワーク トラフィックを転送するエンドポイントが必要になります。 

[Linux Virtual Machines](../../linux/classic/setup-endpoints.md) 上でエンドポイントを設定することもできます。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルについて説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。  
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

**Resource Manager** デプロイ モデルでは、エンドポイントは**ネットワーク セキュリティ グループ (NSG)** を使用して構成されます。 詳細については、[Azure portal を使用する VM への外部アクセスの許可](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。

Azure portal で Windows VM を作成すると、共通エンドポイント (リモート デスクトップや Windows PowerShell リモート処理用のエンドポイントなど) が通常は自動的に作成されます。 必要に応じて、あとで追加のエンドポイントを構成できます。

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>次の手順
* Azure PowerShell コマンドレットを使用して VM エンドポイントをセットアップするには、「 [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx)」をご覧ください。
* Azure PowerShell コマンドレットを使用してエンドポイントの ACL を管理するには、「 [PowerShell を使用したエンドポイントのアクセス制御リスト (ACL) の管理](../../../virtual-network/virtual-networks-acl-powershell.md)」をご覧ください。
* Resource Manager デプロイ モデルで仮想マシンを作成した場合は、Azure PowerShell を使用して、VM へのトラフィックを制御する[ネットワーク セキュリティ グループを作成](../../../virtual-network/tutorial-filter-network-traffic.md)できます。
