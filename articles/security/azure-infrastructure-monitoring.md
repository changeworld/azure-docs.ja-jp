---
title: Azure インフラストラクチャの監視
description: この記事では、Azure の運用環境ネットワークの監視について説明します。
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 17e7183ff56725462dc43cba21db418a86d86b51
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101973"
---
# <a name="monitoring-of-azure-infrastructure"></a>Azure インフラストラクチャの監視   

## <a name="configuration-and-change-management"></a>構成と変更管理
Microsoft Azure では、構成設定、およびハードウェア、ソフトウェアおよびネットワーク デバイスのベースライン構成が年 1 回審査及び更新されます。 変更がテスト環境から運用環境に導入される前に、開発、検証、および承認されていること。

Azure ベースのサービスに必要なベースライン構成は、サービス チームと Azure のセキュリティおよびコンプライアンス チームによって審査されます。 サービス チームの審査は、運用サービスを展開する前の検証作業の一環として行われます。

## <a name="vulnerability-management"></a>脆弱性の管理
セキュリティ更新管理を行うことにより、システムを既知の脆弱性から保護できます。 Azure では、統合されたデプロイ システムを使用して、Microsoft ソフトウェアのセキュリティ更新プログラムの配布とインストールを管理します。 Azure は、Microsoft セキュリティ レスポンス センター (MSRC) のリソースを利用することもできます。 MSRC は、毎日 24 時間体制で、セキュリティ インシデントおよびクラウドの脆弱性を識別、監視、対応、および解決しています。

## <a name="vulnerability-scanning"></a>脆弱性のスキャン
脆弱性のスキャンは、サーバー オペレーティング システム、データベース、ネットワーク デバイス上で実行されています。 脆弱性スキャンは、四半期に 1 回以上実行します。 Microsoft Azure は、独立した監査機関と契約して、Microsoft Azure 境界の侵入テストを実行します。 レッド チームの演習も定期的に実行され、結果はセキュリティの向上のために使用されます。

## <a name="protective-monitoring"></a>保護的監視
Microsoft Azure Security には、有効な監視の要件が定義されています。 サービス チームは、これらの要件に従って有効な監視ツールを構成します。 有効な監視ツールには、監視エージェント (MA)、System Center Operations Manager などがあります。 これらのツールは、即時の対応が必要な状況になった場合に Microsoft Azure セキュリティ担当者にタイム アラートを提供するように構成されています。

## <a name="incident-management"></a>インシデント管理
Microsoft では、インシデント発生時の対応を調整するためのセキュリティ インシデント管理プロセスを実装しています。

マイクロソフトがその装置または施設に保管されている顧客データへの不正なアクセスを検知した、装置または施設への不正なアクセスにより顧客データの損失、漏洩、または改ざんが発生した場合、マイクロソフトは以下の措置を取ります。

- セキュリティ インシデントが発生したお客様に直ちに通知する。
- 直ちにセキュリティ インシデントを調査し、それに関する詳細情報をお客様に提供する。
- セキュリティ インシデントによる影響を低減して、発生したる損害を最小限に抑えるための妥当で迅速な手続きを実行する。

役割が定義され、責任が割り当てられたインシデント管理フレームワークが確立されました。 Windows Azure Security Incident Management (WASIM) チームは、エスカレーションと、必要な場合に専門家チームの協力を得るなど、セキュリティ インシデントの管理を担当します。 Azure の運用マネージャーは、セキュリティおよびプライバシー インシデントの調査と解決を監督します。

## <a name="next-steps"></a>次の手順
Microsoft が Azure インフラストラクチャをセキュリティ保護するために行っていることの詳細については、次を参照してください。

- [Azure ファシリティ、プレミス、および物理セキュリティ](azure-physical-security.md)
- [Azure インフラストラクチャの可用性](azure-infrastructure-availability.md)
- [Azure 情報システムのコンポーネントと境界](azure-infrastructure-components.md)
- [Azure ネットワーク アーキテクチャ](azure-infrastructure-network.md)
- [Azure 運用環境ネットワーク](azure-production-network.md)
- [Microsoft Azure SQL Database のセキュリティ機能](azure-infrastructure-sql.md)
- [Azure の実稼働操作と管理](azure-infrastructure-operations.md)
- [Azure インフラストラクチャの整合性](azure-infrastructure-integrity.md)
- [Azure における顧客データの保護](azure-protection-of-customer-data.md)
