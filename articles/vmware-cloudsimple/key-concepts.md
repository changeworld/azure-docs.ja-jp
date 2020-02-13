---
title: Azure VMware Solutions (AVS) の管理に関する主要な概念
description: Azure VMware Solutions (AVS) の管理に関する主要な概念について説明します
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c581ffbba7fbaefc8fbd0ffc7e92350bd69802b8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025233"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-avs"></a>Azure VMware Solutions (AVS) の管理に関する主要な概念

Azure VMware Solutions (AVS) を管理するには、次の概念を理解する必要があります。

* AVS サービス: "Azure VMware Solutions (AVS) - サービス" として表示されます
* AVS ノード: "Azure VMware Solutions (AVS) - ノード" として表示されます
* AVS プライベート クラウド
* サービスのネットワーク
* AVS 仮想マシン: "Azure VMware Solutions (AVS) - 仮想マシン" として表示されます

## <a name="avs-service"></a>AVS サービス

AVS サービスでは、VMware Solutions (AVS) に関連付けられたすべてのリソースの作成と管理を Azure portal から実行できます。 このサービスを使用する予定のすべてのリージョン内にサービス リソースを作成してください。

詳細については、[AVS サービス](cloudsimple-service.md)に関するページをご覧ください。

## <a name="avs-node"></a>AVS ノード

AVS ノードは、ベア メタルのハイパーコンバージド コンピューティングおよびストレージ ホスト専用で、VMware ESXi ハイパーバイザーがデプロイされます。 このノードが VMware vSphere、vCenter、vSAN、および NSX プラットフォームに組み込まれます。 AVS ネットワーク サービスとエッジ ネットワーク サービスも有効化されます。 各ノードは、[AVS プライベート クラウド](cloudsimple-private-cloud.md)を作成するためにプロビジョニングできる、コンピューティングおよびストレージの容量の単位として機能します。 お客様は、AVS サービスを利用できるリージョンでノードをプロビジョニングまたは予約します。

詳細については、[AVS ノード](cloudsimple-node.md)に関するページをご覧ください。

## <a name="avs-private-cloud"></a>AVS プライベート クラウド

AVS プライベート クラウドは、独自の管理ドメイン内の vCenter サーバーによって管理される、分離された VMware スタック環境です。 VMware スタックには、ESXi ホスト、vSphere、vCenter、vSAN、および NSX が含まれます。 スタックは専用ノード (専用の分離されたベア メタル ハードウェア) 上で実行され、vCenter や NSX Manager などのネイティブの VMware ツールを通してユーザーによって使用されます。 専用ノードが Azure の場所にデプロイされ、Azure によって管理されます。 各 AVS プライベート クラウドのセグメント化とセキュリティ保護を行うには、VLAN およびサブネットやファイアウォール テーブルなどのネットワーク サービスを使用します。 オンプレミス環境と Azure ネットワークへの接続は、セキュリティ保護されたプライベート VPN と Azure ExpressRoute 接続を使用して作成されます。

詳細については、[AVS プライベート クラウド](cloudsimple-private-cloud.md)に関するページをご覧ください。

## <a name="service-networking"></a>サービスのネットワーク

AVS サービスでは、AVS サービスがデプロイされているリージョンごとにネットワークが提供されます。 ネットワークは、ルーティングが既定で有効になっている単一の TCP レイヤー 3 アドレス空間です。 このリージョン内に作成されるすべての AVS プライベート クラウドとサブネットでは、追加構成なしで相互通信できます。 VLAN を使って vCenter 上に分散ポート グループを作成します。 次のネットワーク機能を使用して、AVS プライベート クラウド上でワークロード リソースを構成し、セキュリティ保護できます。

* [VLAN とサブネット](cloudsimple-vlans-subnets.md)
* [ファイアウォール テーブル](cloudsimple-firewall-tables.md)
* [VPN ゲートウェイ](cloudsimple-vpn-gateways.md)
* [パブリック IP](cloudsimple-public-ip-address.md)
* [Azure ネットワーク接続](cloudsimple-azure-network-connection.md)

## <a name="avs-virtual-machine"></a>AVS 仮想マシン

AVS サービスでは、Azure portal から VMware 仮想マシンを管理できます。 vSphere 環境の 1 つまたは複数のクラスターまたはリソース プールを、サービスが作成されているサブスクリプションにマップできます。

各項目の詳細情報

* [AVS 仮想マシン](cloudsimple-virtual-machines.md)
* [Azure サブスクリプションのマッピング](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
