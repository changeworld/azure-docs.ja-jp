---
title: 組織のサービス概要
description: Defender for IoT の機能とサービスについて詳しく学習し、Defender for IoT によって包括的な IoT セキュリティがどのように実現されるかを理解します。
ms.topic: overview
ms.date: 05/27/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: de58ffcc0f9097bd1e0b3fa4c1fac714b33d59e6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131011215"
---
# <a name="welcome-to-azure-defender-for-iot-for-organizations"></a>Azure Defender for IoT for organizations へようこそ

[!INCLUDE [Banner for top of topics](../includes/banner.md)]

運用テクノロジ (OT) ネットワークは、社会の最も重要な側面の多くを支えています。 しかし、これらのテクノロジの多くはセキュリティを考慮して設計されたものではなく、従来の IT セキュリティ制御では保護できません。 一方、モノのインターネット (IoT) により、接続された数十億のデバイスによるイノベーションの新しい波が実現されていますが、攻撃対象領域とリスクが増加しています。  

Azure Defender for IoT は、IoT/OT デバイス、脆弱性、脅威を特定するための統合セキュリティ ソリューションです。 これを使用すれば、既存の IoT/OT デバイスを保護する必要があるか、新しい IoT イノベーションにセキュリティを組み込む必要があるかにかかわらず、IoT/OT 環境全体をセキュリティで保護することができます。  

Azure Defender for IoT には、お使いの環境のニーズに適合するように、2 つの機能セットが用意されています。

IoT/OT 環境を使用するエンドユーザー組織の場合、Azure Defender for IoT では、次のようなエージェントレスのネットワーク層監視がもたらされます。

- 迅速にデプロイできる。
- さまざまな産業機器や SOC ツールと簡単に統合する。
- IoT/OT ネットワークのパフォーマンスや安定性に影響を与えない。 

このプラットフォームは、完全にオンプレミスでデプロイすることも、Azure に接続されたハイブリッド環境にデプロイすることもできます。  

IoT デバイス ビルダーにとっては、Azure Defender for IoT により、Linux や RTO などの標準的な IoT オペレーティング システムをサポートする軽量のマイクロ エージェントも得られます。 この軽量エージェントを使用すると、エッジからクラウドへの IoT/OT イニシアチブにセキュリティを確実に組み込むことができます。 柔軟でカスタマイズ可能なデプロイのためのソース コードが含まれています。 

## <a name="agentless-solution"></a>エージェントなしソリューション

以前の IoT デバイスと OT デバイスでは、エージェントがサポートされておらず、多くの場合、パッチが未適用で、正しく構成されておらず、IT チームから見えていません。 このような特質のために、これらのデバイスは、企業ネットワークに深く入り込もうとする脅威アクターにとって格好の標的になっています。 

企業の IT ネットワーク用に開発された従来のネットワーク セキュリティ監視ツールには、IoT および OT の環境で検出された特殊なプロトコル、デバイス、マシン間 (M2M) の動作に関する認識が十分ではないため、これらの環境に対処することができません。 

Azure Defender for IoT のエージェントレス監視機能を使用すれば、これらのネットワークを可視でき、セキュリティで保護できます。 したがって、これらの環境の主要な問題に対処することができます。 

### <a name="automatic-device-discovery"></a>自動デバイス検出  

パッシブでエージェントレスのネットワーク監視を使用すると、IoT/OT ネットワークに影響を与えずに、すべての IoT/OT デバイスの完全なインベントリ、詳細、および通信方法を取得できます。  

### <a name="proactive-visibility-into-risk-and-vulnerabilities"></a>リスクと脆弱性に対するプロアクティブな可視性
 
IoT/OT 環境のリスクと脆弱性を特定します。 たとえば、パッチ未適用のデバイス、開いているポート、未認可のアプリケーション、および未認可の接続を特定します。 デバイス構成、PLC コード、ファームウェアに対する変更を特定することもできます。 

### <a name="iotot-threat-detection"></a>IoT/OT の脅威検出  

特殊な IoT/OT 対応の脅威インテリジェンスと行動分析によって、異常なアクティビティや未認可のアクティビティを検出します。 また、ゼロデイ マルウェア、ファイルレス マルウェア、現地調達型戦術など、静的な IOC では見逃されていた高度な脅威を検出することもできます。 

### <a name="unified-security-management-across-iotot"></a>IoT/OT にわたる統合セキュリティ管理

組織全体の鳥瞰図を表示するために Azure Sentinel に統合します。 Splunk、IBM QRadar、ServiceNow などのサードパーティ製ツールを含む既存のワークフローと一体化した統合 IoT/OT セキュリティ ガバナンスを実装します。 

## <a name="see-also"></a>関連項目

[Azure Defender for IoT のアーキテクチャ](architecture.md)
