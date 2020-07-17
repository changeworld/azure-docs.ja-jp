---
title: セキュリティ エージェントの概要
description: Iot デバイスでの Azure Security Center for IoT セキュリティ サービス エージェントの理解、構成、デプロイ、および使用を開始します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0d4d21db5185e4564666e526b3edb5ca6d3451e0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310649"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Azure Security Center for IoT デバイス セキュリティ エージェントの使用を開始する

Azure Security Center for IoT セキュリティ エージェントでは、リモート接続、アクティブ アプリケーション、ログイン イベント、およびオペレーティング システム構成のベスト プラクティスの監視など、強化されたセキュリティ機能が提供されています。 1つのサービスで、デバイス フィールドの脅威保護とセキュリティ体制を制御します。

Linux および Windows のセキュリティ エージェントの参照アーキテクチャは C# と C の両方で提供されています。

Azure Security Center for IoT セキュリティ エージェントは、デバイスのオペレーティング システムからの未加工イベント収集、コストを削減するためのイベント集計、およびデバイス モジュール ツインを使用した構成を処理します。 セキュリティー メッセージは、IoT Hub を介して Azure Security Center for IoT 分析サービスへ送信されます。

次のワークフローを使用して、Azure Security Center for IoT セキュリティ エージェントをデプロイしてテストします。

1. [IoT ハブで Azure Security Center for IoT サービスを有効にする](quickstart-onboard-iot-hub.md)
1. IoT Hub に登録済みデバイスがない場合は、[新しいデバイスを登録](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy)します。
1. デバイス用の [azureiotsecurity セキュリティ モジュール](quickstart-create-security-twin.md)を作成します。
1. 実際のデバイスにインストールするのではなく、Azure シミュレート デバイスにエージェントをインストールするには、使用可能なゾーンで[新しい Azure Virtual Machine (VM ) を作成](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)します。
1. お使いの IoT デバイス、または新しい VM に [Azure Security Center for IoT セキュリティ エージェントをデプロイ](how-to-deploy-linux-cs.md)します。
1. 無害な攻撃シミュレーションを実行するには、[trigger_events](https://aka.ms/iot-security-github-trigger-events) の手順に従います。
1. 前の手順のシミュレートした攻撃への応答で Azure Security Center for IoT アラートを確認します。 スクリプトを実行した 5 分後に確認を開始してください。
1. IoT Hub を使用して、[アラート](concept-security-alerts.md)、[推奨事項](concept-recommendations.md)、および [Log Analytics を使用した詳細情報](how-to-security-data-access.md)を確認します。

## <a name="next-steps"></a>次のステップ

- [ソリューションを構成する](quickstart-configure-your-solution.md)
- [セキュリティ モジュールを作成する](quickstart-create-security-twin.md)
- [カスタム アラートを構成する](quickstart-create-custom-alerts.md)
- [セキュリティ エージェントをデプロイする](how-to-deploy-agent.md)
