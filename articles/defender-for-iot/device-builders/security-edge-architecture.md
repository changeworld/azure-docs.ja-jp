---
title: IoT Edge 用の Defender for IoT azureiotsecurity
description: IoT Edge 用の Microsoft Defender for IoT azureiotsecurity のアーキテクチャと機能を理解します。
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 55681d731193e0f73a9fb21b415b03d4411ee6fe
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318961"
---
# <a name="microsoft-defender-for-iot-edge-azureiotsecurity"></a>Microsoft Defender for IoT Edge azureiotsecurity

[Azure IoT Edge](../../iot-edge/index.yml) では、エッジでのビジネス ワークフローを管理および実行するための強力な機能が提供されます。 IoT Edge は IoT 環境内で重要な役割を果たしているため、悪意のあるアクターの注意を特に引きます。

Defender for IoT azureiotsecurity により、IoT Edge デバイスの包括的なセキュリティ ソリューションが提供されます。 Defender for IoT モジュールでは、オペレーティング システムおよびコンテナー システムから未加工のセキュリティ データが収集、集約、分析されて、実行可能なセキュリティ推奨事項とアラートに変換されます。

IoT デバイスに対する Defender for IoT セキュリティ エージェントと同様に、Defender for IoT Edge モジュールもそのモジュール ツインによって高度なカスタマイズが可能です。 詳しくは、[エージェントの構成](how-to-agent-configuration.md)に関する記事をご覧ください。

IoT Edge 用の Defender for IoT azureiotsecurity では、次の機能が提供されます。

- 基になるオペレーティング システム (Linux) および IoT Edge コンテナー システムから、生のセキュリティ イベントを収集します。

  使用可能なセキュリティ データ コレクターの詳細については、[Defender for IoT エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。

- IoT Edge のデプロイ マニフェストを分析します。

- [IoT Edge ハブ](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)経由で送信されるメッセージに、未加工のセキュリティ イベントを集約します。

- azureiotsecurity ツインを使用して構成を削除します。

  詳細については、[Defender for IoT エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。

IoT Edge 用の Defender for IoT azureiotsecurity は、IoT Edge において特権モードで実行されます。 モジュールでオペレーティング システムおよび他の IoT Edge モジュールを監視できるためには、特権モードが必要です。

## <a name="module-supported-platforms"></a>モジュールのサポート対象プラットフォーム

現在、IoT Edge 用の Defender for IoT azureiotsecurity は、Linux でのみ利用できます。

## <a name="next-steps"></a>次のステップ

この記事では、IoT Edge 用の Defender for IoT azureiotsecurity のアーキテクチャと機能について説明しました。

Defender for IoT のデプロイの概要について引き続き学習するには、次の記事を参照してください。

- [IoT Edge 用の azureiotsecurity](how-to-deploy-edge.md) をデプロイする
- [Defender-IoT-micro-agent を構成する](how-to-agent-configuration.md)方法を学習する
- [IoT Hub で Defender for IoT サービスを有効にする](quickstart-onboard-iot-hub.md)方法について確認する
- [Defender for IoT についてよく寄せられる質問](resources-agent-frequently-asked-questions.md)からサービスの詳細について確認する
