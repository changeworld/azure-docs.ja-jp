---
title: Azure Log Analytics におけるデータの表示と分析 | Microsoft Docs
description: Log Analytics のログ検索から Azure Monitor のログ クエリ エクスペリエンスに移行するユーザー向けの情報。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: bwren
ms.openlocfilehash: 89811e95ec24eb354020dd6384f6fdab6cee8c8f
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2019
ms.locfileid: "54392573"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Log Analytics のログ検索から Azure Monitor のログに移行する
最近、Log Analytics のログ検索の後継として、Azure Monitor のログを分析するための新しいエクスペリエンスが導入されました。 現在もログ検索ページには、Azure portal の **[Log Analytics ワークスペース]** ページにある **[ログ (クラシック)]** メニュー項目からアクセスできますが、このページは 2019 年 2 月 15 日をもって削除されます。 この記事では、ログ検索から円滑に移行できるよう、2 つのエクスペリエンスの違いについて説明しています。 

## <a name="extract-custom-fields"></a>カスタム フィールドを抽出する 
ログ検索では、リスト ビューから[カスタム フィールド](../platform/custom-fields.md)を抽出します。このとき、フィールドのメニューには _[フィールドを <テーブル> から抽出する]_ というアクションが表示されます。

![ログ検索のフィールドの抽出](media/log-search-transition/extract-fields-log-search.png)

Azure Monitor のログでは、カスタム フィールドをテーブル ビューから抽出します。 _フィールドの抽出_アクションには、レコードの左側にある矢印をクリックしてレコードを展開し、省略記号をクリックしてアクセスします。

![ログのフィールドの抽出](media/log-search-transition/extract-fields-logs.png)

## <a name="filter-results-of-a-query"></a>クエリの結果をフィルター処理する
ログ検索では、検索結果が生成される際にフィルターのリストが表示されます。 いずれかのフィルターを選択して、**[適用]** をクリックすると、選択したフィルターでクエリが実行されます。

![ログ検索フィルター](media/log-search-transition/filter-log-search.png)

Azure Monitor のログでは、*[Filter (preview)]\(フィルター (プレビュー)\)* を選択してフィルターを表示します。 フィルター アイコンをクリックすると、さらに別のフィルターが表示されます。 いずれかのフィルターを選択して、**[Apply & Run]\(適用して実行\)** をクリックすると、選択したフィルターでクエリが実行されます。

![ログ フィルター](media/log-search-transition/filter-logs.png)

## <a name="functions-and-computer-groups"></a>関数とコンピューター グループ
ログ検索で検索条件を保存するには、**[保存した検索条件]** と **[追加]** を選択して、名前、カテゴリ、クエリ テキストを指定します。 [コンピューター グループ](../platform/computer-groups.md)は、関数のエイリアスを追加することによって作成します。

![ログ検索の保存](media/log-search-transition/save-search-log-search.png)

Azure Monitor のログで現在のクエリを保存するには、**[保存]** を選択します。 **[名前を付けて保存]** を _[関数]_ に変更し、**[関数のエイリアス]** を指定して[関数](functions.md)を作成します。

![ログ クエリの保存](media/log-search-transition/save-query-logs.png)

## <a name="saved-searches"></a>保存した検索条件
ログ検索では、保存した検索条件に、操作バーの項目である **[保存した検索条件]** からアクセスできます。 Azure Monitor のログでは、保存したクエリに**クエリ エクスプローラー**からアクセスします。

![クエリ エクスプローラー](media/log-search-transition/query-explorer.png)

## <a name="take-action"></a>アクションの実行
ログ検索では、検索結果から **[アクションの実行]** を選択することで [Runbook を開始](take-action.md)できます。

![アクションの実行](media/log-search-transition/take-action-log-search.png)

Azure Monitor のログでは、[ログ クエリからアラートを作成](../platform/alerts-log.md)します。 アラートに応じて実行されるアクションを少なくとも 1 つ含んだアクション グループを構成することになります。

![アクション グループ](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>次の手順

- 新しい [Azure Monitor のログ エクスペリエンス](get-started-portal.md)について詳しく学習します。