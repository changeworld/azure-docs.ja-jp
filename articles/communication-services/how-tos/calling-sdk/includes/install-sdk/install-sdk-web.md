---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: a671eb00f2108b96220e72a32f8dd9f5654a4224
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699159"
---
## <a name="install-the-sdk"></a>SDK のインストール

`npm install` コマンドを使用して、JavaScript 用の Azure Communication Services 通話と共通の各 SDK をインストールします。

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

## <a name="initialize-required-objects"></a>必要なオブジェクトを初期化する

CallClient インスタンスは、ほとんどの呼び出し操作に必要です。 新しい `CallClient` インスタンスを作成しましょう。 Logger インスタンスなどのカスタム オプションを使用して構成できます。

`CallClient` インスタンスを用意したら、`CallClient` インスタンスで `createCallAgent` メソッドを呼び出すことで、`CallAgent` インスタンスを作成できます。 このメソッドでは、非同期的に `CallAgent` インスタンス オブジェクトが返されます。

`createCallAgent` メソッドでは、`CommunicationTokenCredential` が引数として使用されます。 これは、[ユーザー アクセス トークン](../../../../quickstarts/access-tokens.md)を受け取ります。

`CallClient` インスタンスで `getDeviceManager` メソッドを使用して、`deviceManager` にアクセスできます。

```js
const { CallClient } = require('@azure/communication-calling');
const { AzureCommunicationTokenCredential} = require('@azure/communication-common');
const { AzureLogger, setLogLevel } = require("@azure/logger");

// Set the logger's log level
setLogLevel('verbose');

// Redirect log output to wherever desired. To console, file, buffer, REST API, etc...
AzureLogger.log = (...args) => {
    console.log(...args); // Redirect log output to console
};

const userToken = '<USER_TOKEN>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```