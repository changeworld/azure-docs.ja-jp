---
title: Azure Monitor for VMs (プレビュー) の既知の問題 | Microsoft Docs
description: この記事では Azure Monitor for VMs の既知の問題について説明します。Azure Monitor for VMs は、Azure VM オペレーティング システムの正常性、アプリケーションの依存関係の検出、パフォーマンスの監視が組み合わされた Azure のソリューションです。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/02/2019
ms.openlocfilehash: b59e2d1897557b47bcfeafbc17141f869e2f192e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450675"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Azure Monitor for VMs (プレビュー) の既知の問題

この記事では Azure Monitor for VMs の既知の問題について説明します。Azure Monitor for VMs は、Azure VM オペレーティング システムの正常性、アプリケーション コンポーネントの検出、パフォーマンスの監視が組み合わされた Azure のソリューションです。 

## <a name="health"></a>Health 
正常性機能の現在のリリースに関する既知の問題を次に示します。

- Azure VM は、削除または移動された場合でも、しばらくの間、VM リスト ビューに表示されます。 さらに、移動または削除された VM の状態をクリックすると、**正常性診断**ビューが開き、読み込みループが開始されます。 削除された VM の名前を選択すると、ウィンドウが開き、VM が削除されたことを示すメッセージが表示されます。
- しきい値の更新などの構成の変更は、ポータルまたは Workload Monitor API でそれらがすぐに更新される場合でも、最大 30 分かかります。 
- 正常性診断エクスペリエンスは他のどのビューよりも迅速に更新されます。 ビューの切り替えを行う場合に、情報の遅延が発生する可能性があります。 
- Linux VM に関して、単一 VM ビューの正常性基準を一覧表示するページのタイトルに、ユーザー定義の VM 名ではなく VM のドメイン名全体が表示されます。 
- サポートされている方法の 1 つを使用して VM の監視を無効にした後、それを再びデプロイする場合は、前と同じワークスペースにデプロイする必要があります。 異なるワークスペースを選択した場合、その VM の正常性状態を表示する際に、一貫性のない動作が見られる場合があります。
- ソリューション コンポーネントをワークスペースから削除した後で、引き続きヘルス状態を Azure VM から見ることができます。たとえばパフォーマンス データやマップ データは、ポータルでそれぞれのビューに移動すると見ることができます。 データはしばらくするといずれはパフォーマンスおよびマップのビューに表示されなくなります。しかし、ヘルス ビューには引き続き VM のヘルス状態が表示されます。 **[今すぐ試す]** オプションは、パフォーマンス ビューとマップ ビューからの再オンボードのみに使用できます。

## <a name="next-steps"></a>次のステップ
仮想マシンの監視を有効にするための要件と方法については、[Azure Monitor for VMs の有効化](vminsights-enable-overview.md)に関するページを確認してください。
