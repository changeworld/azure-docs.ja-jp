---
title: Microsoft OPC Publisher のパフォーマンスとメモリの調整
description: このチュートリアルでは、OPC Publisher のパフォーマンスとメモリを調整する方法について説明します。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 89e288d1186efd405019d6474dcbd332e7925d67
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787379"
---
# <a name="tutorial-tune-the-opc-publisher-performance-and-memory"></a>チュートリアル: OPC Publisher のパフォーマンスとメモリを調整する

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * パフォーマンスを調整する
> * メッセージ フローをメモリ リソースに合わせて調整する

運用環境の設定で OPC Publisher を実行する場合は、ネットワーク パフォーマンス要件 (スループットと待機時間) とメモリ リソースを考慮する必要があります。 OPC Publisher では、これらの要件を満たせるように、次のコマンド ライン パラメーターが公開されています。

* メッセージ キューの容量 (バージョン 2.5 以前では `mq`、バージョン 2.6 では利用不可、バージョン 2.7 では `om`)
* IoT Hub 送信間隔 (`si`)
* IoT Hub メッセージ サイズ (`ms`)

## <a name="adjusting-iot-hub-send-interval-and-iot-hub-message-size"></a>IoT Hub 送信間隔と IoT Hub メッセージ サイズの調整

`mq/om` パラメーターによって、内部メッセージ キューの容量の上限が制御されます。 このキューには、IoT Hub に送信される前にすべてのメッセージがバッファーされます。 キューの既定のサイズは、OPC Publisher バージョン 2.5 以前では最大 2 MB、バージョン 2.7 では 4,000 件の IoT Hub メッセージです (つまり、IoT Hub メッセージ サイズの設定が 256 KB の場合、キューのサイズは最大 1 GB になります)。 OPC Publisher がメッセージを IoT Hub に十分な速度で送信できない場合は、このキュー内の項目の数が増加します。 テストの実行中にこれが発生した場合は、軽減するために次のいずれか、または両方を実行できます。

* IoT Hub 送信間隔を短くする (`si`)

* IoT Hub メッセージ サイズを増やす (`ms`、設定できる最大値は 256 KB です)

`si` および `ms` パラメーターを調整してもキューが増大し続ける場合は、最終的にキューの最大容量に達し、メッセージが失われます。 これは、`si` と `ms` の両方のパラメーターに物理的な制限があり、OPC Publisher と IoT Hub 間のインターネット接続が、特定のシナリオで送信する必要があるメッセージの数に対して十分な速度でないことが原因です。 その場合、効果が得られるのは、いくつかの並列 OPC Publisher を設定することだけです。 また、`mq/om` パラメーターは、OPC Publisher によるメモリ消費に最大の影響を与えます。 

`si` パラメーターを使用すると、OPC Publisher が指定された間隔で IoT Hub にメッセージを送信するように強制できます。 メッセージが送信されるのは、最大 IoT Hub メッセージ サイズの 256 KB のデータが取得される (送信間隔のリセットがトリガーされる) か、指定された間隔時間が経過した場合です。

`ms` パラメーターを指定すると、IoT Hub に送信されるメッセージのバッチ処理が有効になります。 ほとんどのネットワーク設定では、IoT Hub に 1 つのメッセージを送信する際の待機時間は、ペイロードの送信にかかる時間と比較して長くなります。 これは、主にサービスの品質 (QoS) の要件に起因し、メッセージが IoT Hub によって処理された後にのみ受信確認されるためです。 したがって、IoT Hub に到着するデータの遅延が許容される場合は、`ms` パラメーターを 0 に設定して、OPC Publisher で 256 KB の最大メッセージ サイズが使用されるように構成する必要があります。 また、これは最もコスト効率の高い OPC Publisher の使用方法でもあります。

既定の構成では、10 秒ごと (`si=10`)、または 256 KB の IoT Hub メッセージ データが取得された (`ms=0`) ときに、データが IoT Hub に送信されます。 これにより、最大 10 秒の遅延が追加されますが、メッセージ サイズが大きいためにデータが失われる可能性は低くなります。 OPC Publisher バージョン 2.5 以前のメトリック `monitored item notifications enqueue failure` と OPC Publisher バージョン 2.7 の `messages lost` は、失われたメッセージの数を示します。

`si` と `ms` の両方のパラメーターを 0 に設定した場合、データが取得されるとすぐに OPC Publisher によって IoT Hub にメッセージが送信されます。 このため、平均 IoT Hub メッセージ サイズは 200 バイトをわずかに超える結果になります。 ただし、この構成の利点は、OPC Publisher で接続済み資産からのデータが遅延なしで送信されることです。 大量のデータを発行する必要があるユース ケースの場合は、失われるメッセージの数が多くなるため、こうしたシナリオにはこれは推奨されません。

OPC Publisher のパフォーマンスを測定するには、`di` パラメーターを使用して、指定した間隔 (秒単位) でパフォーマンス メトリックをトレース ログに出力します。

## <a name="next-steps"></a>次のステップ
OPC Publisher のパフォーマンスとメモリを調整する方法を学んだので、その他のリソースについて OPC Publisher GitHub リポジトリを確認できます。

> [!div class="nextstepaction"]
> [OPC Publisher GitHub リポジトリ](https://github.com/Azure/Industrial-IoT)