---
title: Azure RTOS 用のセキュリティ モジュールの概要
description: Azure RTOS 用のセキュリティ モジュールのサポートと、Defender for IoT サービスの一部としての実装について説明します
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
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: 22bd12bbdcccef2fd0e9010f926cd18e95d42967
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761857"
---
# <a name="overview-defender-for-iot-security-module-for-azure-rtos-preview"></a>概要:Azure RTOS 用の Defender for IoT セキュリティ モジュール (プレビュー)

RTOS 用の Defender for IoT セキュリティ モジュールでは、Azure RTOS デバイス向けの包括的なセキュリティ ソリューションが提供されます。 現在、Azure RTOS には Azure IoT セキュリティ モジュールが組み込みで付属し、リアルタイムのオペレーティング システム デバイスに関する一般的な脅威と潜在的な悪意のあるアクティビティへの対応が提供されるようになっています。 

![Defender for IoT Azure RTOS](./media/architecture/azure-rtos-security-monitoring.png)


Azure RTOS 用のセキュリティ モジュールには、次の機能があります。 
- 悪意のあるネットワーク アクティビティの検出
- カスタム アラート ベースのデバイス動作基準
- デバイス セキュリティの検疫の強化

## <a name="detection-of-malicious-network-activities"></a>悪意のあるネットワーク アクティビティの検出

各デバイスの受信および送信ネットワーク アクティビティが監視されます (サポートされるプロトコル:IPv4 および IPv6 上の TCP、UDP、ICMP)。 Defender for IoT では、これらのネットワーク アクティビティのそれぞれが Microsoft 脅威インテリジェンス フィードに対して検査されます。 このフィードは、世界中で収集される数百万の固有の脅威インジケーターによってリアルタイムに更新されます。 

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>カスタム アラートに基づくデバイス動作基準

基準を使用すると、デバイスのセキュリティ グループへのクラスタリングと、各グループの想定される動作の定義が可能です。 IoT デバイスは、通常、明確に定義された限定的なシナリオで動作するように設計されているため、一連のパラメーターを使用して、想定される動作を定義するベースラインを作成することは容易です。 ベースラインからの逸脱があれば、アラートがトリガーされます。 

## <a name="improve-your-device-security-hygiene"></a>デバイス セキュリティの検疫を強化する

Defender for IoT によって提供される推奨インフラストラクチャを活用することで、デバイスのセキュリティ態勢に影響や損害を与える環境内の問題について、知識や洞察を得てください。 セキュリティの強度は常に組織内の最弱の輪によって測定されるため、IoT デバイスのセキュリティ態勢に問題があるときに、それが修正されないままになっていると、潜在的な攻撃の成功を許す可能性があります。 

## <a name="get-started-protecting-azure-rtos-devices"></a>Azure RTOS デバイスの保護を開始する

Azure RTOS 用のセキュリティ モジュールは、デバイス向けの無料ダウンロードとして提供されています。 Defender for IoT クラウド サービスは、Azure サブスクリプションごとに 30 日間の試用版で利用できます。 開始するには、[Azure RTOS 用のセキュリティ モジュール](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md)をダウンロードしてください。 


## <a name="next-steps"></a>次のステップ

この記事では、Azure RTOS 用のセキュリティ モジュール サービスについて学習しました。 セキュリティ モジュールの詳細と開始方法については、次の記事を参照してください。

- [Azure RTOS IoT セキュリティ モジュールの概念](concept-rtos-security-module.md)
- [クイック スタート: Azure RTOS IoT セキュリティ モジュール](quickstart-azure-rtos-security-module.md)
