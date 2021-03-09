---
title: セキュリティ モジュールとデバイス ツイン
description: セキュリティ モジュール ツインの概念と、それらを Defender for IoT で使用する方法について説明します。
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
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: feb84a1261e37600c68d8e372162033d4021f564
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522922"
---
# <a name="security-module"></a>セキュリティ モジュール

この記事では、Defender for IoT でのデバイス ツインとモジュールの使用方法について説明します。

## <a name="device-twins"></a>デバイス ツイン

Azure に構築された IoT ソリューションでは、デバイス ツインが、デバイスの管理とプロセスの自動化の両方で主要な役割を果たします。

Defender for IoT は、既存の IoT デバイス管理プラットフォームと完全に統合されるため、デバイスのセキュリティ状態を管理したり、既存のデバイス制御機能を利用したりすることができます。 統合は、IoT Hub ツイン メカニズムを利用することによって実現されます。

Azure IoT Hub の[デバイス ツイン](../iot-hub/iot-hub-devguide-device-twins.md)の概念の詳細について学習してください。

## <a name="security-module-twins"></a>セキュリティ モジュール ツイン

Defender for IoT により、サービス内の各デバイスのセキュリティ モジュール ツインが保持されます。
セキュリティ モジュール ツインでは、ソリューション内の特定のデバイスのデバイス セキュリティに関連するすべての情報が保持されます。
デバイスのセキュリティのプロパティは、通信の安全性を高め、必要なリソースが少なくて済む更新とメンテナンスを可能にするために、専用のセキュリティ モジュール ツインに保持されます。

ツインの作成、カスタマイズ、および構成方法の詳細については、[セキュリティ モジュール ツインの作成](quickstart-create-security-twin.md)および[セキュリティ エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。 IoT Hub でのモジュール ツインの概念の詳細については、[モジュール ツインの理解](../iot-hub/iot-hub-devguide-module-twins.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [Defender for IoT の概要](overview.md)
- [セキュリティ エージェントをデプロイする](how-to-deploy-agent.md)
- [セキュリティ エージェントの認証方法](concept-security-agent-authentication-methods.md)