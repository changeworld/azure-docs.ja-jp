<properties
   pageTitle="ロジック アプリの例とシナリオ | Microsoft Azure"
   description="ロジック アプリの一般的な例のほか、一般的なシナリオを実装する方法について説明します。"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/25/2016"
   ms.author="deonhe"/>

# ロジック アプリの例と一般的なシナリオ

このドキュメントでは、ロジック アプリを使用してビジネス プロセスを自動化する方法を理解するのに役立つ一般的なシナリオと例について詳しく説明します。

## ロジック アプリのトリガー

別のアプリケーションからロジック アプリをトリガーする方法は複数あります。その方法をいくつか次に示します。

- [HTTP 要求 (POST)](app-service-logic-http-endpoint.md)
- [webhook を受信](app-service-logic-create-api-app.md)
- [エンドポイントにポーリング](app-service-logic-create-api-app.md)

### シナリオ

- [ロジック アプリの要求への同期応答](app-service-logic-http-endpoint.md)

## コンテンツ タイプ、変換

Logic Apps の[ワークフロー定義言語](http://aka.ms/logicappsdocs)には、さまざまな種類のコンテンツを変換して操作できるように多くの関数が用意されています。また、エンジンは、データがワークフローを移動する間にコンテンツ タイプを維持するために、可能な処理をすべて行います。

- [コンテンツ タイプの処理](app-service-logic-content-type.md) (application/json、application/xml、plain/text など)
- [ワークフロー定義言語](http://aka.ms/logicappsdocs)

## 実行時間の長いアクション

- [実行時間の長いアクションの作成](app-service-logic-create-api-app.md)

## バッチとループ

- [SplitOn](app-service-logic-loops-and-scopes.md)
- [ForEach](app-service-logic-loops-and-scopes.md)
- [Until](app-service-logic-loops-and-scopes.md)

## 入れ子になったワークフローと Azure Functions の統合

- [Azure Functions の統合](app-service-logic-azure-functions.md)

## HTTP、REST、SOAP

 - [SOAP の呼び出し](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)


このドキュメントには、今後も引き続き例とシナリオを追加します。ご希望の例やシナリオがありましたら、以下のコメント セクションを使用してお知らせください。

<!---HONumber=AcomDC_0525_2016-->