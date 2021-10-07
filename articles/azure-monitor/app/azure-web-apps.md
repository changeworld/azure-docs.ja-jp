---
title: Azure App Services のパフォーマンスを監視する | Microsoft Docs
description: Azure App Services のアプリケーション パフォーマンスの監視。 チャートの読み込みおよび応答時間、依存関係の情報やパフォーマンス警告を設定します。
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 03ee74851f82fd8ff7833fc5d27c901cf0b2317a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788745"
---
# <a name="application-monitoring-for-azure-app-service-overview"></a>Azure App Service のアプリケーションの監視の概要

[Azure App Services](../../app-service/index.yml) 上で実行されている ASP.NET、ASP.NET Core、Java、Node.js ベースの Web アプリケーションで、これまでより簡単に監視を有効にすることができるようになりました。 以前は手動でアプリをインストルメント化する必要がありましたが、最新の拡張機能とエージェントが既定で App Service イメージに組み込まれるようになりました。 

## <a name="enable-application-insights"></a>Application Insights を有効にする

Azure App Services がホストするアプリケーションについてアプリケーション監視を有効にする方法は 2 つあります。

- **エージェントベースのアプリケーション監視** (ApplicationInsightsAgent)。 
 
    - 有効にするにはこの方法が最も簡単であり、コードの変更や高度な構成は不要です。 多くの場合、これは "ランタイム" 監視と呼ばれます。 Azure App Services では、少なくともこのレベルの監視を有効にすることをお勧めします。それから、お客様固有のシナリオに基づいて、手動のインストルメンテーションによる高度な監視が必要かどうかを評価できます。

    - 次がエージェントベースの監視でサポートされています。
        - [.NET Core](./azure-web-apps-net-core.md)
        - [.NET](./azure-web-apps-net.md)
        - [Java](./azure-web-apps-java.md)
        - [Nodejs](./azure-web-apps-nodejs.md)
    
* Application Insights SDK をインストールし、コードを介して **手動でアプリケーションをインストルメント化する方法**。

    * このアプローチはカスタマイズできる部分がはるかに多いのですが、[.NET Core](./asp-net-core.md)、[.NET](./asp-net.md)、[Node.js](./nodejs.md)、[Python](./opencensus-python.md) 用 SDK と、[Java](./java-in-process-agent.md) 用のスタンドアロンのエージェントの各アプローチが必要です。 また、この方法では、最新バージョンのパッケージへの更新を自分で管理する必要があります。

    * エージェントベースの監視の既定ではキャプチャされないイベント/依存関係を追跡するためにカスタム API 呼び出しを行う必要がある場合は、この方法を使用する必要があります。 詳細については、[カスタムのイベントとメトリックのための API に関する記事](./api-custom-events-metrics.md)を参照してください。 

> [!NOTE]
> エージェントベースの監視と手動の SDK ベースのインストルメンテーションの両方が検出された場合、.NET では、手動のインストルメンテーション設定のみが受け入れられます。一方、Java では、エージェントベースのインストルメンテーションのみがテレメトリを出力します。 これは、重複したデータが送信されないようにするためです。

> [!NOTE]
> スナップショット デバッガーとプロファイラーは、.NET と .NET Core でのみ使用できます

## <a name="next-steps"></a>次の手順
- App Service で実行されている [.NET Core](./azure-web-apps-net-core.md)、[.NET](./azure-web-apps-net.md)、[Java](./azure-web-apps-java.md)、または [Nodejs](./azure-web-apps-nodejs.md) アプリケーションに対してエージェントベースのアプリケーション監視を有効にする方法について説明します。 
