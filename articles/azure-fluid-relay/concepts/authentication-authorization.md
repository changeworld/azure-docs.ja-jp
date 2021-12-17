---
title: アプリの認証と承認
description: Azure Fluid Relay サービスで認証と承認を使用する方法の概要。
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/auth/
ms.openlocfilehash: e2f24c5455548980318c4536b5c65f84ea6a7dfb
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893825"
---
# <a name="authentication-and-authorization-in-your-app"></a>アプリの認証と承認

> [!NOTE]
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

セキュリティは、最新の Web アプリケーションにとって重要です。 Web アプリケーション アーキテクチャの一部としての Fluid Framework は、セキュリティで保護するインフラストラクチャの重要な部分です。 Fluid Framework は階層化アーキテクチャであり、認証関連の概念は接続先の Fluid サービスに基づいて実装されます。 つまり、すべての Fluid サービスで共通の認証テーマはあるものの、詳細と特性はサービスごとに異なります。

## <a name="azure-fluid-relay-service"></a>Azure Fluid Relay サービス

作成する各 Azure Fluid Relay サービス テナントには、**テナント ID** と独自の一意の **テナント シークレット キー** が割り当てられます。

シークレット キーは **共有シークレット** です。 アプリまたはサービスでそれが認識されてから、Azure Fluid Relay サービスでそれが認識されます。 テナント シークレット キーはテナントに一意に関連付けられているため、それを使用して要求に署名することで、その要求がテナントの承認されたユーザーからのものであることが Azure Fluid Relay サービスに保証されます。

シークレット キーは、要求がアプリまたはサービスからのものであることを Azure Fluid Relay サービスで認識する方法です。 これは重要です。Azure Fluid Relay サービスでは、要求が ''*アプリ*'' からのものであることを信頼できると、送信されるデータを信頼できるためです。 これも、シークレットを安全に処理することが重要な理由です。

> [!CAUTION]
> シークレットにアクセスできるすべてのユーザーは、Azure Fluid Relay サービスと通信するときにアプリケーションを偽装できます。

## <a name="json-web-tokens-and-azure-fluid-relay-service"></a>JSON Web Token と Azure Fluid Relay サービス

