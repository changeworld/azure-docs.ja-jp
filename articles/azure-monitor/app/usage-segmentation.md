---
title: Application Insights でのユーザー、セッション、およびイベント分析
description: Web アプリのユーザーの統計分析について説明します。
ms.topic: conceptual
author: mattmccleary
ms.author: mmcc
ms.date: 07/30/2021
ms.openlocfilehash: d3b6bac65cea13de078d91a6936aa8618d9f7423
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132666"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Application Insights でのユーザー、セッション、およびイベントの分析

ユーザーが Web アプリをいつ使い、どのページに最も興味があり、ユーザーがどこにいて、どのようなブラウザーやオペレーティング システムを使っているかを確認しましょう。 [Application Insights](./app-insights-overview.md) サービスを使用してビジネスおよび使用状況テレメトリを分析します。

:::image type="content" source="./media/usage-segmentation/users.png" alt-text="面グラフが表示されている [ユーザー] タブの画面キャプチャ。" lightbox="./media/usage-overview/users.png":::

## <a name="get-started"></a>はじめに

Application Insights ポータルのユーザー、セッション、またはイベント ブレードにデータがまだ表示されていない場合は、[利用状況ツールの使用方法を確認](usage-overview.md)してください。

## <a name="the-users-sessions-and-events-segmentation-tool"></a>ユーザー、セッション、およびイベント セグメンテーション ツール

3 つの使用状況ブレードは同じツールを使用して、これら 3 つの視点から、Web アプリのテレメトリを詳細に分析します。 データをフィルター処理および分割することによって、さまざまなページや機能の対象の使用状況に関する洞察を得ることができます。

* **ユーザー ツール**:アプリとその機能を使用したユーザーの数。  ブラウザーの Cookie に格納されている匿名 ID を使用してユーザーがカウントされます。 複数のブラウザーまたはコンピューターを使用する 1 人のユーザーは、複数のユーザーとしてカウントされます。
* **セッション ツール**:アプリの特定のページおよび機能を含むユーザー アクティビティのセッション数。 セッションは、ユーザーの非アクティブ状態が 30 分続いた後、または継続した 24 時間の使用の後、カウントされます。
* **イベント ツール**:アプリの特定のページと機能が使用された回数。 ページ ビューは、([インストルメント化した場合](./javascript.md)) ブラウザーがアプリからページを読み込むときにカウントされます。 

    カスタム イベントは、アプリで実行される操作の 1 回の出現を表します。通常、ボタン選択、タスクの完了などのユーザー操作です。 コードをアプリに挿入して、[カスタム イベントを生成](./api-custom-events-metrics.md#trackevent)します。

## <a name="querying-for-certain-users"></a>特定のユーザーのクエリ実行

[ユーザー] ツールの上部にあるクエリ オプションを調整して、さまざまなユーザー グループを調査します。

- 期間:時間範囲を選択します。
- 表示:分析するユーザーのコーホートを選択します。
- 次を使用した: カスタム イベント、要求、ページ ビューを選択します。
- イベント: 複数のイベント、要求、ページ ビューを選択します。これにより、必ずしも選択したものすべてではなく、少なくとも 1 つを実行したユーザーが表示されます。
- 値による x 軸: 時間の範囲によって、または、ブラウザーや市などの別のプロパティによってデータをバケットする方法を選択します。
- 次で分割:データの分割またはセグメント化に使用するプロパティを選択します。 
- フィルターの追加:ブラウザー、市などのプロパティに基づいて特定のユーザー、セッション、またはイベントに対するクエリ実行を制限します。 
 
## <a name="meet-your-users"></a>ユーザーを確認する

**[Meet your users]\(ユーザーを確認する\)** セクションには、現在のクエリに一致する 5 人のサンプル ユーザーについての情報が表示されます。 個人の行動と全体の行動を調査することで、ユーザーが実際にアプリをどのように使用しているかについての分析情報が得られます。

## <a name="next-steps"></a>次のステップ

- 使用状況を把握できるようにするには、[カスタム イベント](./api-custom-events-metrics.md#trackevent)または[ページ ビュー](./api-custom-events-metrics.md#page-views)の送信を開始します。
- カスタム イベントまたはページ ビューを既に送信した場合は、使用状況ツールを見て、自分のサービスがユーザーにどのように使用されているかを把握します。
    - [ファネル](usage-funnels.md)
    - [保持](usage-retention.md)
    - [ユーザー フロー](usage-flows.md)
    - [ブック](../visualize/workbooks-overview.md)
    - [ユーザー コンテキストの追加](./usage-overview.md)