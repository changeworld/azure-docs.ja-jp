---
title: Azure Percept を 5G または LTE ネットワークで接続する
description: この記事では、5G または LTE ネットワークを使用して Azure Percept DK を接続する方法について説明します。
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 07/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 202bec8182cc2ae04e9fadffe01977150ea93dda
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222315"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks"></a>Azure Percept を 5G または LTE ネットワークで接続する

5G または LTE ネットワークを使用して Edge AI デバイスを接続する利点は数多くあります。 Edge AI が最も効果を発揮するのは、スマート シティ、自律走行車、農業など、Wi-Fi および LAN 接続に制限のある場所です。 さらに、5G または LTE ネットワークは、Wi-Fi よりも優れたセキュリティを実現します。 最後に、Edge で AI を実行する IoT デバイスを使用すると、5G または LTE ネットワーク上の帯域幅を最適化できます。 この場合、ほとんどのデータがデバイスで処理され、必要な情報だけがクラウドに送信されます。 現在、Azure Percept DK は 5G または LTE ネットワークに直接接続できません。 ただし、組み込みのイーサネットと Wi-Fi 機能を使用すると、5G または LTE ゲートウェイに接続できます。 この記事では、その仕組みについて説明します。

## <a name="options-for-connecting-the-azure-percept-dk-over-5g-or-lte-networks"></a>5G または LTE ネットワークを使用して Azure Percept DK を接続するためのオプション
追加のハードウェアを使用すると、5G または LTE 接続を使用して Azure Percept DK を接続できます。 現在、主に次の 2 つのオプションがサポートされています。
- **5G/LTE Wi-Fi ホットスポット デバイス** - 開発キットは、Wi-Fi ホットスポットによって提供される Wi-Fi ネットワークに接続されます。 この場合、開発キットは、他の Wi-Fi ネットワークと同様にネットワークに接続されます。 詳細な手順については、[Azure Percept DK セットアップ ガイド](./quickstart-percept-dk-set-up.md)に従い、ホットスポットからブロードキャストされた 5G または LTE Wi-Fi ネットワークを選択してください。
- **5G/LTE イーサネット ゲートウェイ デバイス** - ここでは、開発キットがイーサネット経由で 5G/LTE ゲートウェイに接続されます。Wi-Fi 接続と比較してセキュリティが強化されているという利点があります。 この記事の残りの部分では、このようなネットワークの構成方法について詳しく説明します。

## <a name="5glte-gateway-topology"></a>5G/LTE ゲートウェイのトポロジ
:::image type="Image" source="media/connect-over-cellular/topology.png" alt-text="この図は、Azure Percept DK がイーサネット経由で 5G/LTE ゲートウェイに接続する方法を示しています。":::

上の図から、5G/LTE ゲートウェイを Azure Percept DK と簡単にペアリングする方法がわかります。

## <a name="considerations-when-connecting-to-a-5g-or-lte-gateway"></a>5G/LTE ゲートウェイへの接続時の考慮事項
Azure Percept DK を 5G/LTE ゲートウェイに接続するときに考慮する重要ポイントを次に示します。
- 最初にゲートウェイを設定し、SIM 経由で接続を受けていることを確認します。 これにより、Azure Percept DK の接続時に発生した問題を容易にトラブルシューティングできるようになります。
- イーサネット ケーブルの両端をゲートウェイと Azure Percept DK に確実に接続します。
- イーサネット経由で Azure Percept DK を接続するための[既定の手順](./how-to-connect-over-ethernet.md)に従います。
- 5G/LTE プランにクォータがある場合は、Azure Percept DK モデルからクラウドに送信されるデータの量を最適化することをお勧めします。
- 外部から発信された受信トラフィックをブロックする、[適切に構成されたファイアウォール](./concept-security-configuration.md)を確保します。

## <a name="ssh-over-a-5g-or-lte-network"></a>5G または LTE ネットワーク経由の SSH
5G/LTE イーサネット ゲートウェイ経由で開発キットに SSH 接続するには、次のオプションがあります。
- **開発キットの Wi-Fi アクセス ポイントを使用する**。 Wi-Fi が無効になっている場合は、開発キットを再起動して再度有効にすることができます。 そこから開発キットの Wi-Fi アクセス ポイントに接続し、[これらの SSH の手順](./how-to-ssh-into-percept-dk.md)に従います。
- **ローカル ネットワーク (LAN) へのイーサネット接続を使用する**。 このオプションでは、開発キットを 5G/LTE ゲートウェイから取り外し、LAN ルーターに接続します。 詳細については、[イーサネット経由での接続方法](./how-to-connect-over-ethernet.md)に関するページを参照してください。 
- **ゲートウェイのリモート アクセス機能を使用する**。 多くの 5G/LTE ゲートウェイには、SSH 経由でネットワーク上のデバイスに接続するために使用できるリモート アクセス マネージャーが用意されています。 この機能が搭載されているかどうかは、5G/LTE ゲートウェイの製造元にご確認ください。 次に、[Cradlepoint 5G/LTE ゲートウェイ](https://customer.cradlepoint.com/s/article/NCM-Remote-Connect-LAN-Manager)のリモート アクセス マネージャーの例を示します。
- **開発キットのシリアル ポートを使用する**。 Azure Percept DK には、デバイスに直接接続するために使用できるシリアル接続ポートが含まれます。 詳細な手順については、[Azure Percept DK のシリアル接続](./how-to-connect-to-percept-dk-over-serial.md)に関するページを参照してください。

## <a name="considerations-when-selecting-a-5g-or-lte-gateway-device"></a>5G または LTE ゲートウェイ デバイスの選択時の考慮事項
5G または LTE ゲートウェイでは、ダウンロードとアップロードの最大データ レートに影響を与えるさまざまなテクノロジがサポートされています。 アドバタイズされたデータ レートは、意思決定のための指針となりますが、通常はこれに到達することはありません。 ニーズに合った適切なゲートウェイを選択するためのガイダンスを次に示します。
 
- **LTE CAT-1** では、最大 10 Mbps のダウン ストリームと 5 Mbps のアップ ストリームが提供されます。 オブジェクト検出や音声アシスタントの作成など、Azure Percept Devkit の既定の機能には十分です。 ただし、クラウドへのビデオ ストリーミング データを必要とするソリューションには十分ではない場合があります。
- **LTE CAT-3 と 4** では、最大 100 Mbps のダウン ストリームと 50 Mbps のアップ ストリームが提供されます。これは、クラウドにビデオをストリーミングするのに十分です。 ただし、完全 HD 品質のビデオをストリーミングするには十分ではありません。
- **LTE CAT-5 以上** では、1 台のデバイスで HD ビデオをストリーミングするのに十分な高さのデータ レートが提供されます。 複数のデバイスを 1 つのゲートウェイに接続する必要がある場合は、5G を検討する必要があります。
- **5G ゲートウェイ** では、将来のシナリオに最適に対応できます。 一度に複数のデバイスに対して高いデータ スループットをサポートできるデータ レートと帯域幅が提供されます。 また、データ転送の待機時間が短くなります。


## <a name="next-steps"></a>次のステップ
お使いの 5G/LTE ゲートウェイに Azure Percept DK を接続する場合は、次の手順に従います。
- [イーサネット経由で Azure Percept DK を接続する方法](./how-to-connect-over-ethernet.md)
