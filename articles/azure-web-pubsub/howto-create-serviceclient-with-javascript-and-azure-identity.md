---
title: JavaScript と Azure Identity を使用して WebPubSubServiceClient を作成する方法
description: JavaScript と Azure Identity を使用して WebPubSubServiceClient を作成する方法
author: terencefan
ms.author: tefa
ms.date: 11/15/2021
ms.service: azure-web-pubsub
ms.topic: how-to
ms.openlocfilehash: 9d32f3b7b56fffacf811c9ba1860dec34af917ec
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725237"
---
# <a name="how-to-create-a-webpubsubserviceclient-with-javascript-and-azure-identity"></a>JavaScript と Azure Identity を使用して `WebPubSubServiceClient` を作成する方法

この攻略ガイドでは、JavaScript で Azure Active Directory を使用して `WebPubSubServiceClient` を作成する方法について説明します。

## <a name="requirements"></a>要件

- npmjs.com から [@azure/identity](https://www.npmjs.com/package/@azure/identity) パッケージをインストールします。

  ```bash
  npm install --save @azure/identity
  ```

- npmjs.com から [@azure/web-pubsub](https://www.npmjs.com/package/@azure/web-pubsub) パッケージをインストールします

  ```bash
  npm install @azure/web-pubsub
  ```

## <a name="sample-codes"></a>サンプル コード

1. Azure Identity SDK を使用して `TokenCredential` を作成します。

    ```javascript
    const { DefaultAzureCredential } = require('@azure/identity')

    let credential = new DefaultAzureCredential();
    ```

    `credential` は、`TokenCredential` クラスを継承する任意のクラスにすることができます。

    - EnvironmentCredential
    - ClientSecretCredential
    - ClientCertificateCredential
    - ManagedIdentityCredential
    - VisualStudioCredential
    - VisualStudioCodeCredential
    - AzureCliCredential

    詳細については、[JavaScript 用の Azure Identity クライアント ライブラリ](/javascript/api/overview/azure/identity-readme)に関するページを参照してください

2. 次に、`endpoint`、`hub`、`credential` を使用して `client` を作成します。 

    ```javascript
    const { DefaultAzureCredential } = require('@azure/identity')

    let credential = new DefaultAzureCredential();

    let serviceClient = new WebPubSubServiceClient("<endpoint>", credential, "<hub>");
    ```

    このクライアントの使用方法については、[JavaScript 用 Azure Web PubSub サービス クライアント ライブラリ](/javascript/api/overview/azure/web-pubsub-readme)に関する記事を参照してください

## <a name="complete-sample"></a>完全なサンプル

- [AAD 認証を使用した簡単なチャットルーム](https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp-aad)