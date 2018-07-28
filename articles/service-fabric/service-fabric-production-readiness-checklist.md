---
title: Azure Service Fabric の運用環境の準備状況チェックリスト | Microsoft Docs
description: ベスト プラクティスに従って、Service Fabric アプリケーションとクラスターの運用準備をします。
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/10/2018
ms.author: subramar
ms.openlocfilehash: 5092bf377f6ab213a2db52e01fb7b89ff0c218ce
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39127489"
---
# <a name="production-readiness-checklist"></a>運用環境の準備状況チェックリスト

お使いのアプリケーションとクラスターは、運用環境のトラフィックに対応する準備ができていますか。 アプリケーションとクラスターを実行してテストしたからといって、必ずしも運用環境に移行するための準備ができているというわけではありません。 次のチェックリストを検討して、アプリケーションとクラスターが円滑に実行されるようにします。 これらの項目すべてをチェック済みにすることを強くお勧めします。 当然ながら、特定の項目に代替ソリューションを使用することができます (独自の診断フレームワークなど)。


## <a name="pre-requisites-for-production"></a>運用環境の前提条件

1. 20 を超えるコアまたは 10 個を超えるノードを持つクラスターの場合は、システム サービス用に専用のプライマリ ノード タイプを作成します。 [配置の制約](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)を追加して、システム サービス用にプライマリ ノード タイプを予約します。 
2. プライマリ ノード タイプには D2v2 または上位の SKU を使用します。 少なくとも 50 GB のハード ディスク容量を持つ SKU を選択することをお勧めします。
2. 運用環境クラスターは、[セキュリティで保護されている](service-fabric-cluster-security.md)必要があります。 セキュリティで保護されたクラスターの設定例については、この[クラスター テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG)を参照してください。 証明書の共通名を使用します。自己署名証明書は使用しないでください。
4. [コンテナーとサービスのリソース制約](service-fabric-resource-governance.md)を追加して、ノード リソースの消費が 75% を超えないようにします。 
5. [持続性レベル](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)を理解して設定します。 ステートフル ワークロードを実行しているノード タイプには、シルバー以上の持続性レベルを設定することをお勧めします。 プライマリ ノード タイプには、持続性レベルをシルバー以上に設定する必要があります。
6. ノード タイプの[信頼性レベル](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)を理解して選択します。 シルバー以上の信頼性をお勧めします。
7. クラスターの[容量の要件](service-fabric-cluster-capacity.md)を特定するため、ワークロードのロード テストとスケール テストを実行します。 
8. アラートを使用して、サービスとアプリケーションが監視され、アプリケーション ログが生成され、格納されています。 例については、「[Service Fabric アプリケーションにログ記録を追加する](service-fabric-how-to-diagnostics-log.md)」と「[Log Analytics でコンテナーを監視](service-fabric-diagnostics-oms-containers.md)」を参照してください。
9. クラスターがアラート ([OMS](service-fabric-diagnostics-event-analysis-oms.md) など) で監視されています。 
10. 基になる仮想マシン スケール セット インフラストラクチャが、アラート ([Log Analytics](service-fabric-diagnostics-oms-agent.md) など) で監視されています。
11. ロックアウトされないように、クラスターには、常に[プライマリ証明書とセカンダリ証明書](service-fabric-cluster-security-update-certs-azure.md)があります。
12. 開発、ステージング、および運用環境用に個別のクラスターを維持します。 
13. [アプリケーションのアップグレード](service-fabric-application-upgrade.md)と[クラスターのアップグレード](service-fabric-tutorial-upgrade-cluster.md)は、最初に開発クラスターとステージング クラスターでテストされます。 
14. 自動アップグレードは、運用環境クラスターではオフにして、開発環境クラスターとステージング クラスターではオンにします (必要に応じてロールバック)。 
15. サービスの目標復旧時点 (RPO) を確立し、[ディザスター リカバリー プロセス](service-fabric-disaster-recovery.md)を設定してそれをテストします。
16. クラスターの手動またはプログラムを使用した[スケーリング](service-fabric-cluster-scaling.md)を計画します。
17. クラスター ノードへの[修正プログラムの適用](service-fabric-patch-orchestration-application.md)を計画します。 
18. 最新の変更が継続的にテストされるように、CI/CD パイプラインを確立します。 たとえば、[VSTS](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) や [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md) を使用します。
19. [Fault Analysis Service](service-fabric-testability-overview.md) と制御された[混乱](service-fabric-controlled-chaos.md)の誘発を使用して、負荷の下で開発クラスターとステージング クラスターをテストします。 
20. アプリケーションの[スケーリング](service-fabric-concepts-scalability.md)を計画します。 


Service Fabric の Reliable Services または Reliable Actors プログラミング モデルを使用している場合は、次の項目をチェック済みにする必要があります。
21. ローカル開発中にアプリケーションをアップグレードして、サービス コードが `RunAsync` メソッドでキャンセル トークンを受け入れること、およびカスタム通信リスナーを終了することを確認します。
22. Reliable Collection を使用する場合は、[よくある落とし穴](service-fabric-work-with-reliable-collections.md)を回避します。
23. ロード テストの実行時に、.NET CLR メモリ パフォーマンス カウンターを監視して、高いレートのガベージ コレクションやランナウェイ ヒープの増加を確認します。
24. [Reliable Services と Reliable Actors](service-fabric-reliable-services-backup-restore.md) のオフライン バックアップを保持して、復元プロセスをテストします。 


## <a name="optional-best-practices"></a>省略可能なベスト プラクティス

上記のリストは、運用に入るための前提条件ですが、次の項目も考慮する必要があります。
25. 組み込みの正常性評価とレポートを拡張するため、[Service Fabric の正常性モデル](service-fabric-health-introduction.md)にプラグインします。
26. [リソース分散](service-fabric-cluster-resource-manager-balancing.md)のためにアプリケーションとレポートの[負荷](service-fabric-cluster-resource-manager-metrics.md)を監視しているカスタム ウォッチドッグをデプロイします。 


## <a name="next-steps"></a>次の手順
* [Service Fabric Windows クラスターをデプロイする](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Service Fabric Linux クラスターをデプロイする](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Service Fabric の[アプリケーション ライフサイクル](service-fabric-application-lifecycle.md)について確認します。
