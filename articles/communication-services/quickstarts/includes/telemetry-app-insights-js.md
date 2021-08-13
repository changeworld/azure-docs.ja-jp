---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: orwatson
manager: vravikumar
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: orwatson
ms.openlocfilehash: 8ad763ed5f0213569682599aaa0fc3cba4651939
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292430"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/) のアクティブ LTS バージョンとメンテナンス LTS バージョン。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../create-communication-resource.md)。
- Azure portal で、[Application Insights リソース](../../../azure-monitor/app/create-new-resource.md)を作成します。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-app"></a>新しい Node.js アプリを作成する

ACS ライブラリを使用するアプリが既にあり、JavaScript を使い慣れている場合は、「[トレーサーの設定](#setup-tracer)」に進んでかまいません。

開始するには、クライアント ライブラリのいずれかを使用する JS アプリが必要です。 ここでは、[@azure/communication-identity](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/communication/communication-identity) ライブラリを使用してアプリを作成します。 次の手順に従って、サンプル アプリを設定します。

ターミナルまたはコマンド ウィンドウを開き、新しいディレクトリを作成してそこに移動します。

```console
mkdir export-js-telemetry && cd export-js-telemetry
```
`npm init -y` を実行して、既定の設定で **package.json** を作成します。

```console
npm init -y
```

### <a name="install-dependencies"></a>依存関係のインストール

`npm install` コマンドを使用して、アプリで使用する依存関係をインストールします。

```bash
npm install @azure/communication-identity @azure/monitor-opentelemetry-exporter @opentelemetry/node@^0.14.0 @opentelemetry/plugins-node-core --save
```

`--save` オプションにより、前の手順で作成した **package.json** ファイルに依存関係としてライブラリが追加されます。

`@opentelemetry/node@^0.14.0` のバージョンを忘れないでください。 このガイドの執筆時点では、これが、`@azure/monitor-opentelemetry-exporter` と完全に互換性のあるパッケージの最新バージョンです。

### <a name="add-main-app-file"></a>メイン アプリ ファイルを追加する

ディレクトリのルートに **token.js** というファイルを作成します。 これに次のコードを追加します。

```javascript
// token.js

const { CommunicationIdentityClient } = require("@azure/communication-identity");

async function main() {
  const ACS_CONNECTION_STRING = "<your-acs-connection-string>"
  const client = new CommunicationIdentityClient(ACS_CONNECTION_STRING);

  console.log("Creating user...")
  const user = await client.createUser();
  console.log(`User ${user.communicationUserId} was created successfully.\n`);

  console.log(`Issuing token for ${user.communicationUserId}...`);
  const { token } = await client.getToken(user, ["chat"]);
  console.log("Token issued successfully.\n")
  console.log(`${token}\n`);

  console.log(`Revoking token for ${user.communicationUserId}...`);
  await client.revokeTokens(user);
  console.log("Token revoked successfully.\n");

  console.log(`Deleting user ${user.communicationUserId}...`);
  await client.deleteUser(user);
  console.log("User deleted successfully.\n");
}

main().catch((error) => {
  console.log("Encountered an error:", error);
  process.exit(1);
});
```
## <a name="setup-tracer"></a>トレーサーの設定

ディレクトリのルートに **tracing.js** というファイルを作成します。 これに次のコードを追加します。

```javascript
// tracing.js

const azureSdkTracing = require("@azure/core-tracing");
const { AzureMonitorTraceExporter } = require("@azure/monitor-opentelemetry-exporter");
const { NodeTracerProvider } = require("@opentelemetry/node");
const { BatchSpanProcessor } = require("@opentelemetry/tracing");

const AI_CONNECTION_STRING = "<your-application-insights-connection-string>";
const provider = new NodeTracerProvider();
const azExporter = new AzureMonitorTraceExporter({
  connectionString: AI_CONNECTION_STRING
});

provider.addSpanProcessor(
  new BatchSpanProcessor(azExporter, {
    bufferSize: 1000, // 1000 spans
    bufferTimeout: 5000 // 5 seconds
  })
);

provider.register();

const tracer = provider.getTracer("sample tracer");
azureSdkTracing.setTracer(tracer);

exports.default = tracer;
```

### <a name="add-tracer-to-main-app"></a>メイン アプリにトレーサーを追加する

**token.js** に対して次の更新を行います。

**tracing.js** を要求します。

```javascript
const { CommunicationIdentityClient } = require("@azure/communication-identity");
const tracer = require("./tracing.js");

async function main() {
```

ルート スパンを作成し、データをエクスポートします。

```javascript
  console.log("User deleted successfully.\n");
}

const rootSpan = tracer.startSpan("Root Identity Span");
tracer.withSpan(rootSpan, async function() {
  try {
    await main();
  } catch (error) {
    console.error("Error running sample:", error);
  } finally {
    // End the optional root span on completion
    rootSpan.end();
  }
}).then(function() {
  console.log("Awaiting batched span processor to export batched spans...");

  setTimeout(function() {
    console.log("Spans exported.");
  }, 6000);
});
```

## <a name="run-the-code"></a>コードの実行

プレースホルダー テキストを有効な接続文字列値に置き換えてください。

node コマンドを使用して、**token.js** ファイルに追加したコードを実行します。

```console
node token.js
```