---
title: Azure Application Insights のアプリケーション マップ | Microsoft Docs
description: アプリケーション マップを使用した複雑なアプリケーション トポロジの監視
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 9b39eef5accec4764f61ab31dd894d368242ee3d
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094652"
---
# <a name="application-map-triage-distributed-applications"></a>アプリケーション マップ: 分散アプリケーションのトリアージ

アプリケーション マップを使用すると、分散アプリケーションのすべてのコンポーネントを対象にパフォーマンスのボトルネックや障害のホットスポットを特定できます。 マップ上の各ノードは、アプリケーション コンポーネントまたはその依存関係を表すと共に、正常性の KPI やアラートの状態を保持しています。 任意のコンポーネントをクリックして、さらに詳しい診断結果 (Application Insights イベントなど) にアクセスすることができます。 アプリで Azure サービスを使用している場合は、Azure 診断 (SQL Database アドバイザーのアドバイス情報など) をクリックすることもできます。

## <a name="what-is-a-component"></a>コンポーネントとは

コンポーネントは、分散/マイクロサービス アプリケーションの、個別にデプロイできる部分です。 開発者と運用チームには、これらのアプリケーション コンポーネントによって生成されたテレメトリに対して、コード レベルの可視性またはアクセスがあります。 

* コンポーネントは、チームや組織が (コードやテレメトリに) アクセスできない可能性がある SQL や EventHub などの "観察される" 外部依存関係とは異なります。
* コンポーネントは、任意の数のサーバー/ロール/コンテナーのインスタンス上で実行されます。
* コンポーネントは、個別の Application Insights インストルメンテーション キーである場合も (サブスクリプションが異なる場合も)、単一の Application Insights インストルメンテーション キーにレポートするさまざまなロールである場合もあります。 プレビュー マップのエクスペリエンスには、各コンポーネントがその設定方法に関係なく表示されます。

## <a name="composite-application-map"></a>複合アプリケーション マップ

関連するアプリケーション コンポーネントの階層を横断的に見ながら完全なアプリケーション トポロジを表示できます。 コンポーネントとしては、さまざまな Application Insights リソースや、単一のリソースに含まれる各種ロールが該当します。 アプリ マップでは、Application Insights SDK がインストールされているサーバー間での HTTP 依存関係呼び出しに従ってコンポーネントを検索します。 

このエクスペリエンスでは、最初にコンポーネントが順次検出されます。 アプリケーション マップを初めて読み込むとき、一連のクエリがトリガーされて、対象コンポーネントに関連したコンポーネントが検出されます。 左上隅のボタンには、対象アプリケーション内のコンポーネント数が表示され、その数は、コンポーネントが検出されるにつれて更新されます。 

[マップ コンポーネントの更新] をクリックすると、その時点で検出されたすべてのコンポーネントでマップが更新されます。

すべてのコンポーネントが 1 つの Application Insights リソース内のロールである場合、この検出ステップは不要です。 そのようなアプリケーションでは、そのすべてのコンポーネントが初回読み込みで認識されます。

![アプリケーション マップのスクリーンショット](media/app-insights-app-map/001.png)

このエクスペリエンスが目指す主な目的の 1 つは、何百というコンポーネントを含む複雑なトポロジを視覚化することです。

いずれかのコンポーネントをクリックすると、関連する分析情報を表示したり、そのコンポーネントについてのパフォーマンスと障害のトリアージ機能にアクセスしたりすることができます。

![ポップアップ](media/app-insights-app-map/application-map-001.png)

### <a name="investigate-failures"></a>エラーを調査する

**[investigate failures] (エラーの調査)** を選択すると、[failures] ウィンドウが開きます。

![[investigate failures] (エラーの調査) ボタンのスクリーンショット](media/app-insights-app-map/investigate-failures.png)

![[failures] エクスペリエンスのスクリーンショット](media/app-insights-app-map/failures.png)

### <a name="investigate-performance"></a>パフォーマンスを調査する

パフォーマンスの問題のトラブルシューティングを行うには、**[investigate performance] (パフォーマンスの調査)** を選択します

![[investigate performance] (パフォーマンスの調査) ボタンのスクリーンショット](media/app-insights-app-map/investigate-performance.png)

![[performance] (パフォーマンス) エクスペリエンスのスクリーンショット](media/app-insights-app-map/performance.png)

### <a name="go-to-details"></a>詳細の表示

呼び出し履歴レベルに対して行われたビューを提供できるエンド ツー エンドのトランザクション エクスペリエンスの詳細を表示するには、**[go to details] (詳細の表示)** を選択します。

![[go-to-details] (詳細の表示) ボタンのスクリーンショット](media/app-insights-app-map/go-to-details.png)

![[end-to-end transaction details] (エンドツーエンドのトランザクションの詳細) のスクリーンショット](media/app-insights-app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Analytics での表示

さらにアプリケーション データのクエリと調査を行うには、**[view in analytics] (Analytics で表示)** をクリックします。

![[view in analytics] (Analytics で表示) ボタンのスクリーンショット](media/app-insights-app-map/view-in-analytics.png)

![Analytics エクスペリエンスのスクリーンショット](media/app-insights-app-map/analytics.png)

### <a name="alerts"></a>アラート

アクティブなアラートと、アラートがトリガーされる原因になっている元のルールを表示するには、**[alerts] (アラート)** を選択します。

![[alerts] (アラート) ボタンのスクリーンショット](media/app-insights-app-map/alerts.png)

![Analytics エクスペリエンスのスクリーンショット](media/app-insights-app-map/alerts-view.png)

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>フィードバック
ご意見やご感想は、ポータルのフィードバック オプションからお寄せください。

![MapLink-1 image](./media/app-insights-app-map/13.png)

## <a name="next-steps"></a>次の手順

* [Azure Portal](https://portal.azure.com)
