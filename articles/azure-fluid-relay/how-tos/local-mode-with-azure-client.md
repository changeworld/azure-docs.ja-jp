---
title: '方法: ローカル テストに AzureClient を使用する'
description: AzureClient を使用してサービスなしでアプリケーションをテストする方法
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: reference
ms.service: azure-fluid
ms.openlocfilehash: f73c5e62471f6c038d7338244ec4756b533fe2d7
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661704"
---
# <a name="how-to-use-azureclient-for-local-testing"></a>方法: ローカル テストに AzureClient を使用する

> [!NOTE]
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

この記事では、ローカル モードで **AzureClient** を構成し、それを使用して Fluid アプリケーションをローカルでテストする手順について説明します。

## <a name="configure-and-create-an-azureclient"></a>AzureClient の構成と作成

**AzureClient** は、次のような構成を渡して、ローカルの Azure Fluid Relay インスタンスに対して実行するように構成できます。

```js
    import { AzureClient, AzureConnectionConfig, LOCAL_MODE_TENANT_ID } from "@fluidframework/azure-client";
    import { InsecureTokenProvider } from "@fluidframework/test-client-utils";

    const clientProps = {
        connection: {
            tenantId: LOCAL_MODE_TENANT_ID,
            tokenProvider: new InsecureTokenProvider("", { id: "123", name: "Test User" }),
            orderer: "http://localhost:7070",
            storage: "http://localhost:7070",
        },
    };

    const azureClient = new AzureClient(clientProps);
```

この例では、**InsecureTokenProvider** を使用して認証トークンの生成と署名を行います。これを、Azure Fluid Relay サービスが受け入れます。 ただし、名前が示すように、これは安全ではないため、運用環境では使用しないでください。 InsecureTokenProvider の詳細については、[アプリでの認証と承認](https://fluidframework.com/docs/build/auth/#the-token-provider)に関する記事を参照してください。

ローカルで実行するには、まず、ローカルの Azure Fluid Relay サービス インスタンスが実行されているドメインとポート (既定では http://localhost:7070 ) を指す Orderer とストレージの URL を構成します。 最後の手順は、`tenantId` を `LOCAL_MODE_TENANT_ID` に設定することです。 これらの設定ではすべて、ローカルの Azure Fluid Relay サービスを使用するように AzureClient が構成されます。  

## <a name="enabling-debug-logging"></a>デバッグ ログを有効にする

ブラウザー コンソールで次の設定を使用して、Fluid Framework から組み込みのデバッグ ログを有効にすることができます。

`localStorage.debug = 'fluid:*'`

より高度なシナリオでは、`logger` を AzureClient に渡すことができます。 これにより、ログ動作をカスタマイズできるようになります。 ロガーまたはテレメトリの詳細については、fluidframework.com の「[Logging and telemetry](https://fluidframework.com/docs/testing/telemetry/)」(ログとテレメトリ) を参照してください。 

## <a name="running-azure-fluid-relay-service-locally"></a>Azure Fluid Relay サービスのローカルでの実行

AzureClient のローカル モードを使用するには、まずローカル サーバーを起動する必要があります。 ターミナル ウィンドウから `npx @fluidframework/azure-local-service@latest` を実行すると、Azure Fluid Relay ローカル サーバーが起動します。 サーバーが起動したら、ローカル サービスに対してアプリケーションを実行できます。
