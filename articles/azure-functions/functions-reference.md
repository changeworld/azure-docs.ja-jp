<properties
	pageTitle="Azure Functions 開発者向けリファレンス | Microsoft Azure"
	description="すべての言語とバインドに共通する Azure Functions の概念とコンポーネントについて説明します。"
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="04/07/2016"
	ms.author="chrande"/>

# Azure Functions developer reference (Azure Functions 開発者向けリファレンス)

Azure Functions は、使用する言語またはバインドに関係なく、いくつかの中核となる技術的な概念とコンポーネントを共有します。特定の言語またはバインドに固有の詳細を学習する前に、それらすべてに適用されるこの概要をお読みください。

この記事は、「[Azure Functions の概要](functions-overview.md)」を既に読んでいて、[トリガー、バインド、JobHost ランタイムなどの WebJobs SDK の概念](../app-service-web/websites-dotnet-webjobs-sdk.md)を熟知していることを前提として書かれています。Azure Functions は WebJobs SDK が基になっています。

## function

*関数* は Azure Functions の主要な概念です。好みの言語を選択して関数のコードを記述し、コード ファイルと構成ファイルを同じフォルダーに保存します。構成には JSON を使用し、ファイルの名前は `function.json` です。さまざまな言語がサポートされていて、各言語はその言語での作業に最適化された少しずつ異なるエクスペリエンスを備えています。フォルダー構造の例を次に示します。

```
mynodefunction
| - function.json
| - index.js
| - node_modules
| | - ... packages ...
| - package.json
mycsharpfunction
| - function.json
| - run.csx
```

## function.json とバインド

`function.json` ファイルには、バインドなど、関数に固有の構成が含まれています。ランタイムはこのファイルを読み取って、トリガーするイベント、関数を呼び出すときに含めるデータ、関数自体から渡されるデータの送信先を決定します。

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

`disabled` プロパティを `true` に設定することにより、ランタイムが関数を実行しないようにすることができます。

`bindings` プロパティで、トリガーとバインドの両方を構成します。各バインドは、いくつかの一般的な設定と、バインドの特定の種類に固有の設定を共有します。すべてのバインドには次の設定が必要です。

|プロパティ|値/型|説明|
|---|-----|------|
|`type`|string|バインドの種類。たとえば、「`queueTrigger`」のように入力します。
|`direction`|"in"、"'out"| バインドが関数への受信データか、関数からの送信データかを示します。
| `name` | string | 関数のバインドされたデータに使用される名前。C# の場合は引数の名前です。JavaScript の場合はキー/値リストのキーです。

## ランタイム (スクリプト ホストおよび Web ホスト)

スクリプト ホストとも呼ばれるランタイムは基になる WebJobs SDK ホストであり、イベントをリッスンし、データを収集して送信し、最終的にはコードを実行します。

HTTP トリガーを容易にするため、運用環境シナリオでスクリプト ホストの前に配置されるように設計されている Web ホストもあります。これにより、Web ホストによって管理されるフロントエンド トラフィックからスクリプト ホストが分離されます。

## フォルダー構造

スクリプト ホストは、構成ファイルと 1 つまたは複数の関数を含むフォルダーを参照します。

```
parentFolder (for example, wwwroot)
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

*host.json* ファイルは、スクリプト ホスト固有の構成を含み、親フォルダーに格納されます。

各関数には、コード ファイル、*function.json*、およびその他の依存関係を含むフォルダーがあります。

Azure App Service で関数アプリに関数をデプロイするためにプロジェクトをセットアップするときは、このフォルダー構造をサイト コードとして扱うことができます。継続的インテグレーションやデプロイなどの既存のツール、またはカスタム デプロイ スクリプトを使用して、デプロイ時のパッケージのインストールまたはコードの変換を行うことができます。

## 並列実行

シングル スレッドの関数ランタイムが処理できるより速く複数のトリガー イベントが発生する場合、ランタイムは関数を並列で複数回呼び出す場合があります。関数アプリが[動的サービス プラン](functions-scale.md#dynamic-service-plan)を使用している場合、関数アプリは最大 10 個の同時インスタンスまで自動的にスケールアウトできます。アプリが動的サービス プランまたは標準の [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)のどちらで実行していても、関数アプリの各インスタンスは、複数の同時関数呼び出しを、複数のスレッドを使用して並列に処理できます。各関数アプリ インスタンスでの同時関数呼び出しの最大数は、関数アプリのメモリ サイズによって異なります。

## Azure Functions パルス  

パルスは、関数の実行頻度と成功または失敗を示すライブ イベント ストリームです。また、平均実行時間を監視することができます。今後、さらに機能とカスタマイズが追加される予定です。**[パルス]** ページには **[監視]** タブからアクセスできます。

## バインド

サポートされるすべてのバインドを次の表に示します。

[AZURE.INCLUDE [動的コンピューティング](../../includes/functions-bindings.md)]

## 問題の報告

[AZURE.INCLUDE [問題の報告](../../includes/functions-reporting-issues.md)]

## 次のステップ

詳細については、次のリソースを参照してください。

* [Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)](functions-reference-csharp.md)
* [Azure Functions NodeJS 開発者向けリファレンス](functions-reference-node.md)
* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0427_2016-->