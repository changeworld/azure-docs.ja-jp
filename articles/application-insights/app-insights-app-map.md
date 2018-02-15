---
title: "Azure Application Insights のアプリケーション マップ | Microsoft Docs"
description: "アプリケーション マップを使用した複雑なアプリケーション トポロジの監視"
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 3bbed59bf93eab5e729fbdd3ccae04599ac47081
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2018
---
# <a name="application-map-triage-distributed-applications"></a>アプリケーション マップ: 分散アプリケーションのトリアージ
アプリケーション マップを使用すると、分散アプリケーションのすべてのコンポーネントを対象にパフォーマンスのボトルネックや障害のホットスポットを特定できます。 マップ上の各ノードは、アプリケーション コンポーネントまたはその依存関係を表すと共に、正常性の KPI やアラートの状態を保持しています。 任意のコンポーネントをクリックして、さらに詳しい診断結果 (Application Insights イベントなど) にアクセスすることができます。 アプリで Azure サービスを使用している場合は、Azure 診断 (SQL Database アドバイザーのアドバイス情報など) をクリックすることもできます。

## <a name="what-is-a-component"></a>コンポーネントとは

コンポーネントは、分散/マイクロサービス アプリケーションの、個別にデプロイできる部分です。 開発者と運用チームには、これらのアプリケーション コンポーネントによって生成されたテレメトリに対して、コード レベルの可視性またはアクセスがあります。 

* コンポーネントは、チームや組織が (コードやテレメトリに) アクセスできない可能性がある SQL や EventHub などの "観察される" 外部依存関係とは異なります。
* コンポーネントは、任意の数のサーバー/ロール/コンテナーのインスタンス上で実行されます。
* コンポーネントは、個別の Application Insights インストルメンテーション キーである場合も (サブスクリプションが異なる場合も)、単一の Application Insights インストルメンテーション キーにレポートするさまざまなロールである場合もあります。 プレビュー マップのエクスペリエンスには、各コンポーネントがその設定方法に関係なく表示されます。

## <a name="composite-application-map-preview"></a>複合アプリケーション マップ (プレビュー)
*このマップは、まだ初期段階のプレビューであり、今後さまざまな機能が追加される予定です。新しいエクスペリエンスについてのフィードバックをぜひお寄せください。プレビューとクラシックのエクスペリエンスは簡単に切り替えることができます。*

[プレビューの一覧](app-insights-previews.md)から [複合アプリケーション マップ] を有効にするか、または右上隅にあるトグル ボタンで [プレビュー マップ] をクリックします。 このトグル ボタンを使用して、元のクラシック エクスペリエンスに切り替えることができます。
![プレビュー マップの有効化](media/app-insights-app-map/preview-from-classic.png)

>[!Note]
このプレビューは、以前の "複数ロールのアプリケーション マップ" プレビューに代わるものです。 現時点では、いくつもの階層にわたるアプリケーション コンポーネントの依存関係も含めてトポロジ全体を表示するには、こちらを使用してください。 皆様のフィードバックをお待ちしております。クラシック マップでサポートされていたものと同様の機能を追加していく予定です。

関連するアプリケーション コンポーネントの階層を横断的に見ながら完全なアプリケーション トポロジを表示できます。 コンポーネントとしては、さまざまな Application Insights リソースや、単一のリソースに含まれる各種ロールが該当します。 アプリ マップでは、Application Insights SDK がインストールされているサーバー間での HTTP 依存関係呼び出しに従ってコンポーネントを検索します。 

このエクスペリエンスでは、最初にコンポーネントが順次検出されます。 プレビューを初めて読み込むとき、一連のクエリがトリガーされて、対象コンポーネントに関連したコンポーネントが検出されます。 左上隅のボタンには、対象アプリケーション内のコンポーネント数が表示され、その数は、コンポーネントが検出されるにつれて更新されます。 
![プレビュー マップ](media/app-insights-app-map/preview.png)

[マップ コンポーネントの更新] をクリックすると、その時点で検出されたすべてのコンポーネントでマップが更新されます。
![読み込み済みマップのプレビュー](media/app-insights-app-map/components-loaded-hierarchical.png)

すべてのコンポーネントが 1 つの Application Insights リソース内のロールである場合、この検出ステップは不要です。 そのようなアプリケーションでは、そのすべてのコンポーネントが初回読み込みで認識されます。

この新しいエクスペリエンスが目指す主な目的の 1 つは、何百というコンポーネントを含んだ複雑なトポロジを視覚化することです。 新たにズームがサポートされ、拡大するにつれて情報の詳細度が増していきます。 縮小すると、一度に表示されるコンポーネントが増え、失敗率が突出して高いコンポーネントを特定することができます。 

