---
title: '方法: Azure Fluid Relay でテスト自動化を使用する'
description: テスト自動化ライブラリを使用して、Fluid アプリケーション用の自動テストを作成する方法
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/testing/testing/
ms.openlocfilehash: 0087be9dccc1b040720ea53f5e6aae6d64f6fa17
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661637"
---
# <a name="how-to-use-test-automation-with-azure-fluid-relay"></a>方法: Azure Fluid Relay でテスト自動化を使用する

> [!NOTE]
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

コードの品質を維持し、長期にわたって使用できるようにするには、テストと自動化が不可欠です。 Fluid では、内部で [Mocha](https://mochajs.org/)、[Jest](https://jestjs.io/)、[Puppeteer](https://github.com/puppeteer/puppeteer)、[Webpack](https://webpack.js.org/) を利用したさまざまな単体および統合テストを使用しています。

テストを実行するには、ローカルの **@fluidframework/azure-local-service** を使用するか、Azure Fluid Relay サービスのテスト テナントを使用できます。 **AzureClient** は、リモート サービスとローカル サービスの両方に接続するように構成できます。これにより、ライブおよびローカルのサービス インスタンスに対するテスト間で単一のクライアントの種類を使用できます。 唯一の違いは、クライアントの作成に使用される構成です。

## <a name="automation-against-azure-fluid-relay"></a>Azure Fluid Relay に対する自動化

自動化により、運用環境のテナントと同じ方法で Azure Fluid Relay のテスト テナントに接続でき、必要なのは適切な接続構成のみです。 詳細については、「[方法: Azure Fluid Relay サービスに接続する](connect-fluid-azure-service.md)」を参照してください。

## <a name="creating-an-adaptable-test-client"></a>適応性のあるテスト クライアントの作成

適応性のあるテスト クライアントを作成するためには、サービス ターゲットに応じて異なる方法で AzureClient を構成する必要があります。 次の関数では、環境変数を使用してこれを決定します。 テスト スクリプトで環境変数を設定して、ターゲットとなるサービスを制御することができます。

```typescript
function createAzureClient(): AzureClient {
    const useAzure = process.env.FLUID_CLIENT === "azure";
    const tenantKey = useAzure ? process.env.FLUID_TENANTKEY as string : "";
    const user = { id: "userId", name: "Test User" };

    const connectionConfig = useAzure ? {
        tenantId: "myTenantId",
        tokenProvider: new InsecureTokenProvider(tenantKey, user),
        orderer: "https://myOrdererUrl",
        storage: "https://myStorageUrl",
    } : {
        tenantId: LOCAL_MODE_TENANT_ID,
        tokenProvider: new InsecureTokenProvider("", user),
        orderer: "http://localhost:7070",
        storage: "http://localhost:7070",
    };

    const clientProps = {
        connection: config,
    };

    return new AzureClient(clientProps);
}
```

テストでは、この関数を呼び出して、基になるサービスを考慮することなく AzureClient オブジェクトを作成できます。 次の [Mocha](https://mochajs.org/) テストでは、テストを実行する前にサービス クライアントを作成し、それを使用して各テストを実行します。 サービス クライアントを使用して、エラーがスローされない限りテストを通過するコンテナーを作成するテストが 1 つあります。

```typescript
describe("ClientTest", () => {
    const client = createAzureClient();
    let documentId: string;

    it("can create Azure container successfully", async () => {
        const schema: ContainerSchema = {
            initialObjects: {
                customMap: SharedMap
            },
        };
        documentId = await container.attach();
        const { container, services } = await azureClient.createContainer(schema);
    });
});

```

## <a name="running-tests"></a>テストを実行する

プロジェクトの package.json に次の npm スクリプトを追加して、テストを実行することができます。

```json
"scripts": {
    "start:local": "npx @fluidframework/azure-local-service@latest > local-service.log 2>&1",
    "test:mocha": "mocha",
    "test:azure": "cross-env process.env.FLUID_CLIENT='\"azure\"' && npm run test:mocha",
    "test:local": "start-server-and-test start:local 7070 test:mocha"
}
```

上記のスクリプトで必要とされる依存関係をインストールするには、次のコマンドを使用できます。

```bash
npm install cross-env start-server-and-test mocha
```

`test:local` スクリプトでは、[start-server-and-test](https://www.npmjs.com/package/start-server-and-test) ライブラリを使用して、ローカル サーバーを起動し、ポート 7070 (ローカル サーバーで使用される既定のポート) が応答するまで待って、テスト スクリプトを実行し、ローカル サーバーを終了させます。
