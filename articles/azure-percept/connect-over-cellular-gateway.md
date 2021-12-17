---
title: ゲートウェイを使用して 5G および LTE ネットワーク経由で Azure Percept DK を接続する
description: この記事では、ゲートウェイを使用して 5G または LTE ネットワークで Azure Percept DK を接続する方法について説明します。
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 5b66885afdec8e32b938c735625bd48c9c62535a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439980"
---
# <a name="connect-azure-percept-dk-over-5g-and-lte-networks-by-using-a-gateway"></a>ゲートウェイを使用して 5G および LTE ネットワーク経由で Azure Percept DK を接続する

Azure Percept をインターネットに接続する簡単な方法は、5G または LTE 経由でインターネットに接続し、イーサネットポートを提供するゲートウェイを使用する方法です。 この場合、Azure Percept は、5G や LTE で接続されていることを認識しません。 "わかって" いるのは、イーサネット ポートが接続されており、すべてのトラフィックがそのポート経由でルーティングされていることのみです。  


## <a name="overview-of-5g-and-lte-gateway-topology"></a>5G および LTE ゲートウェイ トポロジの概要

次の図は、5G または LTE ゲートウェイを Azure Percept DK (開発キット) と簡単に組み合わせて使用する方法を示しています。

:::image type="Image" source="media/connect-over-cellular/topology-v2.png" alt-text="Azure Percept DK がイーサネット経由で 5G または LTE ゲートウェイに接続する方法を示している図。" lightbox="media/connect-over-cellular/topology-expanded-v2.png":::

## <a name="if-youre-connecting-to-a-5g-or-lte-gateway"></a>5G または LTE ゲートウェイに接続している場合

Azure Percept DK を 5G または LTE ゲートウェイに接続している場合は、次の重要な点を考慮してください。
- 最初にゲートウェイを設定し、SIM 経由で接続を受けていることを確認します。 この順序に従うことで、Azure Percept DK の接続時に問題が見つかった場合のトラブルシューティングが容易になります。
- イーサネット ケーブルの両端がゲートウェイおよび Azure Percept DK に確実に接続されていることを確認します。
- イーサネット経由で Azure Percept DK を接続するための[既定の手順](./how-to-connect-over-ethernet.md)に従います。
- 5G または LTE のプランにクォータが定められている場合は、Azure Percept DK モデルによってクラウドに送信されるデータ量を最適化することをお勧めします。
- 外部から発信された受信トラフィックをブロックする[ファイアウォールが適切に構成](./concept-security-configuration.md)されていることを確認してください。

## <a name="if-youre-connecting-to-the-dev-kit-via-ssh-protocol"></a>SSH プロトコルを使用して開発キットに接続している場合

Secure Shell (SSH) ネットワーク プロトコルを使用して、5G または LTE イーサネット ゲートウェイ経由で開発キットに接続する場合は、次のいずれかのオプションを使用します。
- **開発キットの Wi-Fi アクセス ポイントを使用する**: Wi-Fi が無効になっている場合は、開発キットを再起動して再度有効にすることができます。 そこから開発キットの Wi-Fi アクセス ポイントに接続し、「[Azure Percept DK に SSH で接続する](./how-to-ssh-into-percept-dk.md)」の手順に従います。
- **ローカル エリア ネットワーク (LAN) へのイーサネット接続を使用する**: このオプションでは、開発キットを 5G または LTE ゲートウェイから取り外し、LAN ルーターに接続します。 詳細については、「[Azure Percept DK にイーサネットで接続する](./how-to-connect-over-ethernet.md)」を参照してください。 
- **ゲートウェイのリモート アクセス機能を使用する**: 多くの 5G または LTE ゲートウェイには、SSH 経由でネットワーク上のデバイスに接続するために使用できるリモート アクセス マネージャーが用意されています。 この機能が搭載されているかどうかは、5G または LTE ゲートウェイの製造元にご確認ください。 リモート アクセス マネージャーの例については、[Cradlepoint 5G および LTE ゲートウェイ](https://customer.cradlepoint.com/s/article/NCM-Remote-Connect-LAN-Manager)に関するページを参照してください。
- **開発キットのシリアル ポートを使用する**: Azure Percept DK には、デバイスに直接接続するために使用できるシリアル接続ポートが含まれます。 詳細については、[Azure Percept DK にシリアル ケーブルで接続する](./how-to-connect-to-percept-dk-over-serial.md)方法に関するページを参照してください。

## <a name="next-steps"></a>次の手順
アクセスできる携帯デバイスに応じて、次の 2 つの方法のいずれかを使用して接続できます。

* [USB モデムを使用して接続する](./connect-over-cellular-usb.md)
* [5G または LTE を使用して接続する](./connect-over-cellular.md)
