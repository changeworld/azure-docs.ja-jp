---
title: "Azure Log Analytics の変更点 | Microsoft Docs"
description: "この記事では、よく寄せられる新しいクエリ言語への Log Analytics のアップグレードに関する質問について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: bwren
ms.openlocfilehash: 783223a37c2a13c9affbf382209ca2aa4f1ba4c7
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2017
---
# <a name="whats-changed-in-azure-log-analytics"></a>Azure Log Analytics の変更点
クエリ言語自体に加えて、Log Analytics ワークスペースが[新しいクエリ言語にアップグレード](log-analytics-log-search-new.md)されるときに認識しておく必要のあるいくつかの改善点と変更点があります。  この記事では、従来のワークスペースとアップグレードされたワークスペース間での変更について簡単に説明し、それぞれの詳しいコンテンツへのリンクを示します。 

アップグレードに関する既知の問題の説明とよく寄せられる質問への回答については、「[Log Analytics の新しいログ検索についての FAQ と既知の問題](log-analytics-log-search-faq.md)」をご覧ください。  

## <a name="query-language"></a>クエリ言語
Log Analytics のアップグレードの主要な変更点は、以前の言語よりも大幅に改善された新しいクエリ言語です。  

従来の言語と新しい言語の一般的な操作の直接の比較は、「[Azure Log Analytics の新しいクエリ言語への移行](log-analytics-log-search-transition.md)」をご覧ください。  新しい言語の完全なドキュメントは、「[Azure Log Analytics Query Language (Azure Log Analytics クエリ言語)](https://docs.loganalytics.io)」をご覧ください。


## <a name="computer-groups"></a>コンピューター グループ
コンピューター グループを作成する方法は変更されていませんが、それぞれに一意の別名を指定することが必要になりました。  ログ検索に基づくコンピューター グループでは、新しい言語の構文を使用する必要があります。

ログ検索でコンピューター グループを使用するための新しい構文があります。  $ComputerGroups 関数を使用する代わりに、コンピューター グループはそれぞれ一意の別名を持つ関数として実装されるようになりました。  他の関数と同様に、ログ検索では別名を使用します。  

詳しくは、「[Log Analytics のログ検索におけるコンピューター グループ](log-analytics-computer-groups.md)」をご覧ください。


## <a name="log-search-portals"></a>ログ検索ポータル
ログ検索を作成および実行するログ検索ポータルに加えて、大幅に向上した編集機能を提供する高度な分析ポータルを使用できるようになりました。

この 2 つのポータルの簡単な説明については、「[Azure Log Analytics でログ クエリを作成および編集するためのポータル](log-analytics-log-search-portals.md)」をご覧ください。  「[Getting Started with the Analytics Portal (分析ポータルの概要)](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal)」で新しいポータルのチュートリアルを実行できます。

## <a name="alerts"></a>アラート
アラートは、アップグレード後のワークスペースでも同じ動作をしますが、実行するクエリは新しい言語で記述する必要があります。  アップグレードの前にあった既存のアラート ルールは、新しい言語に自動的に変換されます。  詳しくは、「[Log Analytics のアラートについて](log-analytics-alerts.md)」をご覧ください。

アラートから送信された Runbook と webhook のペイロードの形式が変更されました。  新しいペイロード形式について詳しくは、「[Log Analytics のアラート ルールへのアクションの追加](log-analytics-alerts-actions.md)」をご覧ください。

## <a name="dashboards"></a>ダッシュボード
[ダッシュボード](log-analytics-dashboards.md)は今後使用されなくなる予定です。  ワークスペースがアップグレードされる前にダッシュボードに追加したタイルは引き続き使用できますが、タイルを編集したり、新しいダッシュボードを追加したりすることはできません。  カスタム ビューを作成するには、ダッシュボードより豊富な機能セットを持つビュー デザイナーを使用してください。

ビュー デザイナーについて詳しくは、「[Log Analytics のビュー デザイナーを使用してカスタム ビューを作成する](log-analytics-view-designer.md)」をご覧ください。

## <a name="power-bi"></a>Power BI
アップグレードされたワークスペースでは Log Analytics データを Power BI にエクスポートするプロセスが変更され、アップグレード前に作成した既存のスケジュールは無効になります。  

詳しくは、「[Log Analytics データを Power BI にエクスポートする](log-analytics-powerbi.md)」をご覧ください。

## <a name="powershell"></a>PowerShell
PowerShell からログ検索を実行するための Get-AzureRmOperationalInsightsSearchResults は、アップグレード後のワークスペースでは機能しません。  アップグレード後のワークスペースでのこの機能には、Invoke-LogAnalyticsQuery を使用します。

詳しくは、[Azure Log Analytics REST API - PowerShell コマンドレット](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets)に関するページをご覧ください。

## <a name="log-search-api"></a>Log Search API
ログ検索の REST API が変更され、従来のバージョンを使用するソリューションは新しいバージョンの API を使用するように更新する必要があります。   

新しいバージョンの API について詳しくは、[Azure Log Analytics REST API](https://dev.loganalytics.io/) に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

- アップグレードに関する既知の問題の説明とよく寄せられる質問への回答については、「[Log Analytics の新しいログ検索についての FAQ と既知の問題](log-analytics-log-search-faq.md)」をご覧ください。