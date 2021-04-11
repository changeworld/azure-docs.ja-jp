---
title: Microsoft OPC Publisher
description: この記事では、OPC Publisher Edge モジュールの概要を紹介します。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 3a44bdbadfe6ecd86a1b98fb7002f2d75c23bb6a
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800535"
---
# <a name="what-is-the-opc-publisher"></a>OPC Publisher とは

OPC Publisher は、オープンで開発された完全にサポートされている Microsoft 製品であり、産業用資産と Microsoft Azure クラウドの間のギャップを埋めます。 これを行うために、OPC UA 対応の資産または産業用接続ソフトウェアに接続し、IEC62541 OPC UA PubSub 標準形式 (バージョン2.6 以降) などのさまざまな形式で Azure IoT Hub にテレメトリ データを発行します。

これは、Azure IoT Edge でモジュールとして、またはプレーン Docker でコンテナーとして実行されます。 .NET クロスプラットフォーム ランタイムを利用するので、Linux と Windows 10 でネイティブにも実行されます。

OPC Publisher は、以下の方法を説明するリファレンス実装です。

- 既存の OPC UA サーバーに接続する。
- OPC UA Pub/Sub 形式で OPC UA サーバーから取得した、JSON でエンコードされた利用統計情報を、JSON ペイロードを使用して Azure IoT Hub に発行します。

Azure IoT Hub クライアント SDK でサポートされている任意のトランスポート プロトコルを使用できます。HTTPS、AMQP、および MQTT です。

リファレンス実装には以下が含まれます。

- ご利用のネットワーク上にある既存の OPC UA サーバーに接続する OPC UA "*クライアント*"。
- 発行されている内容の管理に使用できる ポート 62222 上の OPC UA "*サーバー*"。同じことをできる直接のメソッドが IoT Hub に用意されています。

GitHub から [OPC Publisher のリファレンス実装](https://github.com/Azure/iot-edge-opc-publisher)をダウンロードできます。

このアプリケーションは、.NET Core テクノロジを使用して実装され、.NET Core でサポートされている任意のプラットフォーム上で実行できます。

## <a name="what-does-the-opc-publisher-do"></a>OPC Publisher の機能

OPC Publisher は、一定数のキープアライブ要求に応答しないエンドポイントへの接続を確立する再試行ロジックを実装しています。 たとえば、停電のために OPC UA サーバーが応答しなくなったとします。

OPC UA サーバーに対する明確な発行間隔ごとに、この発行間隔ですべてのノードを更新する別のサブスクリプションが作成されます。

ネットワーク負荷を軽減するために、OPC Publisher は IoT Hub に送信されたデータのバッチ処理をサポートしています。 このバッチ処理により、構成されたパケット サイズに達した場合にのみ IoT Hub にパケットが送信されます。

このアプリケーションでは、NuGet パッケージとして OPC Foundation OPC UA リファレンス スタックを使用しています。 ライセンス条項については、[https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) を参照してください。

## <a name="next-steps"></a>次の手順
OPC Publisher が何であるかについて学習したので、これをデプロイして使い始めることができます。

> [!div class="nextstepaction"]
> [OPC Publisher をスタンドアロン モードでデプロイする](tutorial-publisher-deploy-opc-publisher-standalone.md)
