---
title: Azure SignalR とは | Microsoft Docs
description: Azure SignalR サービスの概要。
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/17/2018
ms.author: wesmc
ms.openlocfilehash: bc144fb1d7db9251871e7e181b012417a32de7e6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
ms.locfileid: "33868111"
---
# <a name="what-is-azure-signalr-service"></a>Azure SignalR サービスとは

Microsoft Azure SignalR サービスは現在、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の段階です。

Azure SignalR サービスは、[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) に基づく Azure サービスです。 ASP.NET Core SignalR は、リアルタイムの Web 機能を HTTP 経由でアプリケーションに追加するプロセスを簡略化する[オープン ソースのライブラリ](https://github.com/aspnet/signalr)です。 このリアルタイム機能により、Web サーバーは接続されているクライアントにコンテンツの更新をプッシュできます。 その結果、クライアントは、サーバーをポーリングしたり更新プログラムについて新しい HTTP 要求を送信したりしなくても更新されます。

この記事では、Azure SignalR サービスの概要について説明します。 作業を開始する場合は、[ASP.NET Core のクイック スタート](signalr-quickstart-dotnet-core.md)から開始します。

## <a name="what-is-signalr-service-used-for"></a>SignalR サービスの用途 

リアルタイムのコンテンツ更新を必要とするアプリケーションの種類は多数あります。 次のアプリケーションの種類の例は、Azure SignalR サービスの使用に適しています。

* サーバーからの頻繁な更新が必要なアプリ。 たとえば、ゲーム、ソーシャル ネットワーク、投票、オークション、マップ、GPS などのアプリです。
* ダッシュボードと監視アプリ。 たとえば、会社のダッシュボード、売上の即時更新、トラベル アラートなどです。
* コラボレーション アプリ。 ホワイトボード アプリとチーム会議ソフトウェアは、コラボレーション アプリの例です。
* 通知を必要とするアプリ。 ソーシャル ネットワーク、電子メール、チャット、ゲーム、トラベル アラート、その他の多くのアプリは通知を使用します。

内部的には、SignalR はリアルタイム Web アプリケーションのビルドに使用される多くの手法の抽象化です。 [WebSocket](https://wikipedia.org/wiki/WebSocket) が最適なトランスポートですが、他のオプションを使用できないときは、[Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) や長いポーリングなどの他の手法が使用されます。 SignalR はサーバーとクライアントでサポートされる機能に基づいて、適切なトランスポートを自動的に検出し、初期化します。

## <a name="developing-signalr-apps"></a>SignalR のアプリの開発

現時点では、Web アプリケーションで使用できる SignalR には 2 つのバージョンがあります。SignalR for ASP.NET と、最新バージョンの ASP.NET Core SignalR です。 Azure SignalR サービスは、ASP.NET Core SignalR 上に構築された Azure のマネージド サービスです。 

ASP.NET Core SignalR は、以前のバージョンのリライトです。 そのため、ASP.NET Core SignalR は、以前のバージョンの SignalR と下位互換性がありません。 API および動作が異なります。 ASP.NET Core SignalR SDK は .NET Standard なので、.NET Framework と共に使用することができます。 ただし、以前の API ではなく新しい API を使用する必要があります。 SignalR を使用していて、ASP.NET Core SignalR または Azure SignalR サービスに移行する場合は、API の違いを処理するためにコードを変更する必要があります。

Azure SignalR サービスでは、ASP.NET Core SignalR のサーバー側コンポーネントは Azure にホストされます。 ただし、テクノロジは ASP.NET Core 上に組み込まれているので、[Azure App Service](../app-service/app-service-web-overview.md)、[IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index)、[Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx)、[Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache)、[Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index) でホストしているときに、複数のプラットフォーム (Windows、Linux、MacOS) で実際の Web アプリケーションを実行できます。 独自のプロセスで自己ホストを使用することもできます。

アプリケーションの目標に、リアルタイムでのコンテンツ更新により Web クライアントを更新するための最新機能のサポート、複数のプラットフォーム (Azure、Windows、Linux、MacOS) での実行、異なる環境へのホストが含まれる場合は、Azure SignalR サービスの利用が最適な選択肢である可能性があります。


## <a name="why-not-deploy-signalr-myself"></a>なぜ SignalR を自分でデプロイしないのか

ASP.NET Core SignalR をバックエンド コンポーネントとしてサポートする独自の Azure Web アプリを全体的な Web アプリケーションにデプロイするアプローチは依然として有効です。

Azure SignalR サービスを使用する主な理由の 1 つは、シンプルさです。 Azure SignalR サービスでは、パフォーマンス、スケーラビリティ、可用性などの問題を処理する必要がありません。 これらの問題は、99.9% のサービス レベル アグリーメントで対処されます。

また、WebSocket は、通常はリアルタイムのコンテンツ更新をサポートするための優先手法です。 ただし、多数の永続的な WebSocket 接続の負荷分散は、スケーリングの際に解決するのが複雑な問題になります。 一般的なソリューションでは、DNS 負荷分散、ハードウェア ロード バランサー、ソフトウェア負荷分散を利用します。 Azure SignalR サービスがこの問題を処理します。

別の理由は、Web アプリケーションを実際にホストする必要がまったくない可能性があることです。 Web アプリケーションのロジックでは、[サーバーレス コンピューティング](https://azure.microsoft.com/overview/serverless-computing/)を利用できます。 たとえば、[Azure Functions](https://docs.microsoft.com/azure/azure-functions/) トリガーでコードをオンデマンドでのみホストおよび実行できる可能性があります。 このシナリオは、コードがオンデマンドでのみ実行され、クライアントとの接続が長時間維持されないため、注意が必要になる可能性があります。 Azure SignalR サービスでは、サービスが既に接続を管理しているため、このような状況に対処できます。

## <a name="how-does-it-scale"></a>スケーリングの方法

通常は、SQL Server、Azure Service Bus、または Redis Cache で SignalR をスケーリングします。 Azure SignalR サービスによってスケーリング アプローチが処理されます。 パフォーマンスとコストはこれらのアプローチと同等で、これらの他のサービスを扱う複雑さを伴いません。 行う必要があるは、サービスのユニット数の更新だけです。 各サービス ユニットでは、最大 1000 のクライアント接続がサポートされます。

## <a name="next-steps"></a>次の手順
* [クイック スタート: Azure SignalR でのチャット ルームの作成](signalr-quickstart-dotnet-core.md)  
  

