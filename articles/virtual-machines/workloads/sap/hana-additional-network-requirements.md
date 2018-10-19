---
title: SAP HANA on Azure (L インスタンス) 向け追加のネットワーク要件 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) 向け追加のネットワーク要件
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ee89553846309bd96f3cad3648e337488cf07d3
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167630"
---
# <a name="additional-network-requirements"></a>追加のネットワーク要件

HANA L インスタンスの配備の一環として、追加のネットワーク要件があることもあります。 この記事では、次のネットワーク要件について説明します。
- [IP アドレスまたはサブネットの追加](#adding-more-ip-addresses-or-subnets)
- [仮想ネットワークの追加](#adding-vnets)
- [ExpressRoute 回線の帯域幅の増加](#increasing-expressroute-circuit-bandwidth)
- [ExpressRoute 回線の追加](#adding-an-additional-expressroute-circuit)
- [サブネットの削除](#deleting-a-subnet)
- [仮想ネットワークの削除](#deleting-a-vnet)
- [ExpressRoute 回線の削除](#deleting-an-expressroute-circuit)


## <a name="adding-more-ip-addresses-or-subnets"></a>IP アドレスまたはサブネットの追加

IP アドレスまたはサブネットを追加するには、Azure Portal、PowerShell、CLI のいずれかを使用します。

この場合、集約した範囲を新たに作成するのではなく、新しい IP アドレス範囲を新しい範囲として VNet アドレス空間に追加することをお勧めします。 どちらにしても、新しい IP アドレス範囲のクライアントから HANA L インスタンス ユニットへの接続を許可するためには、この変更を Microsoft に提出する必要があります。 追加する新しい VNet アドレス空間を取得するために、Azure サポート要求を開くことができます。 確認通知を受信したら、次の手順を実行します。

追加のサブネットを Azure Portal で作成するには、「[Azure Portal を使用した仮想ネットワークの作成](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)」を参照してください。PowerShell で作成するには、「[PowerShell を使用した仮想ネットワークの作成](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)」を参照してください。

## <a name="adding-vnets"></a>VNet の追加

最初に 1 つ以上の Azure VNet を接続した後、SAP HANA on Azure (L インスタンス) にアクセスする追加の Azure VNet を作成することができます。 最初に Azure サポート要求を送信します。この要求には、特定の Azure デプロイを識別するための固有の情報と、Azure VNet アドレス空間の IP アドレス範囲を含めます。 その後、SAP HANA on Azure サービス管理から、追加の VNet および ExpressRoute を接続するために必要な情報が提供されます。 ExpressRoute 回線を HANA L インスタンスに接続するためには、すべての VNet に一意の承認キーが必要です。

新しい Azure VNet を追加する手順:

1. オンボード プロセスの最初の手順を完了します。_Azure VNet の作成_に関するセクションを参照してください。
2. オンボード プロセスの 2 番目の手順を完了します。_ゲートウェイ サブネットの作成_に関するセクションを参照してください。
3. 追加の VNet を HANA L インスタンスの ExpressRoute 回線に接続するには、新しい VNet の情報で Azure サポート要求を開き、新しい承認キーを要求します。
4. 承認の完了通知を受け取った後、Microsoft から提供された承認情報を使用して、オンボード プロセスの 3 番目の手順を完了します。「_VNet のリンク_」セクションを参照してください。

## <a name="increasing-expressroute-circuit-bandwidth"></a>ExpressRoute 回線の帯域幅の増加

SAP HANA on Azure サービス管理に問い合わせてください。 SAP HANA on Azure (L インスタンス) の ExpressRoute 回線の帯域幅を増やすよう推奨された場合は、Azure サポート要求を作成します  (1 つの回線につき、帯域幅を最大 10 Gbps 増やすよう要求することができます)。作業が完了すると通知が届きます。その他の作業は不要です。これで Azure が高速化されます。

## <a name="adding-an-additional-expressroute-circuit"></a>ExpressRoute 回線の追加

SAP HANA on Azure サービス管理に問い合わせてください。ExpressRoute 回線の追加を推奨された場合は、Azure サポート要求を作成して新しい ExpressRoute 回線の作成を要求します (また、それに接続するための承認情報も取得します)。 VNet で使用されるアドレス空間は、SAP HANA on Azure サービス管理での承認に必要となるため、要求を作成する前に定義しておく必要があります。

新しい回線の作成と、SAP HANA on Azure サービス管理の構成が完了した後、作業の続行に必要な情報が記載された通知が届きます。 上の手順に従って新しい VNet を作成し、この追加の回線に接続します。 既に同じ Azure リージョン内の別の SAP HANA on Azure (L インスタンス) ExpressRoute 回線に接続されている Azure VNet は、この追加の回線には接続できません。

## <a name="deleting-a-subnet"></a>サブネットの削除

VNet のサブネットを削除するには、Azure Portal、PowerShell、CLI のいずれかを使用することができます。 Azure VNet の IP アドレス範囲/Azure VNet アドレス空間の範囲が集約されている場合、Microsoft のフォローアップはありません。 ただし、削除したサブネットを含む BGP ルート アドレス空間が依然として VNet から伝播されている場合は除きます。 Azure VNet IP アドレス範囲/Azure VNet アドレス空間を複数の IP アドレス範囲として定義した場合で、かつそのいずれかが、削除したサブネットに割り当てられていた場合、そのアドレス範囲を SAP HANA on Azure (L インスタンス) の通信可能範囲から削除するには、該当するアドレス範囲を VNet アドレス空間から削除したうえで、SAP HANA on Azure の Service Management に伝える必要があります。

サブネットを削除するには、[サブネットの削除](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet)を参照してください。

## <a name="deleting-a-vnet"></a>VNet の削除

仮想ネットワークを削除するには、[仮想ネットワークの削除](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network)を参照してください。 SAP HANA on Azure Service Management は、SAP HANA on Azure (L インスタンス) の ExpressRoute 回線での既存の承認を取り消し、HANA L インスタンスとの通信に使用される Azure VNet IP アドレス範囲/Azure VNet アドレス空間を削除します。

VNet が削除された後、Azure サポート要求を開き、削除する IP アドレス空間範囲を指定します。

すべてを確実に削除するために、次の項目を削除します。

- ExpressRoute 接続、VNet ゲートウェイ、VNet ゲートウェイのパブリック IP、VNet

## <a name="deleting-an-expressroute-circuit"></a>ExpressRoute 回線の削除

SAP HANA on Azure (L インスタンス) の追加の ExpressRoute 回線を削除するには、SAP HANA on Azure サービス管理で Azure サポート要求を開き、回線の削除を要求します。 Azure サブスクリプション内では、必要に応じて VNet を削除または保持することができます。 ただし、HANA L インスタンスの ExpressRoute 回線と、そのリンク先 VNet ゲートウェイとを結ぶ接続は削除する必要があります。

VNet も削除する場合は、前のセクション「VNet の削除」のガイダンスに従います。

**次のステップ**

- 「[SAP HANA on Azure (L インスタンス) のインストールと構成の方法](hana-installation.md)」を参照してください。
