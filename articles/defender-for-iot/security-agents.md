---
title: セキュリティ エージェントの概要
description: IoT デバイスでの Azure Defender for IoT セキュリティ サービス エージェントについて理解を深め、構成、デプロイ、および使用を開始します。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 888fdbb2352c4ac972eac8b24c29d22ce973e04d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90931535"
---
# <a name="get-started-with-azure-defender-for-iot-device-security-agents"></a>Azure Defender for IoT デバイス セキュリティ エージェントの使用を開始する

Azure Defender for IoT セキュリティ エージェントでは、リモート接続、アクティブ アプリケーション、ログイン イベント、およびオペレーティング システム構成のベスト プラクティスの監視など、強化されたセキュリティ機能が提供されています。 1つのサービスで、デバイス フィールドの脅威保護とセキュリティ体制を制御します。

Linux および Windows のセキュリティ エージェントの参照アーキテクチャは C# と C の両方で提供されています。

Azure Defender for IoT セキュリティ エージェントでは、デバイスのオペレーティング システムからの未加工イベント収集、コストを削減するためのイベント集計、およびデバイス モジュール ツインを使用した構成が処理されます。 セキュリティ メッセージは、IoT Hub を介して Defender for IoT 分析サービスへ送信されます。

次のワークフローを使用して、Defender for IoT セキュリティ エージェントをデプロイしてテストします。

1. [IoT Hub で Defender for IoT サービスを有効にする](quickstart-onboard-iot-hub.md)
1. IoT Hub に登録済みデバイスがない場合は、[新しいデバイスを登録](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy)します。
1. デバイス用の [azureiotsecurity セキュリティ モジュール](quickstart-create-security-twin.md)を作成します。
1. 実際のデバイスにインストールするのではなく、Azure シミュレート デバイスにエージェントをインストールするには、使用可能なゾーンで[新しい Azure Virtual Machine (VM ) を作成](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)します。
1. IoT デバイスまたは新しい VM 上に [Defender for IoT セキュリティ エージェントをデプロイ](how-to-deploy-linux-cs.md)します。
1. 無害な攻撃シミュレーションを実行するには、[trigger_events](https://aka.ms/iot-security-github-trigger-events) の手順に従います。
1. 前の手順のシミュレートした攻撃への応答で Defender for IoT アラートを確認します。 スクリプトを実行した 5 分後に確認を開始してください。
1. IoT Hub を使用して、[アラート](concept-security-alerts.md)、[推奨事項](concept-recommendations.md)、および [Log Analytics を使用した詳細情報](how-to-security-data-access.md)を確認します。

## <a name="next-steps"></a>次のステップ

- [ソリューションを構成する](quickstart-configure-your-solution.md)
- [セキュリティ モジュールを作成する](quickstart-create-security-twin.md)
- [カスタム アラートを構成する](quickstart-create-custom-alerts.md)
- [セキュリティ エージェントをデプロイする](how-to-deploy-agent.md)
