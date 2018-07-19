---
title: Azure 監視エージェントの概要 | Microsoft Docs
description: この記事では、Azure VM の監視をサポートする Azure エージェントについて概説します。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: magoedte
ms.openlocfilehash: a399c3968e5ee1e2d1f6d623a68dbb1e15cef212
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2018
ms.locfileid: "37088454"
---
# <a name="overview-of-the-azure-agents-to-monitor-azure-virtual-machines"></a>Azure 仮想マシンを監視する Azure エージェントの概要
Microsoft Azure では、Azure または Microsoft Windows や Linux を実行する他のクラウド プロバイダー内でホストされている仮想マシンからの各種のデータをさまざまな方法で収集できます。  この記事では、どのエージェントがサービス管理や一般的な監視要件をサポートするかを判断するために、各エージェントの違いや使用可能な機能について説明します。  

## <a name="comparing-azure-diagnostic-and-log-analytics-agent"></a>Azure 診断と Log Analytics エージェントを比較する
Azure では現在、Azure VM を監視できるエージェントとして、Azure 診断拡張機能、および Log Analytics エージェント (Linux および Windows 用) の 2 タイプが用意されています。  基本的に、これらのエージェントは、メトリックとログを収集して、リポジトリに転送するように設計されています。 ただし、共通する点はここまでです。  

2010 年に一般公開されて以降、Azure Cloud Services 用に提供されている [Azure 診断拡張機能](../monitoring-and-diagnostics/azure-diagnostics.md)は、VM などの Azure IaaS リソースから診断データを収集して、Azure ストレージに保持するエージェントです。  データがストレージに保持されたら、[Visual Studio のサーバー エクスプローラー](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md)や [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) などの提供されているツールの 1 つで表示することができます。

次のものを収集することを選択できます。

* 事前に定義された一連のオペレーティング システム パフォーマンス カウンターとイベント ログ。どちらを収集するかを選択できます。 
* IIS Web サーバーに対するすべての要求/失敗した要求
* .NET アプリのトレース出力のログ
* Windows イベント トレーシング (ETW) イベント 
* Syslog からのログ イベント  
* クラッシュ ダンプ 

データは、[Application Insights](../application-insights/app-insights-cloudservices.md) や [Log Analytics](../log-analytics/log-analytics-overview.md) に転送することも、[イベント ハブ](../event-hubs/event-hubs-what-is-event-hubs.md)を使用して Azure 以外のサービスに転送することもできます。 

高度な監視を行って、メトリックやログの サブセット以外も収集する必要がある場合は、Log Analytics エージェント (Windows および Linux 用) が必要です。  このエージェントでは、Automation や Log Analytics などの Azure サービスに加え、Azure のすべての機能も利用して、Azure VM をそのライフサイクルの間、総合的に管理できます。 次のトピックがあります。

* [Azure Automation Update](../automation/automation-update-management.md) による、オペレーティング システム更新プログラムの管理
* [Azure Automation Desired State Configuration](../automation/automation-dsc-overview.md) による、構成状態の一貫性の維持
* [Azure Automation Change Tracking および Inventory](../automation/automation-change-tracking.md) による、構成変更の追跡
* Log Analytics による、OS およびホストされたアプリケーション ([FluentD](../log-analytics/log-analytics-data-sources-json.md)、[カスタム ログ](../log-analytics/log-analytics-data-sources-custom-logs.md)、[MySQL、Apache](../log-analytics/log-analytics-data-sources-linux-applications.md) など) からのカスタム ログの収集
* [Application Insights](https://docs.microsoft.com/azure/application-insights/) や [Azure Security Center](https://docs.microsoft.com/azure/security-center/) などの Azure サービスでは、データが直接 Log Analytics にネイティブに保管されます。  

## <a name="next-steps"></a>次の手順

- 「[Collect data from computers in your environment with Log Analytics](../log-analytics/log-analytics-concept-hybrid.md)」(Log Analytics を使用して、環境内のコンピューターからデータを収集する) を参照し、データセンターやその他のクラウド環境内にあるコンピューターにエージェントをデプロイするための要件と使用可能な手法を確認します。
- 「[Azure Virtual Machines に関するデータの収集](../log-analytics/log-analytics-quick-collect-azurevm.md)」を参照して、Azure VM からのデータ コレクションを構成します。 