Azure Fluid Relay では [JSON Web Token (JWT)](https://jwt.io/) を使用して、シークレット キーで署名されたデータをエンコードして検証します。 JSON Web Token は、権限とアクセス許可に関する追加情報を含めることができる JSON の符号付きビットです。

> [!NOTE]
> JWT の特性については、この記事では説明しません。 JWT 標準の詳細については、<https://jwt.io/introduction> を参照してください。

認証の詳細は Fluid サービスによって異なりますが、いくつかの値は常に存在する必要があります。

- **containerId**  Fluid サービスでは、呼び出し元のコンテナーに対応するサービスを識別するためにコンテナー ID が必要です。 *注*: JWT ではこの documentId フィールドを呼び出しますが、Fluid サービスではこのフィールドにコンテナー ID が必要です。
- **tenantId**: Azure Fluid Relay サービスではテナント ID を使用して、要求の認証に使用する共有シークレットを取得します。 
- **scopes**: scopes では、呼び出し元コンテナーのアクセス許可を定義します。 scopes フィールドの内容は柔軟であり、独自のカスタム アクセス許可を作成できます。

```json {linenos=inline,hl_lines=["5-6",13]}
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "documentId": "azureFluidDocumentId",
  "scopes": [ "doc:read", "doc:write", "summary:write" ],
  "user": {
    "name": "TestUser",
    "id": "Test-Id-123"
  },
  "iat": 1599098963,
  "exp": 1599098963,
  "tenantId": "AzureFluidTenantId",
  "ver": "1.0"
}.[Signature]
```

ユーザーのモードからは、接続が読み取りモードか、読み取り/書き込みモードかが示されます。 これは `AzureAudience` の `connections` フィールドで確認できます。 トークン スコープ アクセス許可は、サーバーレス Azure Function の `generateToken` 関数で更新できます。

```ts
const token = generateToken(
  tenantId,
  documentId,
  key,
  scopes ?? [ "Token Scope" ],
  user
);
```

トークン スコープ、コンテナーの動作、モードは次のようになります。

| トークン スコープ | マイ ドキュメントの動作 | 対象ユーザー ドキュメントの動作 | 
|-------------|----------------------|----------------------------|
| DocRead     | ドキュメントを読み取り、ドキュメントに書き込みます。 ドキュメントに加えられた変更は、他の対象ユーザー ドキュメントには反映されません。 <br /> モード: 読み取り | ドキュメントを読み取り、ドキュメントに書き込みます。 変更は、他の対象ユーザー ドキュメントには反映されません。 <br /> モード: 書き込み | 
| DocWrite    | ドキュメントを読み取り、ドキュメントに書き込みます。 加えられた変更は、他のすべての対象ユーザー ドキュメントに反映されます。 <br />モード: 書き込み | ドキュメントを読み取り、ドキュメントに書き込みます。 加えられた変更は、他のすべての対象ユーザー ドキュメントに反映されます。 <br />モード: 書き込み |
| DocRead、DocWrite | ドキュメントを読み取り、ドキュメントに書き込みます。 加えられた変更は、他のすべての対象ユーザー ドキュメントに反映されます。 <br />モード: 書き込み | ドキュメントを読み取り、ドキュメントに書き込みます。 加えられた変更は、他のすべての対象ユーザー ドキュメントに反映されます。 <br />モード: 書き込み |

> [!NOTE]
> トークンにはユーザー情報も含まれていることに注意してください (上記の 7 行目から 9 行目を参照)。 これを使用すると、[対象ユーザー](../how-tos/connect-fluid-azure-service.md#getting-audience-details)機能を使って、Fluid コードで自動的に使用可能になるユーザー情報を拡張できます。 詳細については、「[トークンへのカスタム データの追加](../how-tos/connect-fluid-azure-service.md#adding-custom-data-to-tokens)」を参照してください。

## <a name="the-token-provider"></a>トークン プロバイダー

Azure Fluid Relay へのすべての要求には、有効な JWT を使用して署名する必要があります。 Fluid により、これらのトークンを作成して署名する責任がトークン プロバイダーに委ねられます。

トークン プロバイダーは、`@fluidframework/azure-client` で Azure Fluid Relay サービスへの要求を行う際に使用されるトークンの作成と署名を担当します。 独自のセキュリティで保護されたトークン プロバイダーの実装を提供する必要があります。 しかし、Fluid により、テナント シークレットを受け入れてからローカルで署名済みトークンを生成して返す `InsecureTokenProvider` が提供されます。 このトークン プロバイダーはテストに役立ちますが、運用シナリオでは使用できません。

### <a name="a-secure-serverless-token-provider"></a>セキュリティで保護されたサーバーレス トークン プロバイダー

セキュリティで保護されたトークン プロバイダーを構築するためのオプションの 1 つは、サーバーレス Azure 関数を作成し、トークン プロバイダーとして公開することです。 これにより、セキュリティで保護されたサーバーに ''*テナント シークレット キー*'' を格納できます。 その後、アプリケーションでは Azure 関数を呼び出して、`InsecureTokenProvider` の場合のようにローカルで署名するのではなく、トークンを生成します。 詳細については、「[方法: Azure 関数を使用して TokenProvider を記述する](../how-tos/azure-function-token-provider.md)」を参照してください。

## <a name="connecting-user-auth-to-fluid-service-auth"></a>Fluid サービス認証へのユーザー認証の関連付け

Fluid サービスでは、特定のユーザーに関連付けられていない共有クライアント シークレットを使用して、着信呼び出しを認証します。 ユーザー認証は、Fluid サービスの詳細に基づいて追加できます。 

ユーザー認証の簡単なオプションの 1 つは、トークン プロバイダーとして [Azure 関数](../../azure-functions/index.yml)を使用し、トークンを取得する条件としてユーザー認証を適用することです。 アプリケーションで関数を呼び出そうとすると、認証システムで認証されていない限り失敗します。 たとえば、Azure Active Directory (Azure AD) を使用している場合は、Azure 関数用の Azure AD アプリケーションを作成し、それを組織の認証システムに関連付けることができます。

この場合、ユーザーは、Azure 関数の呼び出しに使用するトークンを取得する際に経由する Azure AD を使用して、アプリケーションにサインインします。 Azure 関数自体も同じように動作しますが、現在アクセスできるのは、Azure AD でも認証されたユーザーのみです。

現在、Azure 関数は有効なトークンを取得するエントリ ポイントであるため、関数に対して適切に認証されたユーザーのみが、そのトークンをクライアント アプリケーションから Azure Fluid Relay サービスに提供できます。 この 2 段階のアプローチでは、Azure Fluid Relay サービスと組み合わせて独自のカスタム認証プロセスを使用できます。

## <a name="see-also"></a>こちらもご覧ください

- [JWT の概要](https://jwt.io/introduction)
- [JSON Web Token を作成する方法](../how-tos/fluid-json-web-token.md)
- [Azure Fluid Relay のペイロードのクレーム](../how-tos/fluid-json-web-token.md#payload-claims)
