---
title: Azure VMware Solutions (AVS) - サービス
description: AVS サービスの概要と概念について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d09c8c34093e7d33122f934138ff9fdf4842508e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024956"
---
# <a name="avs-service-overview"></a>AVS サービスの概要

Azure VMware Solution by AVS は、AVS サービスを通じて利用できます。 このサービスを作成することで、ノードの購入、予約、AVS プライベート クラウドの作成が可能になります。 AVS サービスを利用できる各 Azure リージョンに、AVS サービスを作成します。 このサービスでは、Azure VMware Solution by AVS のエッジ ネットワークが定義されます。 VPN、ExpressRoute、AVS プライベート クラウドへのインターネット接続などのサービスは、このエッジ ネットワークによってサポートされます。

## <a name="gateway-subnet"></a>ゲートウェイ サブネット

AVS サービスごとに、その作成先のリージョンに固有のゲートウェイ サブネットが必要となります。 ゲートウェイ サブネットはエッジ ネットワークを作成するときに使用し、/28 CIDR ブロックを必要とします。 ゲートウェイ サブネットのアドレス空間は一意である必要があります。 AVS 環境と通信するネットワークと重複しないようにしてください。 AVS と通信するネットワークとしては、オンプレミスのネットワークや Azure Virtual Network があります。 いったん作成したゲートウェイ サブネットは削除できません。 ゲートウェイ サブネットは、サービスが削除されたときに削除されます。

## <a name="next-steps"></a>次のステップ

* [Azure で AVS サービスを作成する](quickstart-create-cloudsimple-service.md)方法を確認します。
