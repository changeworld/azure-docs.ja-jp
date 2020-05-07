---
title: Azure Cache for Redis とは
description: Azure Cache for Redis で、キャッシュ アサイド、コンテンツ キャッシング、ユーザー セッション キャッシイング、ジョブおよびメッセージ キュー、分散トランザクションなどを有効にする方法について説明します。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 03/11/2020
ms.openlocfilehash: cd4e7c8e2693c25f3fc092fb53874a97cfd62434
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113206"
---
# <a name="azure-cache-for-redis-description"></a>Azure Cache for Redis の説明

Azure Cache for Redis は、オープンソース ソフトウェア [Redis](https://redis.io/) を基にしたインメモリ データ ストアを提供します。 Redis をキャッシュとして使用すると、バックエンドのデータストアに大きく依存するシステムのパフォーマンスとスケーラビリティが向上します。 アプリケーションに近い場所にある高速ストレージに、アクセス頻度が高いデータをコピーすることで、パフォーマンスが向上します。 Azure Cache for Redis を使用すると、この高速ストレージは、データベースによってディスクから読み込まれずに、メモリ内に配置されます。

Azure Cache for Redis は、メモリ内のデータ構造ストア、分散型の非リレーショナル データベース、およびメッセージ ブローカーとして使用できます。 Redis エンジンの短い待機時間、高スループットのパフォーマンスを活用して、アプリケーションのパフォーマンスが向上します。

Azure Cache for Redis を使用すると、セキュリティ保護された専用の Redis キャッシュにアクセスできます。 Azure Cache for Redis は、Microsoft で管理されており、Azure 内でホストされ、Azure 内外の任意のアプリケーションからアクセスできます。 また、Azure Redis for Cache は、ディスクレス レプリケーション方式を採用しているため、ペイメント カード業界との親和性が高くなっています。

## <a name="using-azure-cache-for-redis"></a>Azure Cache for Redis の使用

Azure Cache for Redis では、一般的なアプリケーションのアーキテクチャ パターンがサポートされているため、アプリケーションのパフォーマンスが向上します。 次に、最も一般的なものをいくつか示します。

| Pattern      | 説明                                        |
| ------------ | -------------------------------------------------- |
| [キャッシュ アサイド](cache-web-app-cache-aside-leaderboard.md) | 多くの場合、データベースが大きすぎてキャッシュに直接読み込むことができません。 [キャッシュ アサイド](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) パターンを使用して、必要な場合にだけデータをキャッシュに読み込むのが一般的です。 システムがデータを変更した場合、システムはキャッシュも更新できます。それが後で他のクライアントに配布されます。 さらに、システムでは、データの有効期限を設定したり、削除ポリシーを使用してキャッシュへのデータの更新をトリガーしたりすることができます。|
| [コンテンツ キャッシュ](cache-aspnet-output-cache-provider.md) | 多くの Web ページは、ヘッダー、フッター、バナーなどの静的コンテンツを使用するテンプレートから生成されます。 これらの静的項目は、頻繁には変更されません。 メモリ内キャッシュを使用すると、バックエンド データストアと比較して、静的コンテンツに迅速にアクセスできます。 このパターンでは、処理時間とサーバーの負荷が軽減されるため、Web サーバーの応答性が向上します。 負荷に対処するために必要なサーバー数を減らすことができます。 Azure Cache for Redis は、このパターンを ASP.NET でサポートするために、Redis 出力キャッシュ プロバイダーを提供します。|
| [ユーザー セッション キャッシュ](cache-aspnet-session-state-provider.md) | このパターンは通常、Web アプリケーションでユーザーの Cookie に関連付けられる、ショッピング カートなどのユーザー履歴データに使用されます。 Cookie に格納する情報量が多すぎると、Cookie のサイズが大きくなり、大きなサイズ のCookie が要求ごとに渡されたり検証されたりするため、パフォーマンスに悪影響を及ぼす場合があります。 一般的なソリューションでは、データベース内のデータにクエリを実行するためのキーとして、Cookie を使用します。 ユーザーに情報を関連付けるために、Azure Cache for Redis などのメモリ内キャッシュを使用すると、リレーショナル データベース全体を操作するよりも、はるかに高速になります。 |
| ジョブおよびメッセージ キュー | アプリケーションは、多くの場合、要求に関連付けられている操作の実行に時間がかかると、キューにタスクを追加します。 実行時間の長い操作は、通常は別のサーバーによって順番に処理されるように、キューに登録されます。  作業を延期するこの手法は、タスク キューと呼ばれます。 Azure Cache for Redis では、アプリケーションでこのパターンを有効にするために、分散キューを提供します。|
| 分散トランザクション | アプリケーションは、バックエンド データストアに対する一連のコマンドを単一のアトミック操作として実行することを必要とする場合があります。 すべてのコマンドが成功する必要があり、成功しなかった場合はすべて、初期の状態にロールバックされる必要があります。 Azure Cache for Redis では、一連のコマンドを単一の[トランザクション](https://redis.io/topics/transactions)として実行する方法をサポートしています。 |

## <a name="azure-cache-for-redis-offerings"></a>Azure Cache for Redis のプラン

Azure Cache for Redis は以下のレベルでご利用いただけます。

| レベル | 説明 |
|---|---|
Basic | 単一ノード キャッシュ。 このレベルでは、複数のメモリ サイズ (250 MB から 53 GB) がサポートされており、開発、テスト、および重要ではないワークロードに最適です。 Basic レベルには、サービス レベル アグリーメント (SLA) はありません。 |
| Standard | Azure によって管理されているプライマリとセカンダリの 2 つのノードにレプリケートされたキャッシュ。高可用性の SLA が付きます (99.9%) |
| Premium | Premium レベルは、エンタープライズ対応レベルです。 Premium レベルのキャッシュでは、より多くの機能をサポートし、より短い待機時間でより高いスループットを実現します。 Premium レベルのキャッシュは、Basic または Standard レベルと比較して優れたパフォーマンスを発揮する、より高性能なハードウェア上にデプロイされます。 この利点によって、Premium レベルでは、Standard レベルと比較して、同じサイズのキャッシュのスループットがより高くなります。 |

> [!TIP]
> Premium キャッシュのサイズ、スループット、帯域幅の詳細については、「[Azure Cache for Redis FAQ](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)」(Azure Redis Cache の FAQ) を参照してください。
>

キャッシュは、作成後に、より高いレベルにスケールアップできます。 より低いレベルへのスケールダウンは、サポートされていません。 スケーリングの詳細な手順については、「[How to Scale Azure Cache for Redis](cache-how-to-scale.md)」(Azure Redis Cache のスケーリング方法) と「[How to automate a scaling operation](cache-how-to-scale.md#how-to-automate-a-scaling-operation)」(スケーリング処理を自動化する方法) を参照してください。

### <a name="feature-comparison"></a>機能の比較

「[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)」ページに、各レベルの詳細な比較が示されています。 次の表に、レベル別にサポートされる一部の機能を紹介します。

| 機能の説明 | Premium | Standard | Basic |
| ------------------- | :-----: | :------: | :---: |
| [サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis データの保持](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis クラスター](cache-how-to-premium-clustering.md) |✔|-|-|
| [ファイアウォール規則によるセキュリティ](cache-configure.md#firewall) |✔|✔|✔|
| 転送中の暗号化 |✔|✔|✔|
| [VNet による強化されたセキュリティと分離](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/Export](cache-how-to-import-export-data.md) |✔|-|-|
| [スケジュールされた更新](cache-administration.md#schedule-updates) |✔|✔|✔|
| [geo レプリケーション](cache-how-to-geo-replication.md) |✔|-|-|
| [Reboot](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>次のステップ

* [ASP.NET Web アプリ クイック スタート](cache-web-app-howto.md): Azure Cache for Redis を使用する単純な ASP.NET Web アプリを作成します。
* [.NET クイック スタート](cache-dotnet-how-to-use-azure-redis-cache.md): Azure Cache for Redis を使用する .NET Web アプリを作成します。
* [.NET クイック スタート](cache-dotnet-core-quickstart.md): Azure Cache for Redis を使用する .NET Core アプリを作成します。
* [Node.js クイック スタート](cache-nodejs-get-started.md): Azure Redis Cache を使用する単純な Node.js アプリを作成します。
* [Java クイック スタート](cache-java-get-started.md): Azure Cache for Redis を使用する単純な Java アプリを作成します。
* [Python クイック スタート](cache-python-get-started.md): Azure Cache for Redis を使用する Python アプリを作成します。
