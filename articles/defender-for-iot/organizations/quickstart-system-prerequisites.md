---
title: 'クイックスタート: システムの前提条件'
description: このクイックスタートでは、Microsoft Defender for IoT を実行するために必要なシステムの前提条件を確認します。
ms.date: 11/09/2021
ms.topic: quickstart
ms.openlocfilehash: f4f0912aade1f00623637791bc05aeee3a8747cb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278305"
---
# <a name="quickstart-system-prerequisites"></a>クイックスタート: システムの前提条件

この記事では、Microsoft Defender for IoT を実行するためのシステムの前提条件を示します。

## <a name="prerequisites"></a>前提条件

- なし

## <a name="minimum-requirements"></a>最小要件

- SPAN ポート経由のトラフィック監視をサポートするネットワーク スイッチ。
- NTA センサー用のハードウェア アプライアンス。
- Azure サブスクリプションの共同作成者ロール。 これは、コミットされたデバイスと Microsoft Sentinel への接続を定義するために、オンボード時にのみ必要です。
- Azure IoT Hub (Free または Standard レベル) の **共同作成者** ロール (クラウド接続管理用)。 **Microsoft Defender for IoT** 機能が有効になっていることを確認します。

## <a name="supported-service-regions"></a>サポートされているサービス リージョン

Defender for IoT では、すべてのヨーロッパ リージョンのすべてのトラフィックを、西ヨーロッパ リージョン データセンターにルーティングします。 残りのすべてのリージョンからのトラフィックは米国中部リージョン データセンターにルーティングします。

詳細については、[IoT Hub によりサポートされるリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [必要なアプライアンスを特定する](how-to-identify-required-appliances.md)
