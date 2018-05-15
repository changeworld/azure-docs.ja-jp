---
title: Azure コンテナーの監視の概要 | Microsoft Docs
description: この記事では、Azure 内のコンテナーを監視し、クラスターの正常性と可用性をすばやく把握するために Azure で使用できるさまざまな方法の概要を示します。
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2018
ms.author: magoedte
ms.openlocfilehash: 0d511c1f6dfd482e5754741da15b2852ee77c11e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2018
---
# <a name="overview-of-monitoring-containers-in-azure"></a>Azure 内のコンテナーの監視の概要
Azure では、Kubernetes または Docker を実行している Azure コンテナーに展開されているワークロードを効果的に監視し、管理できます。 大規模なシステムに信頼性の高いサービスを提供し、監視計画をサポートするためには、複数のマイクロサービス アプリケーションを含むコンテナーのパフォーマンス状況を把握する必要があります。 この記事では、各機能を理解し、お客様の要件に適切な機能を特定できるよう、Azure の 管理機能および監視機能の概要について説明します。

[Azure Monitor のコンテナーの正常性の監視](monitoring-container-health.md)機能を使用することで、Linux コンテナーのインフラストラクチャのパフォーマンスと正常性を一目で確認でき、問題をすばやく調査できます。 利用統計情報を Log Analytics ワークスペースに格納し、Azure Portal に統合して、ダッシュボードでデータを検索、フィルター処理したり、セグメントごとに集計することで、負荷、パフォーマンスおよび正常性を監視できます。  

ホストしている Azure Kubernetes の外で実行されているコンテナーの場合、Log Analytics [Windows および Docker コンテナー ソリューション](../log-analytics/log-analytics-containers.md)を使用することにより、Windows および Docker コンテナー ホストを確認し管理できます。 Log Analytics ワークスペースのインベントリの詳細、パフォーマンス、およびイベントを、環境内のノードとコンテナーから確認できます。 コンテナーで使用されているコマンドを示す詳細な監査情報を表示したり、中央管理されたログを表示したり検索したりすることで、Docker または Windows ホストに遠隔アクセスしなくても、コンテナーのトラブルシューティングを行えます。

包括的、またはエンド ツー エンドのアプリケーションの監視を実現するためには、Azure またはオンプレミスのリソースなどの依存関係を Azure Monitor または Log Analytics で管理する必要があります。  正常性をより正確に認識するには、Application Insights を使用してプラットフォームとアプリケーション の両方のレベルにアプリケーション レイヤーを追加する必要があります。 プラットフォーム レベルでは、[Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes)、[Docker](https://hub.docker.com/r/microsoft/applicationinsights/)、および [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) 用の Application Insights SDK が使用できます。 マイクロサービス アプリケーションでは、[Java](../application-insights/app-insights-java-get-started.md)、[Node.js](../application-insights/app-insights-nodejs-quick-start.md)、[.Net](../application-insights/app-insights-asp-net.md)、[.Net Core](../application-insights/app-insights-asp-net-core.md) と、多数の[言語/フレームワーク](../application-insights/app-insights-platforms.md)がサポートされています。 

両方のレベルにアプリケーション レイヤーを追加しないと問題が認識されず、アプリケーションの可用性に影響を与え、サービス レベル目標が達成されない可能性があります。  
