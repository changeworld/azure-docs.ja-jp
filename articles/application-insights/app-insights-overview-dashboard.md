---
title: Azure Application Insights の概要ダッシュボード | Microsoft Docs
description: Azure の Application Insights と概要ダッシュボードの機能を使用してアプリケーションを監視します。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mbullwin
ms.openlocfilehash: 0be54c47965c6a27c3506fd37a7bf67e4b2b7924
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356088"
---
# <a name="application-insights-overview-dashboard-preview"></a>Application Insights の概要ダッシュボード (プレビュー)

Application Insights では常に、アプリケーションの正常性とパフォーマンスを一目で評価できる概要ウィンドウが提供されてきました。 新しいプレビューの概要ダッシュボードでは、より高速で柔軟なエクスペリエンスが提供されます。

## <a name="how-do-i-test-out-the-new-experience"></a>新しいエクスペリエンスをテストする方法

 Application Insights の _[概要]_ で、_[Please try new Overview before it becomes the default experience]\(既定のエクスペリエンスになる前に、新しい概要をお試しください\)_ を選択します。

![概要プレビュー](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-01.png)

これにより、新しい既定の概要ダッシュボードが表示されます。

![概要プレビュー ウィンドウ](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-02.png)

## <a name="better-performance"></a>パフォーマンスの向上

時間範囲の選択が、簡単な 1 クリックのインターフェイスに簡素化されました。

![時間範囲](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-03.png)

全体的なパフォーマンスが大幅に向上しました。 既定の動的に更新される各 KPI タイルは、Application Insights の対応する機能にリンクされています。 たとえば、失敗した要求を選ぶと、_[失敗]_ ウィンドウが開きます。

![エラー](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>アプリケーション ダッシュボード

アプリケーション ダッシュボードは、Azure が備える既存のダッシュボード テクノロジを利用して、アプリケーションの正常性とパフォーマンスに関する完全にカスタマイズ可能な単一のウィンドウ ビューを提供します。

既定のダッシュボードにアクセスするには、左上隅の _[アプリケーション ダッシュボード]_ を選びます。

![ダッシュボード ビュー](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-05.png)

初めてダッシュボードにアクセスした場合は、既定のビューが表示されます。

![ダッシュボード ビュー](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-06.png)

必要な場合は既定のビューのままにすることもできますが、ダッシュボードの要素を追加および削除して、チームのニーズに合わせることもできます。

> [!NOTE]
> Application Insights のリソースにアクセスできるすべてのユーザーが、同じアプリケーション ダッシュボードのエクスペリエンスを共有します。 1 人のユーザーによって行われた変更が、すべてのユーザーのビューを変更します。

選択するだけで、概要エクスペリエンスに戻ります。

![[概要] ボタン](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-07.png)

## <a name="next-steps"></a>次の手順

- [ファネル](usage-funnels.md)
- [保持](app-insights-usage-retention.md)
- [ユーザー フロー](app-insights-usage-flows.md)
- [ダッシュボード](app-insights-dashboards.md)
