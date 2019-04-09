---
title: ASC for IoT のセキュリティ モジュール ツインについて (プレビュー) | Microsoft Docs
description: セキュリティ モジュール ツインの概念と ASC for IoT での使用する方法について説明します。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 63d21cc5027145ab87030bd2561bc5087298f16c
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541525"
---
# <a name="security-module"></a>セキュリティ モジュール

> [!IMPORTANT]
> ASC for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、ASC for IoT でのデバイス ツインとモジュールの使用方法について説明します。 

## <a name="device-twins"></a>デバイス ツイン

Azure に構築された IoT ソリューションでは、デバイス ツインが、デバイスの管理とプロセスの自動化の両方で主要な役割を果たします。  

ASC for IoT は、既存の IoT デバイス管理プラットフォームと完全に統合されるため、デバイスのセキュリティ状態を管理したり、既存のデバイス制御機能を利用したりすることができます。 統合は、IoT Hub ツイン メカニズムを利用することによって実現されます。  

Azure IoT Hub の[デバイス](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) ツインの概念の詳細については、こちらをご覧ください。 

## <a name="security-module-twins"></a>セキュリティ モジュール ツイン

ASC for IoT は、サービス内の各デバイスのセキュリティ モジュール ツインを保持します。 セキュリティ モジュール ツインでは、ソリューション内の特定のデバイスのデバイス セキュリティに関連するすべての情報が保持されます。 デバイスのセキュリティのプロパティは、通信の安全性を高め、必要なリソースが少なくて済む更新とメンテナンスを可能にするために、専用のセキュリティ モジュール ツインに保持されます。  

ツインの作成、カスタマイズ、および構成方法の詳細については、[セキュリティ モジュール ツインの作成](quickstart-create-security-twin.md)および[セキュリティ エージェントの構成](concept-agent-configuration.md)に関するページを参照してください。 IoT Hub でのモジュール ツインの概念の詳細については、[モジュール ツインの理解](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)に関するページを参照してください。 
 

## <a name="see-also"></a>関連項目
- [ASC for IoT (プレビュー)](overview.md)
- [セキュリティ エージェントをデプロイする](select-deploy-agent.md)
- [セキュリティ エージェントの認証方法](concept-security-agent-authentication-methods.md)