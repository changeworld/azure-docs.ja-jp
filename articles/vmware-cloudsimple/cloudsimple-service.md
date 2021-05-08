---
title: Azure VMware Solution by CloudSimple - サービス
description: この概要で、CloudSimple サービスについて説明します。 このサービスを作成することで、ノードの購入、予約、プライベート クラウドの作成が可能になります。
author: shortpatti
ms.author: v-patsho
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: acf5180e438d244f2eab791f4a2ebff77df6eaa4
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108182284"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple サービスの概要

Azure VMware Solution by CloudSimple は、CloudSimple サービスを通じて利用できます。  このサービスを作成することで、ノードの購入、予約、プライベート クラウドの作成が可能になります。  CloudSimple サービスは、それが提供されている各 Azure リージョンに作成することになります。 このサービスでは、Azure VMware Solution by CloudSimple のエッジ ネットワークが定義されます。 VPN、ExpressRoute、プライベート クラウドへのインターネット接続などのサービスは、このエッジ ネットワークによってサポートされます。

## <a name="gateway-subnet"></a>ゲートウェイ サブネット

CloudSimple サービスごとに、その作成先のリージョンに固有のゲートウェイ サブネットが必要となります。 ゲートウェイ サブネットはエッジ ネットワークを作成するときに使用し、/28 CIDR ブロックを必要とします。  ゲートウェイ サブネットのアドレス空間は一意である必要があります。 CloudSimple 環境と通信するネットワークと重複しないようにしてください。 CloudSimple と通信するネットワークとしては、オンプレミスのネットワークや Azure Virtual Network があります。  いったん作成したゲートウェイ サブネットは削除できません。  ゲートウェイ サブネットは、サービスが削除されたときに削除されます。

## <a name="next-steps"></a>次の手順

* [Azure で CloudSimple サービスを作成する](quickstart-create-cloudsimple-service.md)方法を学習します。
