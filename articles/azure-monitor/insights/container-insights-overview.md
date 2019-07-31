---
title: コンテナー用 Azure Monitor の概要 | Microsoft Docs
description: この記事では、AKS Container Insights ソリューションを監視するコンテナーに対する Azure Monitor と、Azure の AKS クラスターと Container Instances の正常性を監視して取得される値について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: 7d8fb7bc0e837ba78d86a39524d869c9bb01895d
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867564"
---
# <a name="azure-monitor-for-containers-overview"></a>コンテナーに対する Azure Monitor の概要

コンテナーに対する Azure Monitor は、Azure Kubernetes Service (AKS) でホストされた Azure Container Instances またはマネージド Kubernetes クラスターにデプロイされているコンテナー ワークロードのパフォーマンスを監視するために設計された機能です。 コンテナーの監視は、複数のアプリケーションを含む大規模な運用クラスターを実行するときは特に重要です。

コンテナーに対する Azure Monitor では、Kubernetes で使用可能なコントローラー、ノード、およびコンテナーから Metrics API 経由でメモリやプロセッサ メトリックを収集することにより、パフォーマンスを把握できます。 コンテナーのログも収集されます。  Kubernetes クラスターから監視を有効化すると、コンテナー化されたバージョンの Linux 向けの Log Analytics エージェントを使用してメトリックとログが自動的に収集されます。 メトリックはメトリック ストアに書き込まれ、ログ データは [Log Analytics](../log-query/log-query-overview.md) ワークスペースに関連付けられたログ ストアに書き込まれます。 

![Azure Monitor for containers のアーキテクチャ](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>コンテナーに対する Azure Monitor の機能

Azure Monitor for containers では、お使いの Kubernetes クラスターやコンテナー ワークロードのパフォーマンスと正常性を把握できる Azure Monnitor のさまざまな機能を利用した、包括的な監視エクスペリエンスを提供しています。 Azure Monitor for containers を利用すると、次のことが可能です。

* ノードで実行されている AKS コンテナーと、そのプロセッサおよびメモリの平均使用率を特定します。 この知識は、リソースのボトルネックを特定するのに役立ちます。
* Azure Container Instances でホストされているコンテナー グループとそのコンテナーのプロセッサおよびメモリ使用率を特定します。  
* コントローラーまたはポッドのどこにコンテナーが存在するかを特定します。 この知識は、コントローラーまたはポッド全体のパフォーマンスを表示するのに役立ちます。
* ポッドをサポートする標準プロセスと関連のない、ホスト上で実行されているワークロードのリソース使用率を確認します。
* 平均的な負荷、および最大の負荷がかかったときのクラスターの動作を理解します。 この知識は、容量ニーズを特定し、クラスターが維持できる最大負荷を判断するのに役立ちます。 
* ノードまたはコンテナーの CPU やメモリの使用量がしきい値を超えた場合に、事前に通知したりそのことを記録したりするように、アラートを構成します。
* [Prometheus](https://prometheus.io/docs/introduction/overview/) と統合して、[クエリ](container-insights-log-search.md)を使用してノードと Kubernetes から収集したアプリケーションとワークロードのメトリックを表示し、カスタム アラート、ダッシュボード、詳細な分析を作成します。

>[!NOTE]
>現時点では、Prometheus のサポートはパブリック プレビューの機能です。
>

## <a name="how-do-i-access-this-feature"></a>この機能にアクセスする方法
コンテナーに対する Azure Monitor には、Azure Monitor からアクセス、または選択した AKS クラスターから直接アクセスという 2 つの方法でアクセスできます。 Azure Monitor からは、監視対象かどうかにかかわらず、デプロイされているすべてのコンテナーを全体的に把握し、サブスクリプションとリソース グループ全体を検索してフィルター処理することができます。また、選択したコンテナーからコンテナーに対する Azure Monitor を詳細に調べることができます。  それ以外にも、選択した AKS コンテナーの AKS ページから機能に直接アクセスできます。  

![コンテナーに対する Azure Monitor にアクセスする方法の概要](./media/container-insights-overview/azmon-containers-experience.png)

構成、監査、リソースの使用率を確認するために Docker および Windows コンテナー ホストを監視および管理する必要がある場合は、[コンテナー監視ソリューション](../../azure-monitor/insights/containers.md)を参照してください。

## <a name="next-steps"></a>次の手順
AKS クラスターの監視を開始するには、「[Azure Monitor for containers を有効にする方法](container-insights-onboard.md)」を参照し、監視を有効にするための要件と使用できる方法を把握してください。  
