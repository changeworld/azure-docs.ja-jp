---
title: コンポーネントと前提条件
description: Azure Defender for IoT サービスの使用を開始するために必要なすべてのことに関する前提条件の詳細。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.custom: references_regions
ms.openlocfilehash: b0913dc48f807c26396a38e31d293877b4561b7d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089181"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Azure Defender for IoT の前提条件

この記事では、Defender for IoT サービスのさまざまなコンポーネント、開始するために必要なこと、およびこのサービスの理解に役立つ基本的な概念について説明します。

## <a name="minimum-requirements"></a>最小要件

- IoT および OT デバイスのエージェントレス監視 (CyberX テクノロジに基づく)
    - SPAN ポート経由のトラフィック監視をサポートするネットワーク スイッチ
    - NTA センサーのハードウェア アプライアンス。詳細については、[認定ハードウェア](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)に関するページをご覧ください
    - Azure サブスクリプションの **共同作成者** ロール (コミットされたデバイスを定義するためのオンボード時にのみ必要)
    - IoT Hub (Free または Standard レベル) の **共同作成者** ロール (クラウド接続管理用)
- Azure IoT Hub によって管理されるマネージド IoT デバイスのセキュリティ
    - IoT Hub (Standard レベル) の **共同作成者** ロール
    - IoT Hub: **Azure Defender for IoT** フィーチャー トグルを有効にする必要があります
    - デバイス レベルのセキュリティ モジュール サポートの場合  
        - Defender for IoT エージェントでは、増え続けているデバイスとプラットフォームをサポートしています。[サポートされるプラットフォームのリスト](how-to-deploy-agent.md)に関するページをご覧ください


## <a name="supported-service-regions"></a>サポートされているサービス リージョン

詳細については、[IoT Hub によりサポートされるリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)に関するページをご覧ください。 

Defender for IoT では、ヨーロッパの全リージョンからのトラフィックがすべて西ヨーロッパ リージョンのデータ センターにルーティングされ、それ以外の全リージョンからのトラフィックがすべて米国中部リージョンのデータ センターにルーティングされます。

## <a name="next-steps"></a>次のステップ

- Azure IoT セキュリティの[概要](overview.md)を読む
- [このサービスを有効にする](quickstart-onboard-iot-hub.md)方法を学習する
- [Defender for IoT の FAQ](resources-frequently-asked-questions.md) について確認する
- [Defender for IoT アラートを理解する](concept-security-alerts.md)方法を確認する
