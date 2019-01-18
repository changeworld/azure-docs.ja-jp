---
title: Azure Stack 上の Azure Monitor | Microsoft Docs
description: Azure Stack 上の Azure Monitor について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2018
ms.author: mabrigg
ms.openlocfilehash: cfeb1ce7791db463dc0ac39376baa8228dd96daf
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106292"
---
# <a name="azure-monitor-on-azure-stack"></a>Azure Stack 上の Azure Monitor

*適用対象:Azure Stack 統合システム*

この記事では、Azure Stack 上の Azure Monitor サービスの概要を説明します。 Azure Monitor の動作について説明し、Azure Stack での Azure Monitor の使用方法に関するその他の情報を紹介します。 

Azure Monitor の紹介、概要、および使用開始方法については、Azure の記事「[Azure Monitor の使用](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started)」を参照してください。

![Azure Stack の Monitor ブレード](./media/azure-stack-metrics-azure-data/azs-monitor.png)

Azure Monitor は、Azure リソースを監視するための 1 つのソースを提供するプラットフォーム サービスです。 Azure Monitor を使用すると、Azure のリソースのメトリックとログに対して、視覚化、クエリ、ルーティング、アーカイブなどのアクションを実行できます。 このデータは、Azure Stack 管理ポータル、Monitor PowerShell コマンドレット、クロスプラットフォーム CLI、または Azure Monitor REST API を使用して操作できます。 Azure Stack でサポートされている特定の接続については、「[Azure Stack の監視データの使用](azure-stack-metrics-monitor.md)」を参照してください

> [!Note]  
Azure Stack Development Kit では、メトリックと診断ログを使用できません。

## <a name="prerequisites"></a>前提条件

サブスクリプションのオファー リソース プロバイダー設定で **Microsoft.insights** リソース プロバイダーを登録します。 サブスクリプションに関連付けられているオファーでリソース プロバイダーが使用可能であることを確認できます。

1. Azure Stack 管理ポータルを開きます。
2. **[オファー]** を選択します。
3. サブスクリプションに関連付けられているオファーを選択します。
4. **[設定]** で、**[リソース プロバイダー]** を選択します。 
5. 一覧内で **Microsoft.Insights** を見つけ、状態が **[登録済み]** になっていることを確認します。

## <a name="overview"></a>概要

Azure 上の Azure Monitor と同様、Azure Monitor 上の Azure Stack では、ほとんどのサービスに対して標準となるインフラストラクチャのメトリックおよびログを提供します。

## <a name="azure-monitor-sources-compute-subset"></a>Azure Monitor のソース: Compute のサブセット

![Azure Monitor のソース - Compute のサブセット](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.png)

Azure Stack 上の **Microsoft.Compute** リソース プロバイダーには、以下が含まれます。
 - Virtual Machines 
 - 仮想マシン スケール セット

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>アプリケーション - 診断ログ、アプリケーション ログ、およびメトリック

アプリケーションは、**Microsoft.Compute** リソース プロバイダーで実行されている VM の OS で実行できます。 これらのアプリケーションと VM からは、それぞれに固有のログとメトリックのセットが出力されます。 Azure Monitor は、Azure 診断拡張機能 (Windows または Linux) を利用して、アプリケーション レベルのメトリックとログの大半を収集します。 

メジャーの種類は次のとおりです。
 - パフォーマンス カウンター
 - アプリケーション ログ
 - Windows イベント ログ
 - .NET イベント ソース
 - IIS ログ
 - マニフェスト ベースの ETW
 - クラッシュ ダンプ
 - カスタム エラー ログ

> [!Note]  
> Azure Stack 上の Linux 診断拡張機能はサポートされていません。

### <a name="host-and-guest-vm-metrics"></a>ホスト VM とゲスト VM のメトリック

上記のコンピューティング リソースには、専用のホスト VM とゲスト OS が存在します。 ホスト VM およびゲスト OS は、Hyper-V ハイパーバイザーのルート VM およびゲスト VM に相当します。 ホスト VM とゲスト OS の両方のメトリックを収集できます。 また、ゲスト OS の診断ログを収集することもできます。 Azure Stack 上のホスト VM とゲスト VM に関して収集可能なメトリックの一覧については、[Azure Stack 上の Azure Monitor でサポートされるメトリック](azure-stack-metrics-supported.md)に関するページを参照してください。 

### <a name="activity-log"></a>アクティビティ ログ

計算リソースの情報については、Azure Stack インフラストラクチャで確認できるようなアクティビティ ログを検索できます。 このログには、リソースが作成された時点や破棄された時点などの情報が記載されています。 Azure Stack のアクティビティ ログには、Azure との整合性があります。 詳細については、[Azure のアクティビティ ログの概要](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)に関するページを参照してください。 


## <a name="azure-monitor-sources-everything-else"></a>Azure Monitor のソース: 上記以外のすべて

![Azure Monitor のソース - 上記以外のすべて](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.png)

### <a name="resources---metrics-and-diagnostics-logs"></a>リソース - メトリックと診断ログ

収集可能なメトリックと診断ログは、リソースの種類によって異なります。 Azure Stack 上の各リソースの収集可能なメトリックの一覧については、サポートされるメトリックに関する記事を参照してください。 詳細については、[Azure Stack 上の Azure Monitor でサポートされるメトリック](azure-stack-metrics-supported.md)に関するページを参照してください。

### <a name="activity-log"></a>アクティビティ ログ

アクティビティ ログは、コンピューティング リソースの場合と同じです。 

### <a name="uses-for-monitoring-data"></a>監視データの用途

**保存とアーカイブ**  

一部の監視データは既に保存されており、Azure Monitor で一定期間使用できます。 
 - メトリックは 90 日間保存されます。 
 - アクティビティ ログのエントリは 90 日間保存されます。 
 - 診断ログは保存されません。
 - より長期間保存するには、データをストレージ アカウントにアーカイブしてください。

**クエリ**  

Azure Monitor REST API、クロスプラットフォーム コマンドライン インターフェイス (CLI) コマンド、PowerShell コマンドレット、または .NET SDK を使えば、システム ストレージまたは Azure ストレージ内のデータにアクセスできます。 

**視覚化**

グラフィックスやグラフで監視データを視覚化すると、データ自体を見るよりも格段に早く傾向を把握できます。 

視覚化の方法には、次のようなものがあります。
 - Azure Stack ユーザーおよび管理ポータルを使用する
 - データを Microsoft Power BI にルーティングする
 - ライブ ストリーミングを使ってサード パーティ製の視覚化ツールにデータをルーティングするか、ツールに Azure Storage のアーカイブからデータを読み込ませる

## <a name="methods-of-accessing-azure-monitor-on-azure-stack"></a>Azure Stack 上の Azure Monitor にアクセスする方法

一般に、次のいずれかの方法で、データの追跡、ルーティング、および取得の操作を行うことができます。 ただし、アクションやデータの種類によっては利用できない方法もあります。

 - [Azure Stack ポータル](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-use-portal)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [クロスプラットフォーム コマンド ライン インターフェイス (CLI)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [REST API](https://docs.microsoft.com/rest/api/monitor)
 - [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>次の手順

「[Azure Stack の監視データの使用](azure-stack-metrics-monitor.md)」を参照して、Azure Stack での監視データの使用に関するオプションの詳細を確認してください。
