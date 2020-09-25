---
title: Azure RTOS 用のセキュリティ モジュールの概要
description: Azure RTOS 用のセキュリティ モジュールのサポートと、Azure Security Center for IoT サービスの一部としての実装について説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: bcf98f503b0f0c8bf76435d9884297d5f53d65e6
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514477"
---
# <a name="overview-security-module-for-azure-rtos-preview"></a>概要:Azure RTOS 用のセキュリティ モジュール (プレビュー)

Azure Security Center for IoT RTOS セキュリティ モジュールでは、Azure RTOS デバイス向けの包括的なセキュリティ ソリューションが提供されます。 Azure RTOS は、Azure IoT セキュリティ モジュールが組み込まれた状態でリリースされるようになりました。また、リアルタイムのオペレーティング システム デバイス上の一般的な脅威と潜在的な悪意のあるアクティビティに関する情報が提供されます。 

![Azure Security Center for IoT と Azure RTOS](./media/architecture/azure-rtos-security-monitoring.png)


Azure RTOS 用のセキュリティ モジュールには、次の機能があります。 
- 悪意のあるネットワーク アクティビティの検出
- カスタム アラート ベースのデバイス動作基準
- デバイス セキュリティの検疫の強化

### <a name="detection-of-malicious-network-activities"></a>悪意のあるネットワーク アクティビティの検出

各デバイスの受信および送信ネットワーク アクティビティが監視されます (サポートされるプロトコル:IPv4 および IPv6 上の TCP、UDP、ICMP)。 Azure Security Center for IoT は、これらのネットワーク アクティビティのそれぞれを、Microsoft 脅威インテリジェンスのフィードと比較して検査します。 このフィードは、世界中で収集される数百万の固有の脅威インジケーターによってリアルタイムに更新されます。 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>カスタム アラートに基づくデバイス動作基準

基準を使用すると、デバイスのセキュリティ グループへのクラスタリングと、各グループの想定される動作の定義が可能です。 IoT デバイスは、通常、明確に定義された限定的なシナリオで動作するように設計されているため、一連のパラメーターを使用して、想定される動作を定義するベースラインを作成することは容易です。 ベースラインからの逸脱があれば、アラートがトリガーされます。 

### <a name="improve-your-device-security-hygiene"></a>デバイス セキュリティの検疫を強化する

Azure Security Center for IoT によって提供される推奨インフラストラクチャを活用することで、デバイスのセキュリティ体制に影響や損害を与える環境内の問題について、知識や洞察を得てください。 どのような組織であってもセキュリティの強度は常に最も弱い輪によって決まるため、IoT デバイスのセキュリティ体制に問題があり、修正されないままになっていると、考えられる攻撃が成功するおそれがあります。 

## <a name="get-started-protecting-azure-rtos-devices"></a>Azure RTOS デバイスの保護を開始する

Azure RTOS 用のセキュリティ モジュールは、デバイス用の無料ダウンロードとして提供されています。 Azure Security Center for IoT クラウド サービスは、Azure サブスクリプションごとに 30 日間の試用版で利用できます。 まず [Azure RTOS 用のセキュリティ モジュール](https://github.com/azure-rtos/iot-security-module-preview)をダウンロードしてください。 


## <a name="next-steps"></a>次のステップ

この記事では、Azure RTOS 用のセキュリティ モジュール サービスについて学習しました。 セキュリティ モジュールの詳細と開始方法については、次の記事を参照してください。

- [Azure RTOS IoT セキュリティ モジュールの概念](concept-rtos-security-module.md)
- [クイック スタート: Azure RTOS IoT セキュリティ モジュール](quickstart-azure-rtos-security-module.md)


