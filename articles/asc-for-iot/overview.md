---
title: Azure Security Center for IoT とは | Microsoft Docs
description: Azure Security Center for IoT の機能とサービスについて説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: fdccf486cacabda4f363f4629a4d6daa4a00c8e4
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597158"
---
# <a name="introducing-azure-security-center-for-iot"></a>Azure Security Center for IoT の概要

ハイブリッド クラウド ワークロードおよび Azure IoT ソリューション全体で、セキュリティ管理を統合し、エンド ツー エンドの脅威の検出と分析を可能にします。 

## <a name="protect-iot-and-hybrid-cloud-workloads-against-evolving-cyber-attacks"></a>進化するサイバー攻撃から IoT およびハイブリッド クラウド ワークロードを保護する

モノのインターネットにより、私たちの世界は急速に変化しています。 接続されたデバイスやバックエンド システムから分析情報を得ることができ、こうした情報を、組織が経営や事業の実績をこれまでにないレベルに引き上げるのに役立つ、強力なアプリケーションやサービスに作り替えることができます。 ただし、何十億もの新しいデバイスがインターネットに接続され、私たちの日常生活やビジネスに統合されているため、セキュリティ運用チームは、これらの新しい攻撃面をカバーできるよう、セキュリティ戦略を迅速に進化させる必要があります。 他のシステムと同様に、IoT ソリューションをセキュリティで保護するには、実装の各段階での保護が必要です。 

Azure Security Center for IoT は、エッジ、オンプレミス、Azure、その他のクラウド上で実行されているワークロードにまたがる統一された可視性と制御、アダプティブな脅威の防止、およびインテリジェントな脅威の検出と対応を提供することによってハイブリッド ワークロード保護を簡素化します。 

![Azure Security Center for IoT のアーキテクチャ](./media/architecture/azure-iot-security-architecture.png)

### <a name="unified-visibility-and-control"></a>統一された可視性と制御

Azure IoT ソリューションなど、オンプレミスとクラウドのすべてのワークロードのセキュリティを、統合された 1 つのビューで確認できます。 新しいデバイスをオンボードし、ワークロード (リーフ デバイス、Microsoft Edge デバイス、IoT Hub) にセキュリティ ポリシーを適用して、セキュリティ標準へのコンプライアンスとセキュリティ体制の改善を保証します。

### <a name="adaptive-threat-prevention"></a>アダプティブ脅威に対する防護

Azure Security Center for IoT を使用して、マシン、ネットワーク、および Azure サービスのセキュリティを継続的に監視します。 数百もの組み込みのセキュリティ評価から選択することも、中央の Azure Security Center for IoT Hub ダッシュボードで独自のセキュリティ評価を作成することもできます。 仮想マシン、ネットワーク、アプリ、およびデータの間で実用的な推奨事項を使用して、セキュリティ設定を最適化し、セキュリティ スコアを向上させます。 新しく追加された IoT 機能により、お使いの Azure IoT ソリューションの攻撃対象領域を削減し、攻撃を受ける前に問題を修復できるようになりました。

### <a name="intelligent-threat-detection-and-response"></a>インテリジェントな脅威検出と対応

高度な分析と Microsoft Intelligent Security Graph により、巧妙化していくサイバー攻撃に対応します。 組み込みの行動分析と機械学習により、各種攻撃やゼロデイ攻撃を特定します。 攻撃や侵害後のアクティビティがないか、IoT ソリューションを監視します。 対話型のツールと状況に応じた脅威インテリジェンスにより、デバイスの調査と修復を効率化します。

## <a name="next-steps"></a>次の手順

この概要では、Azure Security Center for IoT の機能とサービスについて説明しました。 Azure Security Center for IoT のアーキテクチャと前提条件の詳細、および使用を開始する方法については、次の記事を参照してください。

- [アーキテクチャ](architecture.md)
- [サービスの前提条件](service-prerequisites.md)
- [使用の開始](getting-started.md)
- [ソリューションを定義する](quickstart-configure-your-solution.md)
- [Azure Security Center for IoT の FAQ](resources-frequently-asked-questions.md)
- [Azure Security Center for IoT のアラート](concept-security-alerts.md)

