---
title: Azure Defender for IoT
description: Azure Defender for IoT について学習します
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 67bb01092cb6171ae56b8ab8b308e0c2ea3bece0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90931582"
---
# <a name="introduction-to-azure-defender-for-iot"></a>Azure Defender for IoT の概要

ハイブリッド クラウド ワークロードおよび Azure IoT ソリューション全体で、セキュリティ管理を統合し、エンド ツー エンドの脅威の検出と分析を可能にします。

Azure Defender for IoT によって、次のものが提供されます。

- **資産の検出とネットワーク マッピング**。デバイスの製造元、デバイスの種類、ネットワークでのデバイスの通信方法などの詳細を含む。
- **脆弱性管理**。CVE、開いているポート、承認されていないインターネット接続に関する情報を含む。
- **継続的な脅威監視**。対象となる攻撃やマルウェアなどの異常な、または未承認のアクティビティを示すリアルタイムのアラートがあります。

詳細については、[専用のドキュメント](https://docs.microsoft.com/azure/asc-for-iot/overview)を参照してください。

## <a name="availability"></a>可用性
|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|[Azure Defender](security-center-pricing.md) が必要|
|必要なロールとアクセス許可:|コンピューターの NSG の書き込みアクセス許可|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>Azure Defender for IoT のセキュリティでどのようなデバイスが保護されますか?
Security Center、Azure Defender、CyberX エージェントレス テクノロジの機能を組み合わせることにより、以下をセキュリティで保護することができます。

- IoT Hub 経由で接続されている**未開発デバイス**。 これにより、組織は、最新のインターネット ネイティブ デバイスと従来の ICS/SCADA デバイスの両方を単一の統合ソリューションでセキュリティ保護することで、IoT イニシアチブを加速させることができます。
    - 新しいデバイスをオンボードし、ワークロード (リーフ デバイス、Microsoft Edge デバイス、IoT Hub) にセキュリティ ポリシーを適用して、セキュリティ標準へのコンプライアンスとセキュリティ体制の改善を保証します。

- 製造、管理システムの構築 (BMS)、生命科学、公共企業 (エネルギー、水道業)、石油とガス、物流などの運用テクノロジ (OT) 環境で使用される、**管理されていない利用されなくなったデバイス**。 
    - 管理されていないデバイスを保護するために、Azure Defender for IoT では、OT 環境のパフォーマンスに影響を与えないパッシブで非干渉型のネットワーク トラフィック分析 (NTA) にオンプレミスのセンサーを使用します。 
    - また、ルールや署名を構成する必要がないように、(特許取得済みの IoT または OT 対応の動作分析と機械学習と組み合わせた) 特化された IoT または OT プロトコルの詳細な知識も組み込まれます。日や週単位ではなく、分または時間単位の標準的なデプロイになります。 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>Azure Defender for IoT と Azure Sentinel の統合
統一された IT または OT セキュリティの監視とガバナンスを有効にするために、Azure Defender for IoT は [Azure Sentinel](../sentinel/overview.md) とネイティブに統合されます。

SecOps チームは次のことができます。

- Sentinel に含まれる OT 固有の SOAR プレイブックを使用して、IoT または OT の脅威を迅速に検出し、対応する
- Section 52 (Microsoft の社内の IoT または OT の脅威インテリジェンス チーム) によって提供される継続的に更新された IoT または OT 固有の脅威インテリジェンスのメリット
- Azure Defender for IoT と既存の SOC ワークフローおよびサード パーティのセキュリティ ツール (Splunk、IBM QRadar、ServiceNow など) を統合する


## <a name="next-steps"></a>次の手順

この記事では、Azure Security Center での Azure Defender for IoT について学習しました。 詳細については、次を参照してください。

- [Azure Security Center for IoT の概要](../asc-for-iot/overview.md)
