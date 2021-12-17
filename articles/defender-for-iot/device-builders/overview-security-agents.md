---
title: セキュリティ エージェント
description: IoT デバイスでの Microsoft Defender for IoT セキュリティ サービス エージェントについて理解を深め、構成、デプロイ、および使用を開始します。
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 86c0295726989d142e10e01e8f4950cfcf385126
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331549"
---
# <a name="get-started-with-microsoft-defender-for-iot-device-micro-agents"></a>Microsoft Defender for IoT デバイス マイクロ エージェントの使用を開始する

Defender for IoT セキュリティ エージェントでは、オペレーティング システム構成のベスト プラクティスの監視など、強化されたセキュリティ機能が提供されています。 1つのサービスで、デバイス フィールドの脅威保護とセキュリティ体制を制御します。

Azure Defender for IoT セキュリティ エージェントでは、デバイスのオペレーティング システムからの未加工イベント収集、コストを削減するためのイベント集計、およびデバイス モジュール ツインを使用した構成が処理されます。 セキュリティ メッセージは、IoT Hub を介して Defender for IoT 分析サービスへ送信されます。

次のワークフローを使用して、Defender for IoT セキュリティ エージェントをデプロイしてテストします。

1. [IoT Hub で Defender for IoT サービスを有効にします](quickstart-onboard-iot-hub.md)。

1. IoT Hub に登録済みデバイスがない場合は、[新しいデバイスを登録](/previous-versions/azure/iot-accelerators/iot-accelerators-device-simulation-overview)します。

1. デバイスの [DefenderIotMicroAgent モジュール ツインを作成](quickstart-create-micro-agent-module-twin.md)します。

1. 実際のデバイスにインストールするのではなく、Azure シミュレート デバイスにエージェントをインストールするには、[新しい Azure Virtual Machine (VM ) を作成](../../virtual-machines/linux/quick-create-portal.md)します。

1. IoT デバイスまたは新しい VM 上に [Defender for IoT セキュリティ エージェントをデプロイ](how-to-deploy-linux-cs.md)します。

1. OS ベースライン イベントを実行するには、[trigger_events](https://aka.ms/iot-security-github-trigger-events) の手順に従います。

1. 前の手順でシミュレートされた OS ベースライン チェックの失敗に応じて、Defender for IoT の推奨事項を確認します。 スクリプトを実行した 30 分後に確認を開始してください。

## <a name="next-steps"></a>次のステップ

- [ソリューションを構成する](quickstart-configure-your-solution.md)
- [Defender-IoT-micro-agent の作成](quickstart-create-security-twin.md)
- [カスタム アラートを構成する](quickstart-create-custom-alerts.md)
- [セキュリティ エージェントをデプロイする](how-to-deploy-agent.md)
