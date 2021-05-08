---
title: マッピング データ フロー変換の概要
description: データ フローのマッピングで使用できる、さまざまな変換の概要
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: bb5021c0125c3140ef44a1ec3304b9d0ac40c30f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060229"
---
# <a name="mapping-data-flow-transformation-overview"></a>マッピング データ フロー変換の概要

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

データ フローのマッピングで現在サポートされている変換のリストを次に示します。 それぞれの変換をクリックして、構成の詳細を確認します。

| Name | カテゴリ | 説明 |
| ---- | -------- | ----------- |
| [集計](data-flow-aggregate.md) | スキーマ修飾子 | 既存の列または計算列によってグループ化される、SUM、MIN、MAX、COUNT などのさまざまな種類の集計を定義できます。 | 
| [行の変更](data-flow-alter-row.md) | 行の修飾子 | 行に対して、挿入、削除、更新および upsert の各ポリシーを設定します。 |
| [条件分割](data-flow-conditional-split.md) | 複数の入力/出力 | 一致条件に基づいて、データの行を異なるストリームにルートします。 |
| [派生列](data-flow-derived-column.md) | スキーマ修飾子 | データ フローの言語を使用して、新しい列を生成するか、既存のフィールドを変更します。 | 
| [Exists](data-flow-exists.md) | 複数の入力/出力 | お使いのデータが別のソースまたはストリームに存在するかどうかを確認します。 | 
| [Assert](data-flow-filter.md) | 行の修飾子 | 条件に基づいて、行をフィルターします。 |
| [フラット化](data-flow-flatten.md) | スキーマ修飾子 |  JSON などの階層構造体の中で配列値を取得し、それらを個々の行に展開します。 |
| [Join](data-flow-join.md) | 複数の入力/出力 |  2 つのソースまたはストリームのデータを結合します。 |
| [Lookup](data-flow-lookup.md) | 複数の入力/出力 | 別のソースからデータを参照します。 |
| [新しいブランチ](data-flow-new-branch.md) | 複数の入力/出力 | 同じデータ ストリームに対して複数の操作と変換を行います。 |
| [Parse](data-flow-new-branch.md) | フォーマッタ | JSON、区切りテキスト、または XML 形式のテキストの文字列であるデータ ストリーム内のテキスト列を解析します。 |
| [ピボット](data-flow-pivot.md) | スキーマ修飾子 | 1 つまたは複数のグループ化された列が、個々の列に変換された個別の行の値を持つ集計。 |
| [順位](data-flow-rank.md) | スキーマ修飾子 | 並べ替え条件に基づいて順序付けされた順位付けを生成する |
| [Select](data-flow-select.md) | スキーマ修飾子 | 別名列とストリーム名、列のドロップまたは並べ替え |
| [Sink](data-flow-sink.md) | - | お使いのデータの最終受信先 |
| [Sort](data-flow-sort.md) | 行の修飾子 | 現在のデータ ストリームで受信行を並べ替える |
| [ソース](data-flow-source.md) | - | データ フローのデータ ソース |
| [代理キー](data-flow-surrogate-key.md) | スキーマ修飾子 | ビジネス以外の任意のキー値のインクリメントを追加する |
| [Union (結合)](data-flow-union.md) | 複数の入力/出力 | 複数のデータ ストリームを垂直方向に結合する |
| [ピボット解除](data-flow-unpivot.md) | スキーマ修飾子 | 列を行の値にピボットする |
| [ウィンドウ](data-flow-window.md) | スキーマ修飾子 |  データ ストリーム内の列のウィンドウベースの集計を定義します。 |
| [Parse](data-flow-parse.md) | スキーマ修飾子 |  列データを Json または区切りテキストに解析します |
