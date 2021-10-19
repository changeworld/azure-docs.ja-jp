---
title: '方法: Azure 関数を使用して TokenProvider を記述する'
description: Azure 関数としてカスタム トークン プロバイダーを作成してデプロイする方法。
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/tokenproviders/
ms.openlocfilehash: d6987b4e4592167fcb41a7f6654ff46140a79724
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661603"
---
# <a name="how-to-write-a-tokenprovider-with-an-azure-function"></a>方法: Azure 関数を使用して TokenProvider を記述する

> [!NOTE]
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

[Fluid Framework](https://fluidframework.com/) で、トークン プロバイダーは、`@fluidframework/azure-client` で Azure Fluid Relay サービスへの要求を行う際に使用されるトークンの作成と署名を担当します。 Fluid Framework には、**InsecureTokenProvider** という名前の単純で安全でない開発目的の TokenProvider が用意されています。 特定のサービスの認証とセキュリティに関する考慮事項に基づいたカスタム TokenProvider が各 Fluid サービスで実装される必要があります。

## <a name="implementing-your-own-tokenprovider-class"></a>独自の TokenProvider クラスの実装

作成する各 Azure Fluid Relay サービス テナントには、**テナント ID** と独自の一意な **テナント シークレット キー** が割り当てられます。 シークレット キーは **共有シークレット** です。 アプリまたはサービスでそれが認識されてから、Azure Fluid Relay サービスでそれが認識されます。 

要求に署名するためのシークレット キーが TokenProviders で認識されている必要がありますが、シークレット キーをクライアント コードに含めることはできません。 実行時に TokenProviders から Fluid サーバーに接続し、シークレット キーをクライアントに公開することなく安全に取得します。 これは、`fetchOrdererToken` と `fetchStorageToken` という 2 つの別個の API 呼び出しによって実現されます。 これらは、ホストから orderer とストレージの URL をそれぞれフェッチする役割を担います。 どちらの関数からも、トークン値を表す `TokenResponse`オブジェクトが返されます。

## <a name="tokenprovider-class-example"></a>TokenProvider クラスの例

セキュリティで保護されたトークン プロバイダーを構築するためのオプションの 1 つは、サーバーレス Azure 関数を作成し、トークン プロバイダーとして公開することです。 これにより、セキュリティで保護されたサーバーに ''*テナント シークレット キー*'' を格納できます。 その後、アプリケーションで Azure 関数を呼び出してトークンを生成します。

この例では、**AzureFunctionTokenProvider** というクラスにそのパターンを実装します。 それによって Azure 関数の URL、`userId`、および `userName` が受け入れられます。 この特定の実装は、`@fluidframework/azure-client` パッケージからのエクスポートとしても提供されます。

```typescript
import { ITokenProvider, ITokenResponse } from "@fluidframework/azure-client";

export class AzureFunctionTokenProvider implements ITokenProvider {
  constructor(
    private readonly azFunctionUrl: string,
    private readonly userId: string,
    private readonly userName: string,
  );

  public async fetchOrdererToken(tenantId: string, documentId: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }

    public async fetchStorageToken(tenantId: string, documentId: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }
}
```

テナント シークレット キーは、安全な状態を確保するため、セキュリティで保護されたバックエンドの場所に格納され、Azure 関数内からのみアクセスできます。 署名付きトークンを取得する方法の 1 つは、Azure 関数に対して、`tenantID` と `documentId`、および `userID`/`userName` を指定した `GET` 要求を行うことです。 Azure 関数でテナント ID とテナント キー シークレットとの間のマッピングを行い、トークンを適切に生成して署名します。これにより、Azure Fluid Relay サービスがそれを受け入れることができます。

```typescript
private async getToken(tenantId: string, documentId: string): Promise<string> {
    const params = {
        tenantId,
        documentId,
        userId: this.userId,
        userName: this.userName,
    };
    const token = this.getTokenFromServer(params);
    return token;
}
```

次の例では、[`axios`](https://www.npmjs.com/package/axios) ライブラリを使用して HTTP 要求を行います。 他のライブラリまたは方法を使用して HTTP 要求を行うことができます。

```typescript
private async getTokenFromServer(input: any): Promise<string> {
    return axios.get(this.azFunctionUrl, {
        params: input,
    }).then((response) => {
        return response.data as string;
    }).catch((err) => {
        return err as string;
    });
}
```

この例では、テナント キーを渡すことによってトークンをフェッチする独自の **HTTPTrigger Azure 関数** を作成する方法を示します。

```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions";
import { ScopeType } from "@fluidframework/azure-client";
import { generateToken } from "@fluidframework/azure-service-utils";

//Replace "myTenantKey" with your key here.
const key = "myTenantKey";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    // tenantId, documentId, userId and userName are required parameters
    const tenantId = (req.query.tenantId || (req.body && req.body.tenantId)) as string;
    const documentId = (req.query.documentId || (req.body && req.body.documentId)) as string;
    const userId = (req.query.userId || (req.body && req.body.userId)) as string;
    const userName = (req.query.userName || (req.body && req.body.userName)) as string;
    const scopes = (req.query.scopes || (req.body && req.body.scopes)) as ScopeType[];

    if (!tenantId) {
        context.res = {
            status: 400,
            body: "No tenantId provided in query params",
        };
    }

    if (!key) {
        context.res = {
            status: 404,
            body: `No key found for the provided tenantId: ${tenantId}`,
        };
    }

    if (!documentId) {
        context.res = {
            status: 400,
            body: "No documentId provided in query params"
        };
    }

    let user = { name: userName, id: userId };

    // Will generate the token and returned by an ITokenProvider implementation to use with the AzureClient.
    const token = generateToken(
        tenantId,
        documentId,
        key,
        scopes ?? [ScopeType.DocRead, ScopeType.DocWrite, ScopeType.SummaryWrite],
        user
    );

    context.res = {
        status: 200,
        body: token
    };
};

export default httpTrigger;
```

`generateToken` は、テナントのシークレット キーを使用して署名された、指定されたユーザーのトークンを生成する関数です。 これにより、トークンをクライアントに返すことができます。その際、シークレット キー自体を公開する必要はありません。 代わりに、トークンはそれを使用して生成され、指定されたドキュメントへのスコープ指定されたアクセスを提供します。 このトークンは、`AzureClient` と共に使用する `ITokenProvider` の実装によって返されます。

## <a name="see-also"></a>こちらもご覧ください

- [認証トークンにカスタムデータを追加する](connect-fluid-azure-service.md#adding-custom-data-to-tokens)
