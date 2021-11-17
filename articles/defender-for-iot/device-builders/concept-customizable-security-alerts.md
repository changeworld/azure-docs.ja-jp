---
title: IoT Hub のカスタム セキュリティ アラート
description: Defender for IoT Hub の機能とサービスを使用したカスタマイズ可能なセキュリティ アラートと推奨される修復方法について説明します。
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: e91587df3dbfb50700f95eaefea3bf80c2e18e04
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249725"
---
# <a name="defender-for-iot-hub-custom-security-alerts"></a>Defender for IoT Hub のカスタム セキュリティ アラート

Defender for IoT では、高度な分析と脅威インテリジェンスを使用して IoT ソリューションを継続的に分析し、悪意のあるアクティビティに関するアラートを受け取ることができます。

デバイスの予想される各動作の知識に基づいて、カスタム アラートを作成します。 これらのアラートは、組織のデプロイまたはランドスケープに対して可能性のある変更の最も効率的な指標として機能します。

以下の各一覧に示されている Defender for IoT アラートは、IoT Hub で予想される動作に基づいてユーザーが定義できます。 各アラートをカスタマイズする方法の詳細については、「[カスタム アラートの作成](quickstart-create-custom-alerts.md)」を参照してください。

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>IoT Hub の組み込みカスタム アラート

### <a name="low-severity"></a>重大度: 低

| アラート名 | 重大度 | データ ソース | 説明 | アラートの種類 |
|--|--|--|--|--|
| カスタム アラート - AMQP プロトコルによるクラウドからデバイスへのメッセージの数が、許容範囲外である | 低 | IoT Hub | 特定の時間枠内におけるクラウドからデバイスへのメッセージ (AMQP プロトコル) の数が、現在の構成の許容範囲外です。 | IoT_CA_AmqpC2DMessagesNotInAllowedRange |
| カスタム アラート - AMQP プロトコルによる、拒否されたクラウドからデバイスへのメッセージの数が、許容範囲外である | 低 | IoT Hub | 特定の時間枠内においてデバイスによって拒否された、クラウドからデバイスへのメッセージ (AMQP プロトコル) の数が、現在の構成の許容範囲外です。 | IoT_CA_AmqpC2DRejectedMessagesNotInAllowedRange |
| カスタム アラート - AMQP プロトコルによるデバイスからクラウドへのメッセージの数が、許容範囲外である | 低 | IoT Hub | 特定の時間枠内におけるデバイスからクラウドへのメッセージ (AMQP プロトコル) の量が、現在の構成の許容範囲外です。 | IoT_CA_AmqpD2CMessagesNotInAllowedRange |
| カスタム アラート - ダイレクト メソッド呼び出しの回数が許容範囲外である | 低 | IoT Hub | 特定の時間枠内におけるダイレクト メソッド呼び出しの量が、現在の構成の許容範囲外です。 | IoT_CA_DirectMethodInvokesNotInAllowedRange |
| カスタム アラート - ファイル アップロードの回数が許容範囲外である | 低 | IoT Hub | 特定の時間枠内におけるファイル アップロードの量が、現在の構成の許容範囲外です。 | IoT_CA_FileUploadsNotInAllowedRange |
| カスタム アラート - HTTP プロトコルによるクラウドからデバイスへのメッセージの数が、許容範囲外である | 低 | IoT Hub | ある時間枠内におけるクラウドからデバイスへのメッセージ (HTTP プロトコル) の量が、構成された許容範囲内にありません | IoT_CA_HttpC2DMessagesNotInAllowedRange |
| カスタム アラート - HTTP プロトコルによる、拒否されたクラウドからデバイスへのメッセージの数が、許容範囲内にない | 低 | IoT Hub | 特定の時間枠内におけるクラウドからデバイスへのメッセージ (HTTP プロトコル) の量が、現在の構成の許容範囲外です。 | IoT_CA_HttpC2DRejectedMessagesNotInAllowedRange |
| カスタム アラート - HTTP プロトコルによるデバイスからクラウドへのメッセージの数が、許容範囲外である | 低 | IoT Hub | 特定の時間枠内におけるデバイスからクラウドへのメッセージ (HTTP プロトコル) の量が、現在の構成の許容範囲外です。 | IoT_CA_HttpD2CMessagesNotInAllowedRange |
| カスタム アラート - MQTT プロトコルによるクラウドからデバイスへのメッセージの数が、許容範囲外である | 低 | IoT Hub | 特定の時間枠内におけるクラウドからデバイスへのメッセージ (MQTT プロトコル) の量が、現在の構成の許容範囲外です。 | IoT_CA_MqttC2DMessagesNotInAllowedRange |
| カスタム アラート - MQTT プロトコルによる、拒否されたクラウドからデバイスへのメッセージの数が、許容範囲外である | 低 | IoT Hub | 特定の時間枠内においてデバイスから拒否された、クラウドからデバイスへのメッセージ (MQTT プロトコル) の量が、現在の構成の許容範囲外です。 | IoT_CA_MqttC2DRejectedMessagesNotInAllowedRange |
| カスタム アラート - MQTT プロトコルによるデバイスからクラウドへのメッセージの数が、許容範囲外である | 低 | IoT Hub | 特定の時間枠内におけるデバイスからクラウドへのメッセージ (MQTT プロトコル) の量が、現在の構成の許容範囲外です。 | IoT_CA_MqttD2CMessagesNotInAllowedRange |
| カスタム アラート - 許容範囲外であるコマンド キューの消去の回数 | 低 | IoT Hub | 特定の時間枠内におけるコマンド キューの消去の量が、現在の構成の許容範囲外です。 | IoT_CA_QueuePurgesNotInAllowedRange |
| カスタム アラート - モジュール ツインの更新の回数が許容範囲外である | 低 | IoT Hub | 特定の時間枠内におけるモジュール ツインの更新の量が、現在の構成の許容範囲外です。 | IoT_CA_TwinUpdatesNotInAllowedRange |
| カスタム アラート - 未承認の操作の回数が許容範囲外である | 低 | IoT Hub | 特定の時間枠内における未承認の操作の量が、現在の構成の許容範囲外です。 | IoT_CA_UnauthorizedOperationsNotInAllowedRange |

## <a name="next-steps"></a>次のステップ

- [アラートをカスタマイズする](quickstart-create-custom-alerts.md)方法を学ぶ
- Defender for IoT サービスの[概要](overview.md)
