---
title: Logic Apps の例とシナリオ | Microsoft Docs
description: ロジック アプリの一般的な例のほか、一般的なシナリオを実装する方法について説明します。
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/11/2016
ms.author: jehollan

---
# Logic Apps の例と一般的なシナリオ
このドキュメントでは、ロジック アプリを使用してビジネス プロセスを自動化する方法を理解するのに役立つ一般的なシナリオと例について詳しく説明します。

## カスタムのトリガーとアクション
別のアプリケーションからロジック アプリをトリガーする方法は複数あります。いくつかの一般的な例を次に示します。

* [カスタム トリガーまたはカスタム アクションの作成](app-service-logic-create-api-app.md)
* [実行時間の長いアクション](app-service-logic-create-api-app.md)
* [HTTP 要求トリガー (POST)](app-service-logic-http-endpoint.md)
* [Webhook のトリガーとアクション](app-service-logic-create-api-app.md)
* [ポーリング トリガー](app-service-logic-create-api-app.md)

### シナリオ
* [要求への同期応答](app-service-logic-http-endpoint.md)
* [SMS での要求 - 応答](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## エラー処理とログ記録
* [例外とエラー処理](app-service-logic-exception-handling.md)
* [Azure アラートと Azure 診断の構成](app-service-logic-monitor-your-logic-apps.md)

### シナリオ
* [使用例: エラーと例外の処理](app-service-logic-scenario-error-and-exception-handling.md)

## デプロイと管理
* [自動化されたデプロイの作成](app-service-logic-create-deploy-template.md)
* [Visual Studio でのロジック アプリのビルドとデプロイ](app-service-logic-deploy-from-vs.md)
* [ロジック アプリの監視](app-service-logic-monitor-your-logic-apps.md)

## コンテンツ タイプ、変換
Logic Apps の[ワークフロー定義言語](http://aka.ms/logicappsdocs)には、さまざまなコンテンツ タイプを変換して操作できるように多くの関数が用意されています。また、エンジンは、データがワークフローを移動する間にコンテンツ タイプを維持するために、可能な処理をすべて行います。

* [コンテンツ タイプの処理](app-service-logic-content-type.md) (application/json、application/xml、plain/text など)
* [ワークフロー定義の作成](app-service-logic-author-definitions.md)
* [ワークフロー定義言語のリファレンス](http://aka.ms/logicappsdocs)

## バッチとループ
* [SplitOn](app-service-logic-loops-and-scopes.md)
* [ForEach](app-service-logic-loops-and-scopes.md)
* [Until](app-service-logic-loops-and-scopes.md)

## Azure Functions との統合
* [Azure Functions の統合](app-service-logic-azure-functions.md)

### シナリオ
* [Service Bus のトリガーとしての Azure Functions](app-service-logic-scenario-function-sb-trigger.md)

## HTTP、REST、SOAP
* [SOAP の呼び出し](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

このドキュメントには、今後も引き続き例とシナリオを追加します。ご希望の例やシナリオがありましたら、以下のコメント セクションを使用してお知らせください。

<!---HONumber=AcomDC_0817_2016-->