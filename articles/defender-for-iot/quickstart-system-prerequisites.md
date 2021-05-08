---
title: 'クイックスタート: システムの前提条件'
description: このクイックスタートでは、Azure Defender for IoT を実行するために必要なシステムの前提条件を確認します。
ms.date: 11/30/2020
ms.topic: quickstart
ms.openlocfilehash: 2aae849b6ee772c2aa29c680f3b107af3ed600b0
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382948"
---
# <a name="quickstart-system-prerequisites"></a>クイックスタート: システムの前提条件

この記事では、Azure Defender for IoT を実行するためのシステムの前提条件を示します。

## <a name="prerequisites"></a>前提条件

- なし

## <a name="minimum-requirements"></a>最小要件

- SPAN ポート経由のトラフィック監視をサポートするネットワーク スイッチ。
- NTA センサー用のハードウェア アプライアンス。
- Azure サブスクリプションの共同作成者ロール。 これは、コミットされたデバイスと Azure Sentinel への接続を定義するために、オンボード時にのみ必要です。
- Azure IoT Hub (Free または Standard レベル) の **共同作成者** ロール (クラウド接続管理用)。 **Azure Defender for IoT** 機能が有効になっていることを確認します。
- デバイスレベルの Defender-IoT-micro-agent のサポートについては、Defender for IoT エージェントによってサポートされるデバイスとプラットフォームの数が増えています。 [サポートされているプラットフォーム](how-to-deploy-agent.md)の一覧を参照してください。

## <a name="supported-service-regions"></a>サポートされているサービス リージョン

Defender for IoT では、すべてのヨーロッパ リージョンのすべてのトラフィックを、西ヨーロッパ リージョン データセンターにルーティングします。 残りのすべてのリージョンからのトラフィックは米国中部リージョン データセンターにルーティングします。

詳細については、[IoT Hub によりサポートされるリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [必要なアプライアンスを特定する](how-to-identify-required-appliances.md)
