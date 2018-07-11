---
title: Azure Redis Cache とは | Microsoft Docs
description: Azure Redis Cache の概要と一般的な使用方法について説明します。
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: overview
ms.date: 03/26/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 585dcd120c42562b1520d4454f9d04e445553101
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096194"
---
# <a name="what-is-azure-redis-cache"></a>Azure Redis Cache とは

Azure Redis Cache は広く普及しているオープン ソース [Redis Cache](https://redis.io/)を基盤にしています。 Azure Redis Cache は通常、バックエンドのデータストアに大きく依存するシステムのパフォーマンスとスケーラビリティを向上させるためのキャッシュとして使用されます。 アプリケーションに近い場所にある高速ストレージに、アクセス頻度が高いデータを一時的にコピーすることで、パフォーマンスが向上します。 [Redis のキャッシュ](https://redis.io/) を使用すると、この高速ストレージは、データベースによってディスクから読み込まれずに、Redis Cache を利用したメモリ内に配置されます。

また、Azure Redis Cache は、メモリ内のデータ構造ストア、非リレーショナル データベース、およびメッセージ ブローカーとして使用することも可能です。 Redis エンジンの短い待機時間、高スループットのパフォーマンスを活用して、アプリケーションのパフォーマンスが向上します。

Azure Redis Cache を使用すると、セキュリティで保護された専用の Redis のキャッシュにアクセスできます。このキャッシュは Microsoft で管理されており、Azure 内でホストされ、Azure 内外の任意のアプリケーションにアクセスできます。

## <a name="why-use-azure-redis-cache"></a>Azure Redis Cache を使用する理由

アプリケーションのアーキテクチャをサポートするため、またはアプリケーションのパフォーマンスを向上させるために、Redis Cache が使用される多数の共通パターンがあります。 次に、最も一般的なものをいくつか示します。

| パターン      | 説明                                        |
| ------------ | -------------------------------------------------- |
| [キャッシュ アサイド](cache-web-app-cache-aside-leaderboard.md) | データベースが大規模になる場合があるため、データベース全体をキャッシュに読み込む方法は、推奨されません。 [キャッシュ アサイド](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) パターンを使用して、必要な場合にだけデータ項目をキャッシュに読み込むのが一般的です。 システムでバックエンド データへの変更が行われた場合、その時点で、他のクライアントと共に配布されるキャッシュも更新できます。 さらに、システムでは、データ項目の有効期限を設定したり、削除ポリシーを使用してデータを更新し、キャッシュへの再読み込みを行ったりすることが可能です。|
| [コンテンツ キャッシュ](cache-aspnet-output-cache-provider.md) | ほとんどの Web ページは、ヘッダー、フッター、ツールバー、メニューなどを含むテンプレートから生成されます。テンプレートは、実際には頻繁に変更されることはなく、動的に生成されるものではありません。 Azure Redis Cache などのメモリ内キャッシュを使用すると、バックエンド データストアと比較して、Web サーバーからこの種類の静的コンテンツに迅速にアクセスできます。 このパターンでは、コンテンツを動的に生成するために必要な処理時間とサーバー負荷を軽減します。 これにより、Web サーバーの応答性が高まり、負荷に対処するために必要なサーバー数を減らすことができます。 Azure Redis Cache は、このパターンをサポートするための Redis Output Cache Provider を ASP.NET に提供しています。|
| [ユーザー セッション キャッシュ](cache-aspnet-session-state-provider.md) | このパターンは通常、Web アプリケーションでユーザーの Cookie に関連付けられるショッピング カートなどのユーザー履歴の種類情報に使用されます。 Cookie に格納する情報量が多すぎると、Cookie のサイズが大きくなり、大きなサイズ のCookie が要求ごとに渡されたり検証されたりするため、パフォーマンスに悪影響を及ぼす場合があります。 一般的なソリューションとしては、バックエンド データベース内のデータにクエリを実行するためのキーとして、Cookie を使用します。 ユーザーに情報を関連付けるために、Azure Redis Cache などのメモリ内キャッシュを使用すると、完全なリレーショナル データベースを操作するよりも、ずっと高速になります。 |
| ジョブおよびメッセージ キュー | アプリケーションが要求を受け取ると、多くの場合、要求に関連付けられている処理を実行するために、追加の時間がかかります。 キューに追加することで実行時間の長い操作を延期するのが、一般的なパターンです。このような要求は、場合によっては他のサーバーで、後から処理します。 作業を延期するこの手法は、タスク キューと呼ばれます。 タスク キューをサポートするために設計された多数のソフトウェア コンポーネントがあります。 また、Redis Cache では、この目的のために、分散トランザクションも提供しています。|
| 分散トランザクション | アプリケーションが、バックエンド データストアに対する一連のコマンドを単一の操作 (アトミック) として実行できることが、一般的な要件です。 すべてのコマンドが成功する必要があり、成功しなかった場合はすべて、初期の状態にロールバックされる必要があります。 Redis Cache では、一連のコマンドを[トランザクション](https://redis.io/topics/transactions)の形式で単一の操作として実行する方法をサポートしています。 |

## <a name="azure-redis-cache-offerings"></a>Azure Redis Cache のオファリング

Azure Redis Cache は以下のレベルでご利用いただけます。

| レベル | 説明 |
|---|---|
Basic | 単一ノード キャッシュ。 このレベルでは、複数のメモリ サイズ (250 MB ～ 53 GB) をサポートします。 これは、開発/テスト、およびクリティカルではない負荷に最適なレベルです。 Basic レベルには、サービス レベル アグリーメント (SLA) はありません。 |
| 標準 | Microsoft が管理しているプライマリとセカンダリの 2 つのノード構成にレプリケートされたキャッシュ。高可用性の SLA が付きます (99.9%)。 |
| Premium | Premium レベルは、エンタープライズ対応のレベルです。 Premium レベルのキャッシュでは、より多くの機能をサポートし、より短い待機時間でより高いスループットを実現します。 Premium レベルのキャッシュは、Basic または Standard レベルと比較して優れたパフォーマンスを発揮する、より高性能なハードウェア上にデプロイされます。 この利点によって、Premium レベルでは、Standard レベルと比較して、同じサイズのキャッシュのスループットがより高くなります。 |

> [!TIP]
> Premium キャッシュのサイズ、スループット、帯域幅の詳細については、「[Azure Redis Cache の FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)」を参照してください。
>

キャッシュは、既に作成された後に、より高いレベルにスケールアップできます。 より低いレベルへのスケールダウンは、サポートされていません。 スケーリングの詳細な手順については、「[Azure Redis Cache のスケーリング方法](cache-how-to-scale.md)」および「[スケーリング処理を自動化する方法](cache-how-to-scale.md#how-to-automate-a-scaling-operation)」を参照してください。

### <a name="feature-comparision"></a>機能の比較

「[Redis Cache の価格](https://azure.microsoft.com/pricing/details/cache/)」ページに、各レベルの詳細な比較を示しています。 次の表に、レベル別にサポートされる一部の機能を紹介します。

| 機能の説明 | Premium | 標準 | Basic |
| ------------------- | :-----: | :------: | :---: |
| [サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis データの保持](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis クラスター](cache-how-to-premium-clustering.md) |✔|-|-|
| [ファイアウォール規則によるセキュリティ](cache-configure.md#firewall) |✔|✔|✔|
| [VNet による強化されたセキュリティと分離](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/Export](cache-how-to-import-export-data.md) |✔|-|-|
| [更新のスケジュール](cache-administration.md#schedule-updates) |✔|-|-|
| [geo レプリケーション](cache-how-to-geo-replication.md) |✔|-|-|
| [Reboot](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>次の手順

* [ASP.NET Web アプリ クイックスタート](cache-web-app-howto.md)Azure Redis Cache を使用する単純な ASP.NET Web アプリを作成する
* [.NET クイックスタート](cache-dotnet-how-to-use-azure-redis-cache.md)Azure Redis Cache を使用する .NET Web アプリを作成する
* [.NET Core クイック スタート](cache-dotnet-core-quickstart.md)Azure Redis Cache を使用する .NET Core アプリを作成する
* [Node.js クイックスタート](cache-nodejs-get-started.md)Azure Redis Cache を使用する単純な Node.js アプリを作成する
* [Java クイックスタート](cache-java-get-started.md)Azure Redis Cache を使用する単純な Java アプリを作成する
* [Python クイックスタート](cache-python-get-started.md)Azure Redis Cache を使用する Python アプリを作成する
