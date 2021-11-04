---
title: Azure IoT Edge で接続済みレジストリを使用する
description: 階層型 IoT Edge シナリオでの接続済み Azure コンテナー レジストリの概要
author: toddysm
ms.author: memladen
ms.service: container-registry
ms.topic: overview
ms.date: 08/24/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3f5be0a18c2185dffa685291121c8a730d60ef52
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090074"
---
# <a name="using-connected-registry-with-azure-iot-edge"></a>Azure IoT Edge で接続済みレジストリを使用する

この記事では、階層型 [IoT Edge](../iot-edge/about-iot-edge.md) シナリオでの Azure [接続済み](intro-connected-registry.md)レジストリの使用について説明します。 接続済みコンテナー レジストリは、IoT Edge モジュールとして展開することができ、階層内のデバイスに必要なコンテナー イメージを提供する上で重要な役割を果たします。

## <a name="what-is-a-hierarchical-iot-edge-deployment"></a>階層型 IoT Edge 展開とは?

Azure IoT Edge を使用すると、階層レイヤーに編成された複数のネットワークにわたって IoT Edge デバイスを展開できます。 階層内の各レイヤーは、その下のレイヤーにあるデバイスからのメッセージと要求を処理する[ゲートウェイ デバイス](../iot-edge/iot-edge-as-gateway.md)です。 最上位レイヤーだけがクラウドに接続できるように、デバイスの階層構造を構築できます。下位レイヤーは、その上下に隣接するレイヤーとだけ通信できます。 このネットワーク レイヤー化は、ほとんどの産業用ネットワークの基礎であり、[ISA-95 標準](https://en.wikipedia.org/wiki/ANSI/ISA-95)に準拠しています。

IoT Edge デバイスの階層を作成する方法については、「[チュートリアル: IoT Edge デバイスの階層を作成する][tutorial-nested-iot-edge]」を参照してください。

## <a name="how-do-i-use-connected-registry-in-hierarchical-iot-edge-scenarios"></a>階層型 IoT Edge シナリオでの接続済みレジストリの使用方法

次の図は、接続済みレジストリを使用して IoT Edge の階層型展開をサポートする方法を示しています。 灰色の実線は実際のネットワーク フローを示し、破線はコンポーネントと接続済みレジストリとの間の論理通信を示します。

![接続済みレジストリと階層型 IoT Edge 展開](media/overview-connected-registry-and-iot-edge/connected-registry-iot-edge-overview.svg)

### <a name="top-layer"></a>上部レイヤー

サンプル アーキテクチャの最上位層である *レイヤー 5: Enterprise Network* は IT によって管理され、Azure クラウド内の Contoso のコンテナー レジストリにアクセスできます。 接続済みレジストリは IoT Edge VM 上の IoT Edge モジュールとして展開され、クラウド レジストリと直接通信してイメージと成果物をプルおよびプッシュできます。 

接続済みレジストリは、既定の [ReadWrite モード](intro-connected-registry.md#modes)で動作しているものとして表示されます。 この接続済みレジストリのクライアントは、イメージと成果物をプルおよびプッシュできます。 プッシュされたイメージはクラウド レジストリと同期されます。 そのレイヤーでプッシュが必要ない場合は、接続済みレジストリを [ReadOnly モード](intro-connected-registry.md#modes)で動作するように変更できます。

接続済みレジストリをこのレベルで IoT Edge モジュールとして展開する手順については、「[クイック スタート - 接続済みレジストリを IoT Edge デバイスに展開する][quickstart-deploy-connected-registry-iot-edge-cli]」を参照してください。

### <a name="nested-layers"></a>入れ子になったレイヤー

次の下位レイヤーである *レイヤー 4: Site Business Planning and Logistics* は、レイヤー 5 とのみ通信するように構成されています。 したがって、レイヤー 4 に IoT Edge VM を展開するときには、代わりにレイヤー 5 の接続済みレジストリからモジュール イメージをプルする必要があります。 

ReadOnly モードで動作する接続済みレジストリを展開して、下位レイヤーにサービスを提供することもできます。 この点は、*レイヤー 3: Industrial Security Zone* の IoT Edge VM によって例示されています。 その VM は、*レイヤー 4* の接続済みレジストリからモジュール イメージをプルする必要があります。 下位レイヤーのクライアントにサービスを提供する必要がある場合は、ReadOnly モードの接続済みレジストリをレイヤー 3 に展開できます (以下同様)。

このアーキテクチャでは、各レイヤーに展開された接続済みレジストリは、イメージを上位のレイヤーの接続済みレジストリと同期するように構成されています。 接続済みレジストリは IoT Edge モジュールとして展開され、展開とネットワーク ルーティングに IoT Edge メカニズムを活用します。

入れ子になった IoT Edge デバイスに接続済みレジストリを展開する手順については、「[クイック スタート: 入れ子になった IoT Edge デバイスに接続済みレジストリを展開する][tutorial-deploy-connected-registry-nested-iot-edge-cli]」を参照してください。

## <a name="next-steps"></a>次のステップ

この概要では、階層的 IoT Edge シナリオでの接続済みレジストリの使用について説明しました。 引き続き、次の記事では接続済みレジストリを構成して IoT Edge デバイスに展開する方法を説明します。

> [!div class="nextstepaction"]
> [クイック スタート - CLI を使用して接続済みレジストリを作成する][quickstart-connected-registry-cli]

> [!div class="nextstepaction"]
> [クイック スタート - ポータルを使用して接続済みレジストリを作成する][quickstart-connected-registry-portal]

> [!div class="nextstepaction"]
> [クイック スタート - 接続済みレジストリを IoT Edge デバイスに展開する][quickstart-deploy-connected-registry-iot-edge-cli]

> [!div class="nextstepaction"]
> [チュートリアル: 入れ子になった IoT Edge デバイスに接続済みレジストリを展開する][tutorial-deploy-connected-registry-nested-iot-edge-cli]

<!-- LINKS - internal -->
[quickstart-connected-registry-cli]:quickstart-connected-registry-cli.md
[quickstart-connected-registry-portal]:quickstart-connected-registry-portal.md
[quickstart-deploy-connected-registry-iot-edge-cli]:quickstart-deploy-connected-registry-iot-edge-cli.md
[tutorial-nested-iot-edge]:../iot-edge/tutorial-nested-iot-edge.md
[tutorial-deploy-connected-registry-nested-iot-edge-cli]: tutorial-deploy-connected-registry-nested-iot-edge-cli.md
