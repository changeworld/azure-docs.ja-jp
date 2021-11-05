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
ms.openlocfilehash: 80524d6ab2da2e805e1107755cef4cfb367f6d2a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039451"
---
# <a name="how-to-write-a-tokenprovider-with-an-azure-function"></a>方法: Azure 関数を使用して TokenProvider を記述する

> [!NOTE]
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

[Fluid Framework](https://fluidframework.com/) で、トークン プロバイダーは、`@fluidframework/azure-client` で Azure Fluid Relay サービスへの要求を行う際に使用されるトークンの作成と署名を担当します。 Fluid Framework には、**InsecureTokenProvider** という名前の単純で安全でない開発目的の TokenProvider が用意されています。 特定のサービスの認証とセキュリティに関する考慮事項に基づいたカスタム TokenProvider が各 Fluid サービスで実装される必要があります。

作成する各 Azure Fluid Relay サービス テナントには、**テナント ID** と独自の一意な **テナント シークレット キー** が割り当てられます。 シークレット キーは **共有シークレット** です。 アプリまたはサービスでそれが認識されてから、Azure Fluid Relay サービスでそれが認識されます。 要求に署名するためのシークレット キーが TokenProviders で認識されている必要がありますが、シークレット キーをクライアント コードに含めることはできません。

## <a name="implement-an-azure-function-to-sign-tokens"></a>Azure 関数を実装してトークンに署名する

セキュリティトークンプロバイダーを構築するための1つのオプションは、HTTPS エンドポイントを作成し、そのエンドポイントに対して認証された HTTPS 要求を作成してトークンを取得する TokenProvider 実装することです。 これにより、*テナントの秘密鍵* を [Azure Key Vault](../../key-vault/general/overview.md) などの安全な場所に保存することができます。

完全なソリューションには、次の2つの要素があります。

1. 要求を受け入れ、Azure 流体リレートークンを返す HTTPS エンドポイント。
1. エンドポイントへの URL を受け取り、そのエンドポイントへの要求を作成してトークンを取得する ITokenProvider 実装。

### <a name="create-an-endpoint-for-your-tokenprovider-using-azure-functions"></a>Azure Functions を使用して、TokenProvider のエンドポイントを作成します

[Azure Functions](../../azure-functions/functions-overview.md) は、このような HTTPS エンドポイントをすばやく作成する方法です。 この例では、**AzureFunctionTokenProvider** というクラスにそのパターンを実装します。 それによって Azure 関数の URL、`userId`、および `userName` が受け入れられます。 この特定の実装は、`@fluidframework/azure-client` パッケージからのエクスポートとしても提供されます。

この例では、テナント キーを渡すことによってトークンをフェッチする独自の **HTTPTrigger Azure 関数** を作成する方法を示します。

```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions";
import { ScopeType } from "@fluidframework/azure-client";
import { generateToken } from "@fluidframework/azure-service-utils";

// NOTE: retrieve the key from a secure location.
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
        return;
    }

    if (!key) {
        context.res = {
            status: 404,
            body: `No key found for the provided tenantId: ${tenantId}`,
        };
        return;
    }

    if (!documentId) {
        context.res = {
            status: 400,
            body: "No documentId provided in query params"
        };
        return;
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

`@fluidframework/azure-service-utils`に含まれる`generateToken`機能は、テナントのシークレット キーを使用して署名された、指定されたユーザーのトークンを生成する関数です。 これにより、シークレットを公開せずにトークンをクライアントに返すことができます。 代わりに、トークンはシークレットを使用してサーバー側でされ、指定されたドキュメントへのスコープ指定されたアクセスを提供します。 次に示す ITokenProvider の例では、トークンを取得するために、この Azure 関数に対する HTTP 要求を作成しています。

### <a name="deploy-the-azure-function"></a>Azure 関数のデプロイ

Azure Functions はいくつかの方法で展開できます。 Azure Functions の展開の詳細については、 [Azure Functions ドキュメント](../../azure-functions/functions-continuous-deployment.md)の「**Deploy**」セクションを参照してください。

### <a name="implement-the-tokenprovider"></a>TokenProvider を実装する

TokenProviders はさまざまな方法で実装できますが、との2つの異なる API 呼び出しを実装する必要があり 、`fetchOrdererToken`と`fetchStorageToken`です。 これらの API は、流動的な注文サービスと storage サービスのトークンをそれぞれフェッチする役割を担います。 どちらの関数からも、トークン値を表す `TokenResponse`オブジェクトが返されます。 流動フレームワークランタイムは、トークンを取得するために、必要に応じてこれらの2つの API を呼び出します。


テナント シークレット キーは、安全な状態を確保するため、セキュリティで保護されたバックエンドの場所に格納され、Azure 関数内からのみアクセスできます。 トークンを取得するには、デプロイさ れた Azure 関数に対して `GET` または `POST` を要求する必要があります。これには、`tenantID`および`documentId`、および`userID`/`userName`を提供します。 Azure 関数でテナント ID とテナント キー シークレットとの間のマッピングを行い、トークンを適切に生成して署名します。

次の例の実装では、 [axios](https://www.npmjs.com/package/axios) ライブラリを使用して HTTP 要求を行います。 他のライブラリまたは方法を使用して サーバー コードから HTTP 要求を行うことができます。

```typescript
import { ITokenProvider, ITokenResponse } from "@fluidframework/routerlicious-driver";
import axios from "axios";
import { AzureMember } from "./interfaces";

/**
 * Token Provider implementation for connecting to an Azure Function endpoint for
 * Azure Fluid Relay token resolution.
 */
export class AzureFunctionTokenProvider implements ITokenProvider {
    /**
     * Creates a new instance using configuration parameters.
     * @param azFunctionUrl - URL to Azure Function endpoint
     * @param user - User object
     */
    constructor(
        private readonly azFunctionUrl: string,
        private readonly user?: Pick<AzureMember, "userId" | "userName" | "additionalDetails">,
    ) { }

    public async fetchOrdererToken(tenantId: string, documentId?: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }

    public async fetchStorageToken(tenantId: string, documentId: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }

    private async getToken(tenantId: string, documentId: string | undefined): Promise<string> {
        const response = await axios.get(this.azFunctionUrl, {
            params: {
                tenantId,
                documentId,
                userId: this.user?.userId,
                userName: this.user?.userName,
                additionalDetails: this.user?.additionalDetails,
            },
        });
        return response.data as string;
    }
}
```
## <a name="see-also"></a>こちらもご覧ください

- [認証トークンにカスタムデータを追加する](connect-fluid-azure-service.md#adding-custom-data-to-tokens)
- [方法: Azure Static Web Apps を使用して流動的なアプリケーションをデプロイする](deploy-fluid-static-web-apps.md)
