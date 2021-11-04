---
title: .NET アプリを Azure App Service に移行する
description: Azure App Service に使用できる .NET 移行リソースを検出します。
author: msangapu-msft
ms.topic: article
ms.date: 03/29/2021
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8918c32e03c6e2c27621dd5c05b9abe976ded511
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092093"
---
# <a name="net-migration-cases-for-azure-app-service"></a>Azure App Service の .NET 移行のケース

Azure App Service には、オンプレミスの .NET Web アプリをすばやく検出し、準備状況を評価し、コンテンツとサポートされている構成の両方を App Service に移行するための使いやすいツールが備わっています。

これらのツールは、検出、評価、移行に重点を置いて、さまざまな種類のシナリオをサポートするために開発されています。 .NET 移行ツールと使用例の一覧を次に示します。

## <a name="migrate-from-multiple-servers-at-scale-preview"></a>複数のサーバーからの大規模な移行 (プレビュー)

<!-- Intent: discover how to assess and migrate at scale. -->

Azure Migrate、Web アプリの大規模な検出、エージェントレス検出、評価 ASP.NET 発表されました。 VMware 環境 ASP.NET のインターネット インフォメーション サービス (IIS) サーバーで実行されている Web アプリを簡単に検出し、Azure App Service への移行について評価できます。 評価は、Web アプリの移行の準備状況、移行のブロック、修復ガイダンス、推奨される SKU、ホスティング コストを判断するのに役立ちます。 大規模な移行リソースを以下に示します。

### <a name="at-scale-migration-resources"></a>大規模な移行リソース

| 方法 |
|----------------|
| [Web アプリと SQL Server インスタンスを検出する](../migrate/how-to-discover-sql-existing-project.md)                              |
| [Azure App Service の評価を作成する](../migrate/how-to-create-azure-app-service-assessment.md)                            |
| [Azure App Service に移行するための Web アプリの評価に関するチュートリアル](../migrate/tutorial-assess-webapps.md)                       |
| [Azure Migrate を使用したオンプレミス サーバーでのソフトウェア インベントリの検出](../migrate/how-to-discover-applications.md)           |
| **ブログ** |
| [Azure Migrate を使用して ASP.NET アプリを大規模に検出して評価する](https://azure.microsoft.com/blog/discover-and-assess-aspnet-apps-atscale-with-azure-migrate/) |
| **FAQ** |
| [Azure Migrate の検出および評価ツールでの Azure App Service の評価](../migrate/concepts-azure-webapps-assessment-calculation.md) |
| **ベスト プラクティス** |
| [Azure Migrate Discovery and Assessment ツール](../migrate/best-practices-assessment.md) |
| **ビデオ** |
| [Azure Migrate を使用した ASP.NET アプリ移行の大規模検出と評価](https://channel9.msdn.com/Shows/Inside-Azure-for-IT/At-scale-discovery-and-assessment-for-ASPNET-app-migration-with-Azure-Migrate) |

## <a name="migrate-from-an-iis-server"></a>IIS サーバーからの移行

<!-- Intent: discover how to assess and migrate from a single IIS server  -->

[PowerShell スクリプト](https://github.com/Azure/App-Service-Migration-Assistant/wiki/PowerShell-Scripts) [(ダウンロード)](https://appmigration.microsoft.com/api/download/psscriptpreview/AppServiceMigrationScripts.zip) を使用して、Azure Migrate の大規模な検出エクスペリエンスを通じて検出された単一の IIS サーバーから ASP.NET Web アプリを移行できます。 [Azure App Service への移行に関する更新](https://channel9.msdn.com/Shows/The-Launch-Space/Updates-on-Migrating-to-Azure-App-Service)に関するビデオをご覧ください。

## <a name="aspnet-web-app-migration"></a>ASP.NET Web アプリの移行
<!-- Intent: migrate a single web app -->

App Service Migration Assistant を使用して、[スタンドアロンのオンプレミス ASP.NET Web アプリを Azure App Service に移行](https://www.youtube.com/watch?v=9LBUmkUhmXU)できます。 App Service Migration Assistant の目的は、オンプレミスからクラウドにアプリケーションを移行するための無料かつシンプルな高速ソリューションを通じて、クラウドへの移行を簡略化することです。 移行アシスタント ツールの詳細については、[FAQ](https://github.com/Azure/App-Service-Migration-Assistant/wiki) を参照してください。

## <a name="containerize-an-aspnet-web-app"></a>Web アプリ ASP.NET コンテナー化する

.NET Framework Web アプリケーションには、Azure App Service 内で使用できないライブラリやその他の機能への依存関係がある場合があります。 これらのアプリは、グローバル アセンブリ キャッシュ内の他のコンポーネントに依存している場合があります。 以前は、これらのアプリケーションは仮想マシンでのみ実行できます。 ただし、これらは Azure App Service Windows コンテナー内で実行できます。

[アプリ コンテナー化ツール](https://azure.microsoft.com/blog/accelerate-application-modernization-with-azure-migrate-app-containerization/)では、最小限の変更でアプリケーションをコンテナーとして再パッケージ化できます。 このツールは現在、アプリケーションと Apache Tomcat Java ASP.NET コンテナー化をサポートしています。 コンテナー化と移行の詳細については、[方法](../migrate/tutorial-app-containerization-aspnet-app-service.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

[オンプレミスの Web アプリを Azure App Service に移行する](/learn/modules/migrate-app-service-migration-assistant/)
