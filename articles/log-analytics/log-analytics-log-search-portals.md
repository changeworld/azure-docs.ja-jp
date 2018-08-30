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
ms.date: 08/20/2018
ms.author: magoedte; bwren
ms.component: na
ms.openlocfilehash: 386aad94461fa3f2ceafb7564342797eefa2f086
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246706"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>Log Analytics におけるデータの表示と分析
Azure portal には、Log Analytics に保存されたデータの分析とアドホック分析用のクエリ作成に使用できる 2 つのオプションがあります。 これらのポータルを使用して作成したクエリは、アラートやダッシュボードなどの他の機能で使用できます。

## <a name="log-analytics-page-preview"></a>Log Analytics ページ (プレビュー)
[Log Analytics] メニューの **[Logs (プレビュー)]** から Log Analytics ページを開きます。 これは、ログ データ操作とクエリ作成の新しいエクスペリエンスです。 「[Get started with the Log Analytics page in the Azure portal (Azure portal の Log Analytics ページの概要)](query-language/get-started-analytics-portal.md)」で、このポータルの概要を参照して、その機能を確認できます。

Log Analytics ページは、[ログ検索](#log-search)エクスペリエンスに比べて、次の機能が強化されています。

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


### <a name="firewall-requirements"></a>ファイアウォールの要件
Log Analytics ページと高度な分析ポータルにアクセスするには、ブラウザーが次のアドレスにアクセスできる必要があります。  ブラウザーがファイアウォールを介して Azure Portal にアクセスしている場合は、これらのアドレスへのアクセスを有効にする必要があります。

| Uri | IP | ポート |
|:---|:---|:---|
| portal.loganalytics.io | 動的 | 80,443 |
| api.loganalytics.io    | 動的 | 80,443 |
| docs.loganalytics.io   | 動的 | 80,443 |


## <a name="log-search"></a>ログ検索
[Log Analytics] メニューの **[ログ]** または [Azure Monitor] メニューの **[Log Analytics]** からログ検索ページを開きます。 これは、基本的なクエリを使用してログ データを分析するのに適しています。 クエリ言語について熟知していなくても使用できるクエリ編集機能が複数用意されています。  こうした機能の概要については、「[ログ検索を使用して Azure Log Analytics でログ検索を作成する](log-analytics-log-search-log-search-portal.md)」を参照してください。 


![ログ検索ページ](media/log-analytics-log-search-portals/log-search-portal.png)


## <a name="next-steps"></a>次の手順

- [ログ検索の使用に関するチュートリアル](log-analytics-tutorial-viewdata.md)を参照して、クエリ言語を使用してクエリを作成する方法を学習します
- Log Analytics ページと同じエクスペリエンスを提供する[高度な分析ポータルを使用してレッスン](query-language/get-started-analytics-portal.md)を進めます。

