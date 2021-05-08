---
title: Azure Industrial IoT の概要
description: この記事では、産業用 IoT の概要を示します。 IIoT における作業現場の接続とセキュリティのコンポーネントについて説明します。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: 940391d26b5a8455fef01c8094cd08e05ab51290
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787459"
---
# <a name="what-is-industrial-iot-iiot"></a>産業用 IoT (IIoT) の概要

IIoT (産業用モノのインターネット) は、製造業で IoT を応用することで産業効率を高めます。

![産業用 IoT](media/overview-what-is-Industrial-IoT/icon-255-px.png)

## <a name="improve-industrial-efficiencies"></a>産業効率の向上
Azure Industrial IoT を使用して、業務の生産性と収益性を高めます。 オンサイトの資産にアクセスするという時間のかかるプロセスを縮小します。 工場現場で既に稼働している機械を含む、クラウド内の産業機器やデバイスを接続して監視します。 IoT データを分析することで、現場全体の業績向上に役立つ分析情報を入手できます。

## <a name="industrial-iot-components"></a>産業用 IoT のコンポーネント

**IoT Edge デバイス**: IoT Edge デバイスは、Edge ランタイムと Edge モジュールで構成されています。 
- "*Ege モジュール*" は、OPC Publisher や OPC Twin など、評価の最小単位である Docker コンテナーです。 
- "*Edge デバイス*" は、このようなモジュールのデプロイに使用され、クラウド内の OPC UA サーバーと IoT Hub の間の仲介役として機能します。 IoT Edge の詳細については、[こちら](https://azure.microsoft.com/services/iot-edge/)を参照してください。

**IoT Hub**: IoT Hub は、IoT アプリケーションとそれが管理するデバイスの間の双方向通信のための中央メッセージ ハブとして機能します。 これは、オープンソースの SDK と複数のプロトコルをサポートするオープンで柔軟な、サービスとしてのクラウド プラットフォームです。 IoT Hub の詳細については、[こちら](https://azure.microsoft.com/services/iot-hub/)を参照してください。

**産業用エッジ モジュール**
- *OPC Publisher*: OPC Publisher は IoT Edge 内で実行されます。 これは、OPC UA サーバーに接続し、これらのサーバーから JSON でエンコードされた利用統計情報を OPC UA "Pub/Sub" 形式で Azure IoT Hub に発行します。 Azure IoT Hub クライアント SDK でサポートされているすべてのトランスポート プロトコル (つまり、HTTPS、AMQP、MQTT) を使用できます。
- *OPC Twin*: OPC Twin は、Azure IoT Edge と IoT Hub を使用してクラウドと工場のネットワークとを接続するマイクロサービスから成ります。 OPC Twin は、REST API を通じて、産業用デバイスの検出、登録、リモート制御を実現します。 OPC Twin には、OPC Unified Architecture (OPC UA) SDK は不要です。 これは、プログラミング言語に依存せず、サーバーレス ワークフローに組み込むことができます。
- *Discovery*: 探索プログラム ID によって表される検出モジュールは、エッジに検出サービス (OPC UA サーバー検出を含む) を提供します。 検出が構成され、有効になっている場合、このモジュールにより、スキャン プローブの結果が IoT Edge および IoT Hub テレメトリ パスを介してオンボード サービスに送信されます。 このサービスで、結果が処理され、レジストリ内のすべての関連する ID が更新されます。


**Azure で産業用資産の検出、登録、管理を行う**: Azure Industrial IoT を使用すると、工場のオペレーターは、工場ネットワーク内の OPC UA 対応サーバーを検出し、Azure IoT Hub に登録できます。 運用担当者は、世界中のどこからでも工場現場のイベントをサブスクライブし、それに対処できます。 マイクロサービスの REST API は、OPC UA サービスのエッジ側をミラー化します。 これらは、Azure Active Directory (AAD) によってサポートされる OAUTH 認証と認可を使用して保護されます。 これにより、クラウド アプリケーションで、サーバーのアドレス空間の参照、変数の読み取り/書き込み、HTTPS と単純な OPC UA JSON ペイロードを使用したメソッドの実行が可能になります。

## <a name="next-steps"></a>次のステップ
産業用 IoT の概要を学んだので、Industrial IoT Platform と OPC Publisher について確認できます。

> [!div class="nextstepaction"]
> [Industrial IoT Platform の概要](overview-what-is-industrial-iot-platform.md)

> [!div class="nextstepaction"]
> [OPC Publisher の概要](overview-what-is-opc-publisher.md)