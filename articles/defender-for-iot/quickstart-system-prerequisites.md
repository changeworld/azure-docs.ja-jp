---
title: システムの前提条件
description: Azure Defender for IoT を実行するために必要なシステムの前提条件を確認します。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 7b609fec2a47dc0685b30dac12f43263127f70ef
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523211"
---
# <a name="system-prerequisites"></a>システムの前提条件
この記事では、Azure Defender for IoT を実行するためのシステムの前提条件を示します。

## <a name="minimum-requirements"></a>最小要件

- SPAN ポート経由のトラフィック監視をサポートするネットワーク スイッチ。
- NTA センサー用のハードウェア アプライアンス。
- Azure サブスクリプションの共同作成者ロール。 これは、コミットされたデバイスと Azure Sentinel への接続を定義するために、オンボード時にのみ必要です。
- Azure IoT Hub (Free または Standard レベル) の **共同作成者** ロール (クラウド接続管理用)。 **Azure Defender for IoT** 機能が有効になっていることを確認します。
- デバイスレベルのセキュリティ モジュールのサポートについては、Defender for IoT エージェントによってサポートされるデバイスとプラットフォームの数が増えています。 [サポートされているプラットフォーム](how-to-deploy-agent.md)の一覧を参照してください。

## <a name="supported-service-regions"></a>サポートされているサービス リージョン

Defender for IoT では、すべてのヨーロッパ リージョンのすべてのトラフィックを、西ヨーロッパ リージョン データセンターにルーティングします。 残りのすべてのリージョンからのトラフィックは米国中部リージョン データセンターにルーティングします。

詳細については、[IoT Hub によりサポートされるリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

[必要なアプライアンスを特定する](how-to-identify-required-appliances.md)
[Azure Defender for IoT のネットワーク設定について](how-to-set-up-your-network.md)
