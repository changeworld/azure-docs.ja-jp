---
title: セキュリティ モジュールとデバイス ツイン
description: セキュリティ モジュール ツインの概念、およびその Azure Security Center for IoT での使用方法について説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: d598d291612c6e4f58caf77e1b213b2bc3f42820
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311440"
---
# <a name="security-module"></a>セキュリティ モジュール

この記事では、Azure Security Center for IoT でのデバイス ツインおよびモジュールの使用方法について説明します。

## <a name="device-twins"></a>デバイス ツイン

Azure に構築された IoT ソリューションでは、デバイス ツインが、デバイスの管理とプロセスの自動化の両方で主要な役割を果たします。

Azure Security Center for IoT は、既存の IoT デバイス管理プラットフォームと完全に統合されるため、デバイスのセキュリティ状態を管理したり、既存のデバイス制御機能を利用したりすることができます。 統合は、IoT Hub ツイン メカニズムを利用することによって実現されます。

Azure IoT Hub の[デバイス ツイン](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)の概念の詳細について学習してください。

## <a name="security-module-twins"></a>セキュリティ モジュール ツイン

Azure Security Center for IoT は、サービス内の各デバイスのセキュリティ モジュール ツインを保持します。
セキュリティ モジュール ツインでは、ソリューション内の特定のデバイスのデバイス セキュリティに関連するすべての情報が保持されます。
デバイスのセキュリティのプロパティは、通信の安全性を高め、必要なリソースが少なくて済む更新とメンテナンスを可能にするために、専用のセキュリティ モジュール ツインに保持されます。

ツインの作成、カスタマイズ、および構成方法の詳細については、[セキュリティ モジュール ツインの作成](quickstart-create-security-twin.md)および[セキュリティ エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。 IoT Hub でのモジュール ツインの概念の詳細については、[モジュール ツインの理解](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)に関するページを参照してください。

## <a name="see-also"></a>関連項目

- [Azure Security Center for IoT の概要](overview.md)
- [セキュリティ エージェントをデプロイする](how-to-deploy-agent.md)
- [セキュリティ エージェントの認証方法](concept-security-agent-authentication-methods.md)
