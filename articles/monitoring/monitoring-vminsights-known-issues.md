---
title: VM 用 Azure Monitor の既知の問題 | Microsoft Docs
description: VM 用 Azure Monitor は Azure のソリューションの 1 つです。このソリューションでは、Azure VM オペレーティング システムの正常性とパフォーマンスの監視が組み合わされているほか、アプリケーション コンポーネントと他のリソースとの依存関係が自動的に検出され、その間のやり取りがマップされます。 この記事では、次の既知の問題について説明します。
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
ms.date: 10/15/2018
ms.author: magoedte
ms.openlocfilehash: 6d1f1d1ae07ec32262f655fd6ed7205a70e252f4
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385093"
---
# <a name="known-issues-with-azure-monitor-for-vms"></a>VM 用 Azure Monitor の既知の問題

VM 用 Azure Monitor の正常性機能に関する既知の問題を次に示します。

- 正常性機能は、Log Analytics ワークスペースに接続されているすべての VM に対してオンボードされます。これは、オンボードが単一の VM から開始されて完了した場合にも当てはまります。
- 移動されたか削除されたために Azure VM がもう存在しない場合でも、その VM は 3 日から 7 日の間、VM リスト ビューに表示されます。 さらに、移動または削除された VM の状態をクリックすると、その VM に対する**正常性診断**が起動され、読み込みループに入ります。 削除された VM の名前を選択すると、ブレードが起動し、VM が削除されていることを示すメッセージが表示されます。
- このリリースでは、正常性の基準となる期間と頻度を変更できません。 
- 正常性の基準は無効にできません。 
- オンボード後、[Azure Monitor] -> [仮想マシン] -> [正常性] または VM のリソース ブレード -> [インサイト] にデータが表示されるまでに時間がかかる場合があります
- 正常性診断の更新は他のビューよりも迅速に行われるため、ビューを切り替えた場合に、情報の遅延が発生する可能性があります  
- VM の正常性について Azure Monitor で提供されるアラートの概要は、監視対象の Azure VM で検出された正常性の問題に関して発生したアラートのみが対象となります。
- 1 つの VM および Azure Monitor の **[Alerts list]\(アラートの一覧\)** ビュー ページには、過去 30 日間に監視状態が "起動済み" に設定されたアラートが表示されます。  これらは構成できません。 ただし、概要をクリックした後に、**[Alerts list]\(アラートの一覧\)** ビュー ページが起動されたら、監視条件と時間の範囲の両方のフィルター値を変更できます。
- **[Alerts list]\(アラートの一覧\)** ビュー ページでは、**[リソースの種類]**、**[リソース]**、および **[サービスの監視]** の各フィルターは変更しないことをお勧めします。これらのフィルターは、ソリューションに合わせて構成されているためです (この一覧ビューには、Azure Monitor -> [Alerts list]\(アラートの一覧\) ビューのフィールドの他に、追加のフィールドがいくつか表示されています)。    
- Linux VM では、**[正常性の診断]** ビューに、ユーザー定義の VM 名ではなく、VM のドメイン名全体が表示されます。
- VM をシャットダウンすると、その正常性基準が重大な状態に更新されるものと、正常な状態に更新されるものがあり、VM の最終的な状態は重大な状態になります。
- 正常性アラートの重大度を変更することはできません。有効または無効にのみ変更できます。  また、一部の重大度は、正常性の基準の状態に基づいて更新されます。
- 正常性基準インスタンスの設定を変更すると、VM の同じ種類の正常性基準インスタンスすべてについて同じ設定が変更されます。 たとえば、論理ディスク C: に対応する空きディスク領域の正常性基準インスタンスのしきい値を変更すると、このしきい値は、同じ VM で検出および監視されている他の論理ディスクすべてに適用されます。   
- Windows VM を対象とする次の正常性条件のしきい値は変更できません。これらの正常性状態は、既に **[実行中]** または **[使用可能]** に設定されているためです。 [Workload Monitor API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager) からクエリが実行されると、以下に該当する場合、サービスまたはエンティティの正常性状態には、*comparisonOperator* 値 **LessThan** または **GreaterThan** と "*しきい値*" **4** が表示されます。
   - DNS クライアント サービスの正常性 – サービスが実行されていない 
   - DHCP クライアント サービスの正常性 – サービスが実行されていない 
   - RPC サービスの正常性 – サービスが実行されていない 
   - Windows ファイアウォール サービスの正常性 – サービスが実行されていない
   - Windows イベント ログ サービスの正常性 – サービスが実行されていない 
   - サーバー サービスの正常性 – サービスが実行されていない 
   - Windows リモート管理サービスの正常性 – サービスが実行されていない 
   - ファイル システム エラーまたは破損 – 論理ディスクが使用できない

- 次の Linux の正常性条件のしきい値は変更できません。これらの正常性状態は、既に **true** に設定されているためです。  Workload Monitoring API からエンティティに対するクエリが実行されると、コンテキストに応じて、正常性状態には、 *comparisonOperator* 値 **LessThan**と "*しきい値*" **1** が表示されます。
   - 論理ディスクの状態 – 論理ディスクがオンライン/使用可能でない
   - ディスクの状態 – ディスクがオンライン/使用可能でない
   - ネットワーク アダプターの状態 - ネットワーク アダプターが無効  

- Windows での **Total CPU Utilization Percentage (合計 CPU 使用率の割合)** 正常性条件は、Workload Monitoring API からクエリが実行されたときに CPU 使用率が 95% を超え、システム キューの長さが 15 よりも大きい場合、ポータルでは **4 に等しくない** と表示されます。 このリリースでは、この正常性条件を変更することはできません。  
- しきい値の更新などの構成の変更は、ポータルまたは Workload Monitor API ではすぐに更新される場合でも、実際に有効になるまで最大 30 分かかります。  
- 個々のプロセッサと論理プロセッサのレベルの正常性条件は Windows では使用できません。Windows VM では **Total CPU Utilization Percentage (合計 CPU 使用率の割合)** のみを使用できます。  
- 各正常性条件に対して定義されているアラート ルールは、Azure portal には公開されません。 それらは、正常性アラート ルールを有効または無効にする [Workload Monitor API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager) からのみ構成可能です。  
- 正常性アラート用の [Azure Monitor アクション グループ](../monitoring-and-diagnostics/monitoring-action-groups.md)の割り当ては、Azure portal では実行できません。 正常性アラートが発生するたびにトリガーされるアクション グループを構成するには、通知設定 API を使用する必要があります。 現時点では、VM に対してアクション グループを割り当てることができ、VM で発生したすべての*正常性アラート*によって同じアクション グループがトリガーされるように設定できます。 従来の Azure アラートのようなすべての正常性アラート ルールに対する別個のアクション グループの概念はありません。 さらに、正常性アラートがトリガーされたときに、電子メールまたは SMS の送信によって通知を行うように構成されているアクション グループのみがサポートされます。 

## <a name="next-steps"></a>次の手順
仮想マシンの監視を有効にするための要件と方法を理解するため、「[Azure Monitor for VM の配布準備をする](monitoring-vminsights-onboard.md)」を確認します。