---
title: Azure Functions の概要
description: Azure Functions を操作するための最初の一歩を踏み出しましょう。
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 6aba58dad9853714bf26442592f74fc77e39765c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "94975014"
---
# <a name="getting-started-with-azure-functions"></a>Azure Functions の概要

## <a name="introduction"></a>はじめに

[Azure Functions](./functions-overview.md) では、すぐに利用できるコード ブロックの中に自分のシステムのロジックを実装することができます。 これらのコード ブロックを "関数" といいます。

作業を始めるには、次のリソースをご覧ください。

::: zone pivot="programming-language-csharp"

| アクション | リソース |
| --- | --- |
| **最初の関数を作成する** | 次のいずれかのツールを使用します。<br><br><li>[Visual Studio Code](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio](./create-first-function-vs-code-csharp.md)<li>[コマンド ライン](./create-first-function-cli-csharp.md) |
| **実行中の関数を確認する** | <li>[Azure サンプル ブラウザー](https://docs.microsoft.com/samples/browse/?languages=csharp&expanded=azure&products=azure-functions)<li>[Azure コミュニティ ライブラリ](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **対話型チュートリアルを探索する**| <li>[ビジネス シナリオに最適なサーバーレス テクノロジを選択する](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[優れた設計のフレームワーク - パフォーマンス効率](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[トリガーを使用して Azure Functions を実行する](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>[対話型チュートリアルの完全な一覧](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)については、Microsoft Learn を参照してください。|
| **詳しい内容を学習する** | <li>関数が需要に合わせてインスタンスを[自動的に拡大または縮小する](./functions-scale.md)方法について学習する<li>使用可能な各種の[デプロイ方法](./functions-deployment-technologies.md)を確認する<li>組み込みの[監視ツール](./functions-monitoring.md)を使用して関数の分析に役立てる<li>[C# 言語リファレンス](./functions-dotnet-class-library.md)を読む|

::: zone-end

::: zone pivot="programming-language-java"
| アクション | リソース |
| --- | --- |
| **最初の関数を作成する** | 次のいずれかのツールを使用します。<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[ターミナル/コマンド プロンプトによる Java/Maven 関数](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[IntelliJ IDEA](./functions-create-maven-intellij.md) |
| **実行中の関数を確認する** | <li>[Azure サンプル ブラウザー](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=java)<li>[Azure コミュニティ ライブラリ](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **対話型チュートリアルを探索する**| <li>[ビジネス シナリオに最適なサーバーレス テクノロジを選択する](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[優れた設計のフレームワーク - パフォーマンス効率](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Azure Functions 用の Maven プラグインを使用してアプリを開発する](https://docs.microsoft.com/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>[対話型チュートリアルの完全な一覧](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)については、Microsoft Learn を参照してください。|
| **詳しい内容を学習する** | <li>関数が需要に合わせてインスタンスを[自動的に拡大または縮小する](./functions-scale.md)方法について学習する<li>使用可能な各種の[デプロイ方法](./functions-deployment-technologies.md)を確認する<li>組み込みの[監視ツール](./functions-monitoring.md)を使用して関数の分析に役立てる<li>[Java 言語リファレンス](./functions-reference-java.md)を読む|
::: zone-end

::: zone pivot="programming-language-javascript"
| アクション | リソース |
| --- | --- |
| **最初の関数を作成する** | 次のいずれかのツールを使用します。<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Node.js ターミナルまたはコマンド プロンプト](./create-first-function-cli-java.md) |
| **実行中の関数を確認する** | <li>[Azure サンプル ブラウザー](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=javascript%2Ctypescript)<li>[Azure コミュニティ ライブラリ](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **対話型チュートリアルを探索する** | <li>[ビジネス シナリオに最適なサーバーレス テクノロジを選択する](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[優れた設計のフレームワーク - パフォーマンス効率](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Azure Functions を使用してサーバーレス API をビルドする](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Azure Functions を使用したサーバーレス ロジックの作成](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Azure Functions で Node.js と Express API をサーバーレス API にリファクターする](https://docs.microsoft.com/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>[対話型チュートリアルの完全な一覧](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)については、Microsoft Learn を参照してください。|
| **詳しい内容を学習する** | <li>関数が需要に合わせてインスタンスを[自動的に拡大または縮小する](./functions-scale.md)方法について学習する<li>使用可能な各種の[デプロイ方法](./functions-deployment-technologies.md)を確認する<li>組み込みの[監視ツール](./functions-monitoring.md)を使用して関数の分析に役立てる<li>[JavaScript](./functions-reference-node.md) または [TypeScript](./functions-reference-node.md#typescript) の言語リファレンスを読む|
::: zone-end

::: zone pivot="programming-language-powershell"
| アクション | リソース |
| --- | --- |
| **最初の関数を作成する** | <li>[Visual Studio Code](./create-first-function-vs-code-powershell.md) を使用する |
| **実行中の関数を確認する** | <li>[Azure サンプル ブラウザー](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=powershell)<li>[Azure コミュニティ ライブラリ](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **対話型チュートリアルを探索する** | <li>[ビジネス シナリオに最適なサーバーレス テクノロジを選択する](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[優れた設計のフレームワーク - パフォーマンス効率](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Azure Functions を使用してサーバーレス API をビルドする](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Azure Functions を使用したサーバーレス ロジックの作成](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[トリガーを使用して Azure Functions を実行する](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>[対話型チュートリアルの完全な一覧](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)については、Microsoft Learn を参照してください。|
| **詳しい内容を学習する** | <li>関数が需要に合わせてインスタンスを[自動的に拡大または縮小する](./functions-scale.md)方法について学習する<li>使用可能な各種の[デプロイ方法](./functions-deployment-technologies.md)を確認する<li>組み込みの[監視ツール](./functions-monitoring.md)を使用して関数の分析に役立てる<li>[PowerShell 言語リファレンス](./functions-reference-powershell.md)を読む|
::: zone-end

::: zone pivot="programming-language-python"
| アクション | リソース |
| --- | --- |
| **最初の関数を作成する** | 次のいずれかのツールを使用します。<br><br><li>[Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-python)<li>[ターミナル/コマンド プロンプト](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python) |
| **実行中の関数を確認する** | <li>[Azure サンプル ブラウザー](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=python)<li>[Azure コミュニティ ライブラリ](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **対話型チュートリアルを探索する** | <li>[ビジネス シナリオに最適なサーバーレス テクノロジを選択する](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[優れた設計のフレームワーク - パフォーマンス効率](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Azure Functions を使用してサーバーレス API をビルドする](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Azure Functions を使用したサーバーレス ロジックの作成](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>[対話型チュートリアルの完全な一覧](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)については、Microsoft Learn を参照してください。|
| **詳しい内容を学習する** | <li>関数が需要に合わせてインスタンスを[自動的に拡大または縮小する](./functions-scale.md)方法について学習する<li>使用可能な各種の[デプロイ方法](./functions-deployment-technologies.md)を確認する<li>組み込みの[監視ツール](./functions-monitoring.md)を使用して関数の分析に役立てる<li>[Python 言語リファレンス](./functions-reference-python.md)を読む|
::: zone-end

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Functions アプリケーションの構造について学習する](./functions-reference.md)
