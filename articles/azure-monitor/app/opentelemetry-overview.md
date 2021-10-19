---
title: Azure Monitor と共に OpenTelemetry を使用する方法の概要
description: Azure Monitor と共に OpenTelemetry を使用する方法の概要について説明します。
ms.topic: conceptual
ms.date: 10/11/2021
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 268d7d8372fef1568a369f956e3caa91749f0e8e
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859583"
---
# <a name="opentelemetry-overview"></a>OpenTelemetry の概要

Microsoft は、テレメトリ インストルメンテーションの将来として [OpenTelemetry](https://opentelemetry.io/) を採用できることを嬉しく思っています。 お客様からはベンダー中立のインストルメンテーションが求められてきており、Microsoft は OpenTelemetry コミュニティと提携して、言語間で一貫した API または SDK を作成することに喜びを感じています。

Microsoft は、以前人気のあった 2 つのオープンソース テレメトリ プロジェクト [OpenCensus](https://opencensus.io/) と [OpenTracing](https://opentracing.io/) のプロジェクト利害関係者と連携して、1 つのプロジェクト OpenTelemetry の作成を支援してきました。 OpenTelemetry には、すべての主要なクラウドおよびアプリケーション パフォーマンス管理 (APM) ベンダーからの貢献が含まれており、Microsoft が Platinum メンバーである [Cloud Native Computing Foundation (CNCF)](https://www.cncf.io/) によって収容されています。

## <a name="concepts"></a>概念

アプリケーションを観察するために収集されるデータであるテレメトリは、次の 3 つの種類または "重要な要素" に分類できます。
1.  分散トレース
2.  メトリック
3.  ログ

最初に、OpenTelemetry コミュニティは分散トレースを取り上げました。 メトリックとログはまだ進行中です。 完全な可観測性ストーリーには 3 つの重要な要素がすべて含まれますが、現在、[.NET、Python、JavaScript 用の Azure Monitor OpenTelemetry ベースのエクスポーター **プレビュー** オファリング](opentelemetry-enable.md)には **分散トレースのみが含まれています**。

この 3 つの重要な要素を詳細に説明しているソースとしては、[OpenTelemetry コミュニティの Web サイト](https://opentelemetry.io/docs/concepts/data-sources/)、[OpenTelemetry の仕様](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/overview.md)、Cindy Sridharan による[分散システムの可観測性](https://www.oreilly.com/library/view/distributed-systems-observability/9781492033431/ch04.html)を含めいくつかあります。

以降のセクションでは、いくつかのテレメトリ収集の基本について説明します。

### <a name="instrumenting-your-application"></a>アプリケーションのインストルメント化

基本的なレベルでは、"インストルメント化" は、単純にアプリケーションでテレメトリをキャプチャできるようにすることです。

アプリケーションをインストルメント化する方法には、次の 2 つがあります。
1.  手動インストルメンテーション
2.  自動インストルメンテーション

手動インストルメンテーションは、OpenTelemetry API に対するコーディングです。 エンドユーザーのコンテキストでは、これは通常、アプリケーションで言語固有の SDK をインストールすることを指します。 手動インストルメンテーション パッケージは、[.NET、Python、JavaScript 用の Azure Monitor OpenTelemetry ベースのエクスポーター **プレビュー** オファリング](opentelemetry-enable.md)で構成されています。

> [!IMPORTANT]
> "手動" は、分散トレースのスパンを定義するための複雑なコードを記述する必要があることを示しているわけでは **ありません** (ただし、これはオプションとして残されています)。 OpenTelemetry のコントリビューターによって管理されている豊富で拡大しているインストルメンテーション ライブラリのセットにより、共通のフレームワークとライブラリ全体でテレメトリ信号を簡単にキャプチャできます。 OpenTelemetry インストルメンテーション ライブラリのサブセットは Azure Monitor によってサポートされ、カスタマー フィードバックによって通知されます。 さらに、最も普及している Azure サービス SDK を OpenTelemetry プロトコル (OTLP) を出力するようにインストルメント化し、それらを Azure Monitor 用に最適化することに取り組んでいます。

これに対して、自動インストルメンテーションでは、アプリケーションのコードに触れることなく、構成を使用してテレメトリ収集を有効にします。 便利ではありますが、構成できなくなる傾向にあり、すべての言語で使用できるわけではありません。 Azure Monitor の OpenTelemetry ベースの自動インストルメンテーション オファリングは [Java 3.X OpenTelemetry ベースの GA オファリング](java-in-process-agent.md)で構成され、カスタマー フィードバックによって通知されるよう Microsoft はここに引き続き投資しています。 OpenTelemetry コミュニティは C# と Python での自動インストルメンテーションも実験していますが、Azure Monitor は、近い将来における単純で、かつ効率的な手動インストルメンテーション ストーリーの作成に焦点を絞っています。

### <a name="sending-your-telemetry"></a>テレメトリの送信

Azure Monitor (または、いずれかのベンダー) にデータを送信する方法にも、次の 2 つがあります。
1. 直接エクスポーター
2. エージェント経由

直接エクスポーターでは、インプロセス テレメトリを (アプリケーションのコードから) Azure Monitor のインジェスト エンドポイントに直接送信します。 このアプローチの主な利点は、オンボードのシンプルさです。

**Azure Monitor の現在サポートされている OpenTelemetry ベースのオファリングはすべて、直接エクスポーターを使用します**。 

別の方法として、エージェント経由でテレメトリを送信すると、OpenTelemetry でサポートされている任意の言語で [OTLP](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/protocol/README.md) を使用して Azure Monitor に送信するためのパスが提供されます。 これにより、お客様は、[サポートされている言語](platforms.md)以外の言語で記述されたアプリケーションを監視できるようになります。 

> [!NOTE]
> Microsoft がアプリケーション監視のための "エージェント経由" のアプローチをまだ正式にサポートしていないにもかかわらず、一部のお客様は、エージェントの代わりとして [OpenTelemetry-Collector](https://github.com/open-telemetry/opentelemetry-collector/blob/main/docs/design.md) を使用し始めています。 その間、オープン ソース コミュニティは OpenTelemetry-Collector Azure Monitor エクスポーターを提供してきました。一部のお客様は、これを使用してデータを Azure Monitor Application Insights に送信しています。

## <a name="terms"></a>用語

OpenTelemetry の仕様にある[用語集](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/glossary.md)を参照してください。

Application Insights の従来の用語のいくつかは、業界が OpenTelemetry に集中するに従って紛らわしくなっています。 次の表では、これらの違いを強調しています。 最終的に、Application Insights の用語は OpenTelemetry の用語によって置き換えられます。

Application Insights | OpenTelemetry
------ | ------
トレース   | ログ
チャネル   | エクスポーター  
コード不要/エージェント ベース   | 自動インストルメンテーション

## <a name="next-step"></a>次のステップ

次のページは、Microsoft の OpenTelemetry ベースのオファリングを有効にして構成するための言語ごとのガイダンスで構成されています。 重要なこととして、OpenTelemetry が各プロジェクトに適切かどうかを判定できるように、Microsoft は各オファリングの使用可能な機能や制限事項を共有しています。
- [.NET](opentelemetry-enable.md) 
- [Java](java-in-process-agent.md)
- [JavaScript](opentelemetry-enable.md)
- [Python](opentelemetry-enable.md)
