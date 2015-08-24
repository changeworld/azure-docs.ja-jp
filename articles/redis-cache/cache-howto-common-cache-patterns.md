<properties 
   pageTitle="Azure Redis Cache の一般的なキャッシュ パターン" 
   description="Azure Redis Cache をどこでなぜ使うかについて説明します" 
   services="redis-cache" 
   documentationCenter="" 
   authors="Rick-Anderson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd" 
   ms.date="08/11/2015"
   ms.author="riande"/>

# Azure Redis Cache の一般的なキャッシュ パターン

このページでは、キャッシュを使用する最も一般的な利点を紹介します。

## キャッシュによるデータ アクセスの最適化

キャッシュを使用すると、データ ストアからのデータのフェッチを大幅に高速化できます。キャッシュは、高いスループットと低待機時間を実現します。キャッシュからホット データをフェッチすることによって、アプリケーションを高速化するだけでなく、データ アクセスの負荷を減らし、他のクエリの応答性を高めることができます。キャッシュに情報を格納することで、リソースを節約でき、アプリケーションへの要求に合わせてスケーラビリティが向上します。アプリケーションは、キャッシュからデータを効率的にフェッチできると、バースト負荷に対する応答性がきわめて高くなります。

## 分散セッションの状態
セッション状態を使用しないことが最適解ですが、セッション データを使用することによりパフォーマンスの向上や複雑さの軽減という利点が得られるアプリケーションや、無条件にセッション状態を必要とするアプリケーションもあります。メモリ プロバイダーのセッション状態の既定値では、スケールアウト (Web サイトの複数のインスタンスの実行) は許可されません。ASP.NET SQL Server のセッション状態プロバイダーでは複数の Web サイトでセッション状態を使用することが許可されますが、メモリ プロバイダーの場合と比較して、高待機時間のコストが発生します。一方、Redis セッション状態キャッシュ プロバイダーは、低待機時間で、構成や設定が非常に簡単です。アプリケーションで限られた量のセッション状態を使用する場合は、データと少量のセッション状態をキャッシュするのにほとんどのキャッシュを使用できます。

## サービス ダウンタイムへの対応 (キャッシュ フォールバック)
 キャッシュにデータを格納することによって、アプリケーションをネットワークの待機時間、Web サービスの問題、ハードウェア障害などのシステム障害に対応できることがあります。アプリケーションが完全に利用できなくなるより、Web サービスやデータベースの回復までキャッシュされたデータを提供した方がよい場合が多くあります。

## 次のステップ
Azure Redis Cache の使用の詳細については、次を参照してください。
 
- [Redis Azure Cache のドキュメント](http://azure.microsoft.com/documentation/services/cache/): このページには、Azure Redis Cache の使用に関するリンクが多数記載されています。
- [Azure Redis Cache を使用した MVC ムービー アプリを 15 分でデプロイする:](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/): このブログの投稿は、ASP.NET MVC アプリで Azure Redis Cache を使用するためのクイック スタートについて説明しています。
- [Azure Websites で ASP.NET セッション状態を使用する方法](../app-service-web/web-sites-dotnet-session-state-caching.md): このトピックでは、Azure Redis Cache Service を使用してセッション状態をサポートする方法について説明します。

<!---HONumber=August15_HO7-->