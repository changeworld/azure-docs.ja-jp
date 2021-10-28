---
title: Bicep サンプル
description: App Service の一般的なシナリオをピックアップした Bicep サンプルをご覧いただけます。 App Service のデプロイまたは管理タスクを自動化する方法について説明します。
author: seligj95
tags: azure-service-management
ms.topic: sample
ms.date: 8/26/2021
ms.author: jordanselig
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: c6423d3f77e85918f02bc64224dba86488b7323d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224279"
---
# <a name="bicep-files-for-app-service"></a>App Service の Bicep ファイル

次の表には、Azure App Service の Bicep ファイルへのリンクが含まれています。 Bicep のクイックスタートと詳細については、[Bicep のドキュメント](../azure-resource-manager/bicep/index.yml)を参照してください。

App Services リソースの Bicep 構文とプロパティについては、「[Microsoft.Web resource types (Microsoft.Web リソースの種類)](/azure/templates/microsoft.web/allversions)」を参照してください。

| アプリのデプロイ | 説明 |
|-|-|
| [App Service プランと基本的な Linux アプリ](https://github.com/Azure/bicep/tree/main/docs/examples/101/web-app-linux) | Linux 用に構成された App Service アプリをデプロイします。 |
| [App Service プランと基本的な Windows アプリ](https://github.com/Azure/bicep/tree/main/docs/examples/101/web-app-windows) | Windows 用に構成された App Service アプリをデプロイします。 |
| [コンテナーを含む Web サイト](https://github.com/Azure/bicep/tree/main/docs/examples/101/website-with-container) | Linux 用に構成された Docker イメージから Web サイトをデプロイします。 |
| **アプリの構成** | **説明** |
| [条件付きログを含むアプリ](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-conditional-log)| ログ記録有効化に条件を付けて App Service アプリをデプロイします。 |
| [ログ分析モジュールを使用したアプリ](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-loganalytics-mod)| ログ分析と共に App Service アプリをデプロイします。 |
| [リージョン VNet 統合を使用したアプリ](https://github.com/Azure/bicep/tree/main/docs/examples/101/app-service-regional-vnet-integration)| リージョン VNet 統合が有効になっている App Service アプリをデプロイします。 |
|**接続リソースを使ったアプリ**| **説明** |
| [CosmosDB を使用したアプリ](https://github.com/Azure/bicep/tree/main/docs/examples/101/cosmosdb-webapp)| CosmosDB のある Linux で App Service アプリをデプロイします。 |
| [MySQL を使ったアプリ](https://github.com/Azure/bicep/tree/main/docs/examples/101/webapp-managed-mysql)| Azure Database for MySQL を使って Windows 上に App Service アプリをデプロイします。 |
| [Azure SQL Database のデータベースを使ったアプリ](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-sql-database)| App Service アプリと、Azure SQL Database のデータベースを Basic サービス レベルでデプロイします。 |
| [バックエンド WebApp に接続されているアプリ](https://github.com/Azure/bicep/tree/main/docs/examples/101/webapp-privateendpoint-vnet-injection)| VNet インジェクションおよびプライベート エンドポイントと共に安全に接続されている 2 つの Web アプリ (フロントエンドとバックエンド) をデプロイします。 |
| [データベース、マネージド ID、監視を含むアプリ](https://github.com/Azure/bicep/tree/main/docs/examples/301/web-app-managed-identity-sql-db)| データベース、マネージド ID、監視を含む App Service アプリをデプロイします。 |
|**App Service 環境**| **説明** |
| [App Service Environment v2 の作成](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-asev2-create) | App Service Environment v2 を仮想ネットワークに作成します。 |
| | |