---
title: Azure Service Fabric のアプリケーションとクラスターに関するベスト プラクティス
description: Azure Service Fabric を使用してクラスター、アプリ、およびサービスを管理するためのベスト プラクティスと設計上の考慮事項。
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551779"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Azure Service Fabric のアプリケーションとクラスターに関するベスト プラクティス

この記事では、Azure Service Fabric のアプリケーションとクラスターの管理に関するベスト プラクティスへのリンクを提供します。 運用環境の信頼性を最適化するために、これらのプラクティスを実装することを強くお勧めします。 [Service Fabric クラスター テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)のいずれかを使用して運用環境のソリューションの設計を開始するか、または既存のテンプレートを更新してこれらのプラクティスを組み込みます。

## <a name="security"></a>Security

* [セキュリティに関するベスト プラクティス](service-fabric-best-practices-security.md)

## <a name="networking"></a>ネットワーク

* [ネットワークに関するベスト プラクティス](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>コンピューティングの計画とスケーリング

* [コンピューティングのスケーリングに関するベスト プラクティス](service-fabric-best-practices-capacity-scaling.md)
* [コンピューティング能力の計画](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>コードとしてのインフラストラクチャ

* [コードとしてのインフラストラクチャの実装に関するベスト プラクティス](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>監視と診断

* [クラスターの監視と診断に関するベスト プラクティス](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>アプリケーションの設計

* [アプリケーション設計のベスト プラクティス](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>チェック リスト

前のセクションで推奨されたプラクティスを実装した後は、運用環境の準備状況チェックリストのすべてのベスト プラクティスを統合したことを確認します。
* [Azure Service Fabric の運用環境の準備状況チェックリスト](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>次のステップ

* Windows Server を実行している VM またはコンピューター上にクラスターを作成する:[Windows Server 用の Service Fabric クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)
* Linux を実行している VM またはコンピューター上にクラスターを作成する:[Linux クラスターの作成](service-fabric-cluster-creation-via-portal.md)
* Service Fabric のトラブルシューティング:[トラブルシューティング ガイド](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)