---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: craigshoemaker
ms.service: functions
ms.topic: include
ms.date: 09/25/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: fc5b43dcdee394fea023124171fb42c1a18224dc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180941"
---
拡張機能のバンドルは、Azure Functions チームによって公開されたすべてのバインドを *host.json* ファイルの設定を通して利用できるようにします。 ローカル開発の場合、最新バージョンの [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#install-the-azure-functions-core-tools) があることを確認してください。

拡張機能のバンドルを使用するには、*host.json* ファイルを更新して、`extensionBundle` の次のエントリを含めます。

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

- `id` プロパティは、Microsoft Azure Functions 拡張機能のバンドルの名前空間を参照します。
- `version` は、バンドルのバージョンを参照します。

バンドルのバージョンは、バンドル内のパッケージの変更に応じて増加します。 メジャー バージョンの変更は、バンドル内のパッケージによるメジャー バージョンの移動が行われる場合にのみ発生します。 `version` プロパティでは、[バージョン範囲の指定に間隔表記](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards)を使用します。 Functions ランタイムは常に、バージョン範囲 (間隔) で定義された最大許容バージョンを選択します。

自分のプロジェクトで拡張機能のバンドルを参照すると、すべての既定のバインドを自分の関数で使用できるようになります。 [拡張機能のバンドル](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)で使用できるバインドは、次のとおりです。

|Package  |バージョン  |
|---------|---------|
|Microsoft.Azure.WebJobs.Extensions.CosmosDB|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.DurableTask|1.8.0|
|Microsoft.Azure.WebJobs.Extensions.EventGrid|2.0.0|
|Microsoft.Azure.WebJobs.Extensions.EventHubs|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SendGrid|3.0.0|
|Microsoft.Azure.WebJobs.Extensions.ServiceBus|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SignalRService|1.0.0|
|Microsoft.Azure.WebJobs.Extensions.Storage|3.0.4|
|Microsoft.Azure.WebJobs.Extensions.Twilio|3.0.0|