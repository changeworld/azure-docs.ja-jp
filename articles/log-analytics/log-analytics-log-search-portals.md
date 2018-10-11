---
title: Azure Log Analytics におけるデータの表示と分析 | Microsoft Docs
description: この記事では、Azure Log Analytics でのログ検索の作成および編集に使用できるポータルについて説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 8a73ad7c17710ab3e6e4ed41eced643ce9eb86dc
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48042480"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>Log Analytics におけるデータの表示と分析
Azure portal には、Log Analytics に保存されたデータの分析とアドホック分析用のクエリ作成に使用できる 2 つのオプションがあります。 これらのポータルを使用して作成したクエリは、アラートやダッシュボードなどの他の機能で使用できます。

## <a name="log-analytics-page"></a>Log Analytics ページ
[Log Analytics] メニューの **[ログ]** から Log Analytics ページを開きます。 これは、ログ データ操作とクエリ作成の新しいエクスペリエンスです。 「[Get started with the Log Analytics page in the Azure portal (Azure portal の Log Analytics ページの概要)](query-language/get-started-analytics-portal.md)」で、このポータルの概要を参照して、その機能を確認できます。

Log Analytics ページは、[ログ検索 (クラシック)](#log-search-classic) エクスペリエンスに比べて、次の機能が強化されています。

* 複数のタブ - 複数のクエリを操作する個別のタブを作成します。
* 豊富な視覚エフェクト - さまざまなグラフ作成オプション。
* 強化された IntelliSense と言語オート コンプリート。
* 構文の強調表示 – クエリの読みやすさが向上します。 
* クエリ エクスプローラー - 保存されたクエリと関数にアクセスします。
* スキーマ ビュー – クエリの記述を支援するために、データの構造を確認します。
* 共有 - クエリへのリンクを作成したり、共有されている Azure ダッシュボードにクエリをピン留めしたりします。
* スマート分析 - グラフのスパイクを識別し、その原因を迅速に分析します。
* 列の選択 - クエリ結果の列の並べ替えとグループ化を行います。

> [!NOTE]
> Log Analytics ページには、Azure portal に含まれていない外部ツールである高度な分析ポータルと同じ機能があります。 高度な分析ポータルは引き続き使用できますが、Azure portal にある高度な分析ポータルへのリンクや他の参照は、この新しいページに置き換えられています。

![高度な分析ポータル](media/log-analytics-log-search-portals/advanced-analytics-portal.png)

### <a name="resource-logs"></a>リソース ログ
新しい Log Analytics のエクスペリエンスは、Virtual Machines などのさまざまな Azure リソースと統合されます。 つまり、Azure Monitor や Log Analytics に切り替えることなく、また、リソース コンテキストを失うことなく、リソースの監視メニューから直接 Log Analytics ページを開くことができます。 **[ログ]** は一部の Azure リソースではまだ有効になっていませんが、さまざまな種類のリソース用のポータル メニューに表示されるようになります。

特定のリソースから Log Analytics を開くと、自動的にそのリソースのログ レコードのみにスコープが指定されます。   その他のレコードを含むクエリを記述する場合は、Log Analytics または Azure Monitor のメニューから開く必要があります。

次のオプションは、Log Analytics のリソース ビューからはまだ利用できません。

- 保存
- 警告の設定
- クエリ エクスプローラー
- 別のワークスペース/リソースへの切り替え (現時点では未計画)


### <a name="firewall-requirements"></a>ファイアウォールの要件
Log Analytics ページと高度な分析ポータルにアクセスするには、ブラウザーが次のアドレスにアクセスできる必要があります。  ブラウザーがファイアウォールを介して Azure Portal にアクセスしている場合は、これらのアドレスへのアクセスを有効にする必要があります。

| Uri | IP | ポート |
|:---|:---|:---|
| portal.loganalytics.io | 動的 | 80,443 |
| api.loganalytics.io    | 動的 | 80,443 |
| docs.loganalytics.io   | 動的 | 80,443 |


## <a name="log-search-classic"></a>ログ検索 (クラシック)
[Log Analytics] メニューの **[ログ (クラシック)]** または [Azure Monitor] メニューの **[Log Analytics]** からログ検索ページを開きます。 これは Log Analytics クエリを操作するために使用されるクラシック ページであり、上にリストされている [Log Analytics ページ](#log-analytics-page)の追加機能はありません。



![ログ検索ページ](media/log-analytics-log-search-portals/log-search-portal.png)


## <a name="next-steps"></a>次の手順

- [ログ検索の使用に関するチュートリアル](log-analytics-tutorial-viewdata.md)を参照して、クエリ言語を使用してクエリを作成する方法を学習します
- Log Analytics ページと同じエクスペリエンスを提供する[高度な分析ポータルを使用してレッスン](query-language/get-started-analytics-portal.md)を進めます。

