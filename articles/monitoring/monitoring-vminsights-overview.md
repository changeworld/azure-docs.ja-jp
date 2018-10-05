---
title: VM 用 Azure Monitor とは? | Microsoft Docs
description: Azure Monitor for VMs は Azure Monitor の機能です。この機能では、Azure VM オペレーティング システムの正常性とパフォーマンスの監視が組み合わされているほか、アプリケーション コンポーネントと他のリソースとの依存関係が自動的に検出され、それらの間の通信がマップされます。 この記事では概要を説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: 26fcc3eb78af53360cca57382b4c06b017f36c0e
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063270"
---
# <a name="what-is-azure-monitor-for-vms"></a>VM 用 Azure Monitor とは?

Azure Monitor for VMs では、Azure 仮想マシン (VM) の大規模な監視が行われます。それを行うために、さまざまなプロセスや、その他のリソースおよび外部プロセスに対する相互接続された依存関係など、Windows および Linux VM のパフォーマンスと正常性が分析されます。 このソリューションでは、オンプレミスまたは別のクラウド プロバイダーでホストされている VM について、パフォーマンスおよびアプリケーションの依存関係の監視もサポートされています。  これには、そのような詳細な分析情報を提供する次の 3 つの主要機能が含まれています。

* Windows および Linux のオペレーティング システムが稼働している Azure VM の論理コンポーネントは、評価条件が満たされたときに、事前に構成された一連の正常性の基準とアラートに基づいて測定されます。  
* ゲスト VM オペレーティング システムのプロセッサ、メモリ、ディスク、およびネットワーク アダプターからの主要なパフォーマンス メトリックが収集され、事前に定義された、傾向を示すパフォーマンス グラフに表示されます。
* 依存関係マップ。これには、複数のリソース グループおよびサブスクリプションからの該当する VM と相互接続されているコンポーネントが検出されると表示されます。  

これらの機能は、次の 3 つの観点から確認できます。

* Health
* [パフォーマンス]
* マップ

>[!NOTE]
>現時点では、正常性機能は、Azure 仮想マシンにのみ提供されています。
>

Log Analytics との統合により、強力な集計、フィルター処理、時間の経過に伴うデータの傾向分析の実行機能が提供されます。 Azure Monitor、Service Map、または Log Analytics はいずれも単独では、ご利用のワークロードの包括的な監視を実現することはできません。  

このデータは仮想マシンから直接、単一の VM のコンテキストで表示することができます。あるいは、Azure Monitor を使用すれば、各機能の次のパースペクティブに基づいてご利用の VM の集計ビューが提供されます。

* 正常性 - リソース グループに関連する VM
* マップとパフォーマンス - 特定の Log Analytics ワークスペースに報告するように構成された VM。

![ポータルからの仮想マシン分析情報のパースペクティブ](./media/monitoring-vminsights-overview/vminsights-azmon-directvm-01.png)

DevOps では、重大なオペレーティング システムのイベント、パフォーマンスのボトルネック、およびネットワークの問題を識別することによって、重要なアプリケーションのパフォーマンスと可用性を予測すると共に、問題が他の依存関係に関連しているかどうかを把握することができます。  

## <a name="data-usage"></a>データ利用状況 

VM 用 Azure Monitor の使用を開始するとすぐに、ご利用の VM によって収集されたデータが取り込まれ、Azure Monitor に格納されます。  Azure Monitor for VMs では、Azure Monitor の [料金ページ](https://azure.microsoft.com/pricing/details/monitor/)に公開された価格設定に従って、取り込みおよび保持されたデータ、時系列で監視された正常性条件メトリックの数、作成されたアラート ルール、送信された通知に対して課金されます。

ログのサイズは、カウンターの文字列の長さによって異なり、論理ディスクおよびネットワーク アダプターの数に応じて大きくなる可能性があります。  ワークスペースを既にお持ちで、これらのカウンターを収集している場合、重複する課金は適用されません。  Service Map を既に使用している場合、追加の接続データが Azure Monitor に送信されることが唯一の変更点となります。

## <a name="next-steps"></a>次の手順
仮想マシンの監視を有効にするための要件と方法を理解するため、「[Azure Monitor for VM の配布準備をする](monitoring-vminsights-onboard.md)」を確認します。
