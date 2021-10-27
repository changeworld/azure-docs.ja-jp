---
title: Azure Percept を 5G または LTE ネットワークで接続する
description: この記事では、5G または LTE ネットワークを使用して Azure Percept DK を接続する方法について説明します。
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 0df673937ebd195c250a0f0a8880ecf89d42c844
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129997233"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks"></a>Azure Percept を 5G または LTE ネットワークで接続する

5G または LTE ネットワークを使用して Edge AI デバイスを接続する利点は数多くあります。 Edge AI が最も効果を発揮するのは、スマート シティ、自律走行車、農業など、Wi-Fi および LAN 接続に制限のある場所です。 さらに、5G または LTE ネットワークは、Wi-Fi よりも優れたセキュリティを実現します。 最後に、Edge で AI を実行する IoT デバイスを使用すると、5G または LTE ネットワーク上の帯域幅を最適化できます。 ほとんどのデータがデバイスで処理され、必要な情報だけがクラウドに送信されます。 現在、Azure Percept DK では、単純な USB モデムを使用した 5G/LTE ネットワークへの直接接続もサポートされています。 さまざまなオプションの詳細を以下に示します。

## <a name="options-for-connecting-azure-percept-dk-over-5g-or-lte-networks"></a>5G または LTE ネットワークを使用して Azure Percept DK を接続するためのオプション
追加のハードウェアを使用すると、5G または LTE 接続を使用して Azure Percept DK を接続できます。 現在サポートされているオプションは 3 つあります。各オプションの詳細へのリンクを確認できます。
- **USB 5G/LTE モデム デバイス** - Linux オペレーティング システムに USB モデムのサポートを追加するオープンソースの ModemManger SW をサポートする新しい SW イメージがリリースされています。 これにより、多くの場合安価であるさまざまな USB モデムを使用して、LTE または 5G ネットワーク上の Azure Percept に接続できます。 詳細については、[USB モデムを使用した接続](./connect-over-cellular-usb.md)に関するページを参照してください。   
- **5G/LTE イーサネット ゲートウェイ デバイス** - ここでは、Azure Percept は、イーサネット上の 5G/LTE ゲートウェイに接続されます。 詳細については、[移動体通信ゲートウェイを使用した接続](./connect-over-cellular-gateway.md)に関するページを参照してください。
- **5G/LTE Wi-Fi ホットスポット デバイス** - Azure Percept は、Wi-Fi ホットスポットによって提供される Wi-Fi ネットワークに接続されます。 この場合、開発キットは、他の Wi-Fi ネットワークと同様にネットワークに接続されます。 詳細な手順については、[Azure Percept DK セットアップ ガイド](./quickstart-percept-dk-set-up.md)に従い、ホットスポットからブロードキャストされた 5G または LTE Wi-Fi ネットワークを選択してください。


## <a name="considerations-when-selecting-a-5g-or-lte-device-in-general"></a>5G または LTE ゲートウェイ デバイス選択時の一般的な考慮事項
USB モデムであるかイーサネット ゲートウェイであるかに関わらず、5G/LTE デバイスでは、ダウンロードとアップロードの最大データ レートに影響を与えるさまざまなテクノロジがサポートされています。 公表されているデータ レートは決定するためのガイダンスになりますが、実際にそのレートに到達することはめったにありません。 ニーズに合った適切なデバイスを選択するためのガイダンスを次に示します。
 
- **LTE CAT-1** では、最大 10 Mbps のダウン ストリームと 5 Mbps のアップ ストリームが提供されます。 オブジェクト検出や音声アシスタントの作成など、Azure Percept Devkit の既定の機能には十分です。 ただし、クラウドへのビデオ ストリーミング データを必要とするソリューションには十分ではない場合があります。
- **LTE CAT-3 と 4** では、最大 100 Mbps のダウン ストリームと 50 Mbps のアップ ストリームが提供されます。これは、クラウドにビデオをストリーミングするのに十分です。 ただし、完全 HD 品質のビデオをストリーミングするには十分ではありません。
- **LTE CAT-5 以上** では、1 台のデバイスで HD ビデオをストリーミングするのに十分な高さのデータ レートが提供されます。 複数のデバイスを 1 つのゲートウェイに接続する必要がある場合は、5G を検討する必要があります。
- **5G ゲートウェイ** では、将来のシナリオに最適に対応できます。 一度に複数のデバイスに対して高いデータ スループットをサポートできるデータ レートと帯域幅が提供されます。 また、データ転送の待機時間が短くなります。


## <a name="next-steps"></a>次のステップ
アクセスする携帯電話デバイスに応じて、次のリンクに従って Azure Percept 開発キットに接続します。

[移動体通信ゲートウェイを使用して接続する](./connect-over-cellular-gateway.md)。

[USB モデムを使用して接続する](./connect-over-cellular-usb.md)。