![ズーム レベル](media/app-insights-app-map/zoom-levels.png)

いずれかのコンポーネントをクリックすると、関連する分析情報を表示したり、そのコンポーネントについてのパフォーマンスと障害のトリアージ機能にアクセスしたりすることができます。

![ポップアップ](media/app-insights-app-map/preview-flyout.png)


## <a name="classic-application-map"></a>クラシック アプリケーション マップ

マップには次の情報が表示されます。

* 可用性テスト
* クライアント側コンポーネント (JavaScript SDK で監視)
* サーバー側のコンポーネント
* クライアント コンポーネントとサーバー コンポーネントの依存関係

![app map](./media/app-insights-app-map/02.png)

依存関係リンク グループは、展開したり折りたたんだりすることができます。

![collapse](./media/app-insights-app-map/03.png)

特定の種類 (SQL、HTTP など) の依存関係が多数存在する場合、それらがグループ化されて表示される場合があります。 

![grouped dependencies](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>問題の特定
それぞれのノードには、関連するパフォーマンス指標 (対応するコンポーネントの負荷、パフォーマンス、エラー率など) が表示されます。 

問題のリスクは、警告アイコンによって強調表示されます。 オレンジ色の警告は、要求、ページ ビュー、依存関係の呼び出しにおけるエラーの存在を意味します。 赤色は、エラー率が 5% を超えていることを示します。 このしきい値を調整する場合は、[オプション] を開きます。

![failure icons](./media/app-insights-app-map/04.png)

アクティブな警告も表示されます。 

![active alerts](./media/app-insights-app-map/05.png)

SQL Azure を使用している場合、パフォーマンスを高める方法についての推奨事項が存在するときにアイコンが表示されます。 

![Azure recommendation](./media/app-insights-app-map/06.png)

アイコンをクリックすると、詳しい情報にアクセスできます。

![Azure recommendation](./media/app-insights-app-map/07.png)

## <a name="diagnostic-click-through"></a>診断のリンク
マップ上の各ノードには、診断を目的としたリンクが表示されます。 表示されるオプションは、ノードの種類によって異なります。

![server options](./media/app-insights-app-map/09.png)

Azure でホストされるコンポーネントの場合、そのコンポーネントに直接アクセスするためのリンクがオプションに含まれます。

## <a name="filters-and-time-range"></a>フィルターと時間範囲
既定では、選択した時間範囲に関して入手できるすべてのデータがマップに集約されます。 ただしフィルターを適用することで、表示内容を特定の操作名や依存関係に限定することができます。

* 操作名: ページ ビューのほか、サーバー側の要求の種類が表示対象となります。 このオプションでは、選択した操作に限定して、サーバー/クライアント側ノードの KPI がマップに表示されます。 その特定の操作のコンテキストで呼び出された依存関係が表示されます。
* 依存関係のベース名: AJAX ブラウザーの依存関係とサーバー側の依存関係が表示対象となります。 TrackDependency API を使ってカスタムの依存関係のテレメトリを報告対象にしている場合、それらもここに表示されます。 マップに表示する依存関係は選択できます。 現時点では、この選択によって、サーバー側の要求やクライアント側のページ ビューはフィルタリングされません。

![Set filters](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>フィルターの保存
適用したフィルターを保存するには、フィルタリングされたビューを [ダッシュボード](app-insights-dashboards.md)にピン留めします。

![[ダッシュボードにピン留めする]](./media/app-insights-app-map/12.png)

## <a name="error-pane"></a>エラー ウィンドウ
マップ内でノードをクリックすると、そのノードのエラーの概要を示すエラー ウィンドウが右側に表示されます。 エラーは、まず操作 ID でグループ化された後、問題 ID でグループ化されます。

![エラー ウィンドウ](./media/app-insights-app-map/error-pane.png)

エラーをクリックすると、そのエラーの最新のインスタンスが表示されます。

## <a name="resource-health"></a>リソース ヘルス
リソースの種類によっては、エラー ウィンドウの上部にリソースの正常性が表示されます。 たとえば、SQL ノードをクリックすると、データベースの正常性と発生したアラートが表示されます。

![リソース ヘルス](./media/app-insights-app-map/resource-health.png)

リソース名をクリックすると、そのリソースの標準の概要メトリックを表示できます。

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>フィードバック
ご意見やご感想は、ポータルのフィードバック オプションからお寄せください。

![MapLink-1 image](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>次の手順

* [Azure Portal](https://portal.azure.com)
