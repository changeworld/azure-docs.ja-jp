---
title: OPC Publisher の概要 - Azure | Microsoft Docs
description: この記事では、OPC Publisher の機能について概要を紹介します。 JSON ペイロードを使用してエンコードされた JSON テレメトリ データを Azure IoT Hub に発行できます。
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: c77dff4a4f89a78dc0a0d723fbb45fe691246112
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681932"
---
# <a name="what-is-opc-publisher"></a>OPC Publisher の概要

OPC Publisher は、以下の方法を説明するリファレンス実装です。

- 既存の OPC UA サーバーに接続する。
- OPC UA Pub/Sub 形式で OPC UA サーバーから取得した、JSON でエンコードされた利用統計情報を、JSON ペイロードを使用して Azure IoT Hub に発行します。

Azure IoT Hub クライアント SDK でサポートされている任意のトランスポート プロトコルを使用できます。HTTPS、AMQP、および MQTT です。

リファレンス実装には以下が含まれます。

- ご利用のネットワーク上にある既存の OPC UA サーバーに接続する OPC UA "*クライアント*"。
- 発行されている内容の管理に使用できる ポート 62222 上の OPC UA "*サーバー*"。同じことをできる直接のメソッドが IoT Hub に用意されています。

GitHub から [OPC Publisher のリファレンス実装](https://github.com/Azure/iot-edge-opc-publisher)をダウンロードできます。

このアプリケーションは、.NET Core テクノロジを使用して実装され、.NET Core でサポートされている任意のプラットフォーム上で実行できます。

OPC Publisher は、一定数のキープアライブ要求に応答しないエンドポイントへの接続を確立する再試行ロジックを実装しています。 たとえば、停電のために OPC UA サーバーが応答しなくなったとします。

OPC UA サーバーに対する明確な発行間隔ごとに、この発行間隔ですべてのノードを更新する別のサブスクリプションが作成されます。

ネットワーク負荷を軽減するために、OPC Publisher は IoT Hub に送信されたデータのバッチ処理をサポートしています。 このバッチ処理により、構成されたパケット サイズに達した場合にのみ IoT Hub にパケットが送信されます。

このアプリケーションでは、NuGet パッケージとして OPC Foundation OPC UA リファレンス スタックを使用しています。 ライセンス条項については、[https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) を参照してください。

### <a name="next-steps"></a>次のステップ

OPC Publisher の概要を学習しました。推奨される次の手順は [OPC Publisher を構成する](howto-opc-publisher-configure.md)方法を学習することです。
