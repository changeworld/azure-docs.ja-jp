---
title: ゲートウェイを使用して Azure Percept を 5G または LTE ネットワークで接続する
description: この記事では、移動体通信ゲートウェイを使用して 5G または LTE ネットワークで Azure Percept DK を接続する方法について説明します。
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 00596a23e086f801b152e1a3129ecaf7ef44a0df
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007132"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks-using-a-gateway"></a>ゲートウェイを使用して Azure Percept を 5G または LTE ネットワークで接続する
5G または LTE 経由でインターネットに接続し、イーサネットポートを提供するゲートウェイは、Azure Percept をインターネットに接続する簡単な方法です。 この場合、Azure Percept は 5 G または LTE 経由で接続されていることを認識することなく、そのイーサネットポートが接続可能であることを認識しており、すべてのトラフィックをルーティングします。  


## <a name="5glte-gateway-topology-overview"></a>5G/LTE ゲートウェイのトポロジの概要
下の図では、5G/LTE ゲートウェイを Azure Percept DK と簡単にペアリングする方法がわかります。

:::image type="Image" source="media/connect-over-cellular/topology.png" alt-text="この図は、Azure Percept DK がイーサネット経由で 5G/LTE ゲートウェイに接続する方法を示しています。" lightbox="media/connect-over-cellular/topology-expanded.png":::

## <a name="considerations-when-connecting-to-a-5g-or-lte-gateway"></a>5G/LTE ゲートウェイへの接続時の考慮事項
Azure Percept DK を 5G/LTE ゲートウェイに接続するときに考慮する重要ポイントを次に示します。
- 最初にゲートウェイを設定し、SIM 経由で接続を受けていることを確認します。 これにより、Azure Percept DK の接続時に発生した問題を容易にトラブルシューティングできるようになります。
- イーサネット ケーブルの両端をゲートウェイと Azure Percept DK に確実に接続します。
- イーサネット経由で Azure Percept DK を接続するための[既定の手順](./how-to-connect-over-ethernet.md)に従います。
- 5G/LTE プランにクォータがある場合は、Azure Percept DK モデルからクラウドに送信されるデータの量を最適化することをお勧めします。
- 外部から発信された受信トラフィックをブロックする、[適切に構成されたファイアウォール](./concept-security-configuration.md)を確保します。

## <a name="considerations-when-doing-ssh-to-the-devkit"></a>Devkit に SSH 接続する際の考慮事項
5G/LTE イーサネット ゲートウェイ経由で開発キットに SSH 接続するには、次のオプションがあります。
- **開発キットの Wi-Fi アクセス ポイントを使用する**。 Wi-Fi が無効になっている場合は、開発キットを再起動して再度有効にすることができます。 そこから開発キットの Wi-Fi アクセス ポイントに接続し、[Azure Percept DK に SSH 接続する手順](./how-to-ssh-into-percept-dk.md)に従います。
- **ローカル ネットワーク (LAN) へのイーサネット接続を使用する**。 このオプションでは、開発キットを 5G/LTE ゲートウェイから取り外し、LAN ルーターに接続します。 詳細については、[イーサネット経由での接続方法](./how-to-connect-over-ethernet.md)に関するページを参照してください。 
- **ゲートウェイのリモート アクセス機能を使用する**。 多くの 5G/LTE ゲートウェイには、SSH 経由でネットワーク上のデバイスに接続するために使用できるリモート アクセス マネージャーが用意されています。 この機能が搭載されているかどうかは、5G/LTE ゲートウェイの製造元にご確認ください。 次に、[Cradlepoint 5G/LTE ゲートウェイ](https://customer.cradlepoint.com/s/article/NCM-Remote-Connect-LAN-Manager)のリモート アクセス マネージャーの例を示します。
- **開発キットのシリアル ポートを使用する**。 Azure Percept DK には、デバイスに直接接続するために使用できるシリアル接続ポートが含まれます。 詳細な手順については、[Azure Percept DK のシリアル接続](./how-to-connect-to-percept-dk-over-serial.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
アクセスできる携帯ネットワーク デバイスによっては、USB モード経由での接続を検討できます。

[USB モデムを使用して接続します](./connect-over-cellular-usb.md)。

5G または LTE のメインの記事に戻ります。

[5G または LTE を使用して接続します](./connect-over-cellular.md)。