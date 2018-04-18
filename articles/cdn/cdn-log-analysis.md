---
title: Azure CDN の使用パターンを分析する | Microsoft Docs
description: この記事では、Azure CDN 製品で使用可能なさまざまな種類の分析レポートについて説明します。
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: rli; v-deasim
ms.openlocfilehash: 3f475c5cc9b766ea9aa5bd39d4a378e8deed5e35
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2018
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Azure CDN の使用パターンを分析する

アプリケーションの CDN を有効にした後、CDN の使用状況の監視、配信の正常性の確認、潜在的な問題のトラブルシューティングを行うことができます。 Azure CDN では、これらの機能が次の方法で利用できます。 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Azure 診断ログによるコア分析

コア分析は、Verizon (Standard/Premium) および Akamai (Standard) の CDN プロファイルに属しているすべての CDN エンドポイントで使用できます。 Azure 診断ログを使用すると、Azure Storage、イベント ハブ、Log Analytics のいずれかにコア分析をエクスポートすることができます。 Log Analytics には、ユーザーによる構成とカスタマイズに対応したグラフを含んだソリューションが用意されています。 詳細については、「[Azure diagnostic logs (Azure 診断ログ)](cdn-azure-diagnostic-logs.md)」を参照してください。

## <a name="verizon-core-reports"></a>Verizon コア レポート

**Azure CDN Standard from Verizon** または **Azure CDN Premium from Verizon** プロファイルを所有する Azure CDN ユーザーは、Verizon 補助ポータルで Verizon コア レポートを表示できます。 Verizon コア レポートには、Azure Portal の **[管理]** オプションからアクセスできます。このレポートには、さまざまなグラフとビューが用意されています。 詳細については、「[Verizon からのコア レポート](cdn-analyze-usage-patterns.md)」を参照してください。

## <a name="verizon-custom-reports"></a>Verizon カスタム レポート

**Azure CDN Standard from Verizon** または **Azure CDN Premium from Verizon** プロファイルを所有する Azure CDN ユーザーは、Verizon 補助ポータルで Verizon カスタム レポートを表示できます。 Verizon カスタム レポートには、Azure Portal の **[管理]** オプションからアクセスできます。 Verizon カスタム レポート ページには、Azure CDN プロファイルに属する各エッジ CName のヒット数や転送されたデータが表示されます。 データは、HTTP 応答コードまたは一定期間にわたるキャッシュの状態でグループ化できます。 詳細については、「[Custom Reports from Verizon (Verizon のカスタム レポート)](cdn-verizon-custom-reports.md)」をご覧ください。

## <a name="verizon-premium-reports"></a>Verizon プレミアム レポート

**Azure CDN Premium from Verizon** で、次のレポートにアクセスすることもできます。
   * [詳細な HTTP レポート](cdn-advanced-http-reports.md)
   * [リアルタイム統計](cdn-real-time-stats.md)
   * [エッジ ノードのパフォーマンス](cdn-edge-performance.md)

