---
title: Azure インフラストラクチャの可用性
description: この記事では、顧客のデータの最大の可用性を提供する冗長性のレベルについて説明します。
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 18c6b87c9926b93eec26cca4028a38e472912c46
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902579"
---
# <a name="azure-infrastructure-availability"></a>Azure インフラストラクチャの可用性
Azure は、仮想化テクノロジで実現される広範囲の冗長性に基づいた堅牢な可用性を提供します。 Azure には、顧客のデータの最大の可用性を提供するさまざまなレベルの冗長性が用意されています。

## <a name="temporary-outages-and-natural-disaster"></a>一時的な停止および自然災害
Microsoft Cloud Infrastructure and Operations チームは、クラウド インフラストラクチャを設計、構築、運用、およびセキュリティ保護します。 このチームは、Azure インフラストラクチャによって、高可用性と信頼性、高い効率、スマート スケーラビリティ、およびセキュリティ保護された、プライベートで、かつ信頼されるクラウドが確実に提供されるようにします。

無停電電源装置とバッテリーの広大なバンクにより、短期間の停電が発生した場合でも、電気が途切れないことが保証されます。

非常発電機は、長時間にわたる停止や計画メンテナンスのためのバックアップ電源を提供します。 自然災害が発生した場合、データセンターはオンサイトの予備燃料で運用されます。

高速で堅牢な光ファイバ ネットワークは、データセンターを他の主要なハブやインターネット ユーザーと接続します。 コンピューティング ノードは、エンド ユーザーに近いワークロードをホストすることによって待機時間を短縮し、geo 冗長性を提供し、さらに全体的なサービスの回復性を向上させます。 エンジニアのチームは 24 時間休みなしで作業して、顧客がサービスを永続的に使用できることを保証します。

Microsoft は、高度な監視およびインシデント対応、サービスのサポート、およびバックアップ フェールオーバー機能を通して高可用性を確保します。 地理的に分散した Microsoft オペレーション センターは、365 日 24 時間体制で運用しています。 Azure ネットワークは、世界最大のネットワークの 1 つです。 光ファイバおよびコンテンツ配信ネットワークは、データセンターとエッジ ノードを接続して高パフォーマンスと信頼性を確保します。

## <a name="disaster-recovery"></a>ディザスター リカバリー
Azure は、顧客データを 2 つの場所で持続性のある状態に保つと共に、顧客がバックアップ サイトの場所を選択できるようにします。 Azure は常に、両方の場所に顧客データの複数 (3 つ) の正常なレプリカを保持します。

## <a name="database-availability"></a>データベースの可用性
Azure では、データベースがインターネット ゲートウェイ経由でインターネットにアクセスでき、それによりデータベースの可用性が持続されることが保証されます。 監視では、アクティブなデータベースの正常性と状態を 5 分の時間間隔で評価します。

## <a name="storage-availability"></a>ストレージの可用性
Azure は、高度にスケーラブルな、持続性のあるストレージ サービスを通してストレージを提供します。これにより、接続エンドポイントが提供されるため、消費しているアプリケーションからそのストレージに直接アクセスできるようになります。 ストレージ サービス経由で受信したストレージ要求は、トランザクション整合性により効率的に処理されます。

## <a name="next-steps"></a>次の手順
Microsoft が Azure インフラストラクチャをセキュリティ保護するために行っていることの詳細については、次を参照してください。

- [Azure ファシリティ、プレミス、および物理的なセキュリティ](azure-physical-security.md)
- [Azure 情報システムのコンポーネントと境界](azure-infrastructure-components.md)
- [Azure ネットワーク アーキテクチャ](azure-infrastructure-network.md)
- [Azure 運用ネットワーク](azure-production-network.md)
- [Microsoft Azure SQL Database のセキュリティ機能](azure-infrastructure-sql.md)
- [Azure の運用操作と管理](azure-infrastructure-operations.md)
- [Azure インフラストラクチャの監視](azure-infrastructure-monitoring.md)
- [Azure インフラストラクチャの整合性](azure-infrastructure-integrity.md)
- [Azure での顧客データの保護](azure-protection-of-customer-data.md)
