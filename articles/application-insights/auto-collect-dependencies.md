---
title: Azure Application Insights - 依存関係の自動収集 | Microsoft Docs
description: Application Insights では、依存関係が自動的に収集されて視覚化されます。
services: application-insights
documentationcenter: .net
author: nikmd23
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: reference
ms.date: 08/13/2018
ms.reviewer: mbullwin
ms.author: nimolnar
ms.openlocfilehash: f20963f030c9040b696f7d6a33b25bcee2dc517f
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "40130276"
---
# <a name="dependency-auto-collection"></a>依存関係の自動収集

以下に示したのは、アプリケーションのコードに別途変更を加えなくても、現時点で依存関係として自動的に検出することができる依存関係呼び出しの一覧です。 通信ライブラリ、ストレージ クライアント、ログ ライブラリ、メトリック ライブラリに対する発信のほか、アプリケーション フレームワークやサーバーに対する着信で構成されています。 これらの依存関係は、Application Insights の [[アプリケーション マップ]](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) ビューと [[トランザクション診断]](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) ビューで視覚化されます。 必要な依存関係が以下の一覧にない場合でも、[TrackDependency 呼び出し](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)を使えば手動で追跡することができます。

## <a name="net"></a>.NET

| アプリケーション フレームワーク| バージョン |
| ------------------------|----------|
| ASP.NET Webforms | 4.5 以降 |
| ASP.NET MVC | 4 以降 |
| ASP.NET WebAPI | 4.5 以降 |
| ASP.NET Core | 1.1 以降 |
| <b> 通信ライブラリ</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5 以降、.NET Core 1.1 以降 |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0 以降、NuGet 4.3.0 |
| [EventHubs Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus Client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>ストレージ クライアント</b>|  |
| ADO.NET | 4.5 以降 |
| <b>ログ ライブラリ</b> |  |
| ILogger | 1.1 以降 |
| System.Diagnostics.Trace | 4.5 以降 |
| [nLog](https://www.nuget.org/packages/NLog/) | 4.4.12 以降 |
| [log4net](https://www.nuget.org/packages/log4net/) | 2.0.8 以降 (NetStandard 1.3)、2.0.6 以降 (.NET 4.5 以降) |

## <a name="java"></a>Java
| アプリケーション サーバー | バージョン |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7、8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6、7 |
| [Jetty](http://www.eclipse.org/jetty/) | 9 |
| <b>アプリケーション フレームワーク</b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9 以降<sup>*</sup> |
| Java サーブレット | 3.1 以降 |
| <b>通信ライブラリ</b> |  |
| [Apache Http クライアント](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3 以降<sup>†</sup> |
| <b>ストレージ クライアント</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1 以降<sup>†</sup> |
| [Oracle]( http://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1 以降<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1 以降<sup>†</sup> |
| <b>ログ ライブラリ</b> | |
| [Logback](https://logback.qos.ch/) | 1 以降 |
| [Log4j](https://logging.apache.org/log4j/) | 1.2 以降 |
| <b>メトリック ライブラリ</b> |  |
| JMX | 1.0 以降 |

> [!NOTE]
> *リアクティブ プログラミング サポートを除く。
> <br>†[JVM エージェント](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java)のインストールが必要。

## <a name="nodejs"></a>Node.js

| 通信ライブラリ | バージョン |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html)、[HTTPS](https://nodejs.org/api/https.html) | 0.10 以降 |
| <b>ストレージ クライアント</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb)、[MongoDb Core](https://www.npmjs.com/package/mongodb-core) | 2.0.0 から 2.3.0 |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 から 2.14.x |
| [PostgreSql](https://www.npmjs.com/package/pg) | 6.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x |
| <b>ログ ライブラリ</b> | |
| [console](https://nodejs.org/api/console.html) | 0.10 以降 |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x |

## <a name="javascript"></a>JavaScript

| 通信ライブラリ | バージョン |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | All |

## <a name="next-steps"></a>次の手順

- [.NET](app-insights-asp-net-dependencies.md) のカスタム依存関係追跡を設定します。
- [Java](app-insights-java-agent.md) のカスタム依存関係追跡を設定します。
- [カスタム依存関係テレメトリを記述します](app-insights-api-custom-events-metrics.md#trackdependency)。
- Application Insights の型とデータ モデルについては、[データ モデル](application-insights-data-model.md)に関するページを参照してください。
- Application Insights でサポートされている[プラットフォーム](app-insights-platforms.md)を確認します。
