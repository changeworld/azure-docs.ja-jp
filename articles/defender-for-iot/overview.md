---
title: サービスの概要
description: Defender for IoT の機能とサービスについて詳しく学習し、Azure Defender for IoT によって包括的な IoT セキュリティがどのように実現されるかを理解します。
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
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 434a977a6d8c5b2243996063f758f409c20723fb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90932647"
---
# <a name="introducing-azure-defender-for-iot"></a>Azure Defender for IoT の概要

ハイブリッド クラウド ワークロードおよび Azure IoT ソリューション全体で、セキュリティ管理を統合し、エンド ツー エンドの脅威の検出と分析を可能にします。

## <a name="secure-your-entire-iot-solution-from-iot-devices-to-azure-cloud"></a>IoT デバイスから Azure クラウドまでの IoT ソリューション全体をセキュリティで保護する

Microsoft のシームレスなエージェントレス ソリューションを選択するか、エージェント ベースの包括的なセキュリティを利用します。Azure Defender for IoT では、IoT アセット全体のすべてのデバイス、IoT Edge、およびIoT Hub に対して脅威の防止と分析が提供されます。

何十億もの新しいデバイスがインターネットに接続され、私たちの日常生活やビジネスに統合されているため、セキュリティ運用チームは、それぞれの新しい攻撃面をカバーできるよう、セキュリティ戦略を迅速に進化させる必要があります。 他のシステムと同様に、IoT ソリューションを包括的に保護するためには、実装のすべての段階において保護が必要になります。

Defender for IoT では、エッジ、オンプレミス、Azure、およびその他のクラウドで実行されるワークロード全体で、統一された可視性と制御、アダプティブな脅威に対する防護、およびインテリジェントな脅威の検出と対応を提供することで、ハイブリッド ワークロード保護が簡略化されます。

![Defender for IoT のアーキテクチャ](./media/architecture/azure-iot-security-architecture.png)

### <a name="unified-visibility-and-control"></a>統一された可視性と制御

Azure IoT ソリューションなど、オンプレミスとクラウドのすべてのワークロードのセキュリティを、統合された 1 つのビューで確認できます。 新しいデバイスをオンボードし、ワークロード (リーフ デバイス、Microsoft Edge デバイス、IoT Hub) にセキュリティ ポリシーを適用して、セキュリティ標準へのコンプライアンスとセキュリティ体制の改善を保証します。

### <a name="adaptive-threat-prevention"></a>アダプティブ脅威に対する防護

Defender for IoT を使用して、マシン、ネットワーク、および Azure サービスのセキュリティを継続的に監視します。 数百もの組み込みのセキュリティ評価から選択することも、中央の Defender for IoT Hub ダッシュボードで独自のセキュリティ評価を作成することもできます。 仮想マシン、ネットワーク、アプリ、およびデータの間で実用的な推奨事項を使用して、セキュリティ設定を最適化し、セキュリティ スコアを向上させます。 新しく追加された IoT 機能により、お使いの Azure IoT ソリューションの攻撃対象領域を削減し、攻撃を受ける前に問題を修復できるようになりました。

### <a name="intelligent-threat-detection-and-response"></a>インテリジェントな脅威検出と対応

高度な分析と Microsoft Intelligent Security Graph により、巧妙化していくサイバー攻撃に対応します。 組み込みの行動分析と機械学習により、各種攻撃やゼロデイ攻撃を特定します。 攻撃や侵害後のアクティビティがないか、IoT ソリューションを監視します。 対話型のツールと状況に応じた脅威インテリジェンスにより、デバイスの調査と修復を効率化します。

## <a name="next-steps"></a>次のステップ

この概要では、Defender for IoT の機能とサービスについて説明しました。 Defender for IoT のアーキテクチャと前提条件の詳細、および使用を開始する方法については、次の記事を参照してください。

- [アーキテクチャ](architecture.md)
- [サービスの前提条件](service-prerequisites.md)
- [作業の開始](getting-started.md)
- [ソリューションを定義する](quickstart-configure-your-solution.md)
- [Defender for IoT に関する FAQ](resources-frequently-asked-questions.md)
- [Defender for IoT のアラート](concept-security-alerts.md)
