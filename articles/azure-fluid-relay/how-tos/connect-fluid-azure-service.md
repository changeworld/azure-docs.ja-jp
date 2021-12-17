---
title: '方法: Azure Fluid Relay サービスに接続する'
description: '@fluidframework/azure-client ライブラリを使用して Azure Fluid Relay サービスに接続する方法。'
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/deployment/azure-frs/
ms.openlocfilehash: a2093b063e5c2e048bdf0a0a418a8046348032c8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849579"
---
# <a name="how-to-connect-to-an-azure-fluid-relay-service"></a>方法: Azure Fluid Relay サービスに接続する

> [!NOTE]
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

この記事では、Azure Fluid Relay サービスをプロビジョニングして、すぐに使用できるようにするための手順について説明します。 

> [!IMPORTANT]
> アプリを Azure Fluid Relay サーバーに接続する前に、Azure アカウントで [Azure Fluid Relay サーバー](provision-fluid-azure-portal.md) リソースをプロビジョニングする必要があります。

Azure Fluid Relay サービスは、クラウドでホストされる Fluid サービスです。 Fluid アプリケーションを Azure Fluid Relay インスタンスに接続するには、`@fluidframework/azure-client` パッケージ内の `AzureClient` を使用します。 `AzureClient` では、コンテナー オブジェクト自体はサービスに依存しない状態のまま、[Fluid コンテナー](https://fluidframework.com/docs/build/containers/)をサービスに接続するロジックを処理します。 このクライアントの 1 つのインスタンスを使用して、複数のコンテナーを管理できます。

以下のセクションでは、お使いのアプリケーションで `AzureClient` を使用する方法について説明します。

## <a name="connecting-to-the-service"></a>サービスへの接続

Azure Fluid Relay インスタンスに接続するには、最初に `AzureClient`を作成する必要があります。 サービスに対して現在のユーザーを承認するために使用される JSON Web Token (JWT) を生成するには、テナント ID、注文者とストレージ URL、トークン プロバイダーなどの構成パラメーターを指定する必要があります。 `@fluidframework/test-client-utils` パッケージには、開発目的で使用できる `InsecureTokenProvider` が用意されています。

> [!CAUTION]
> `InsecureTokenProvider` は、**使用することによって、クライアント側のコード バンドルでテナント キー シークレットが公開されるため**、開発目的でのみ使用する必要があります。 これを、テナント キーによる署名を担当する独自のバックエンド サービスからトークンを取り込む `ITokenProvider` の実装に置き換える必要があります。

```javascript
const config = {
  tenantId: "myTenantId",
  tokenProvider: new InsecureTokenProvider("myTenantKey", { id: "userId" }),
  orderer: "https://myOrdererUrl",
  storage: "https://myStorageUrl",
};

const clientProps = {
  connection: config,
};

const client = new AzureClient(clientProps);
```

`AzureClient`のインスタンスが作成されたので、それを使用して Fluid コンテナーを作成するか、または読み込むことができます。

### <a name="token-providers"></a>トークン プロバイダー

[AzureFunctionTokenProvider](https://github.com/microsoft/FluidFramework/blob/main/packages/framework/azure-client/src/AzureFunctionTokenProvider.ts) は `ITokenProvider` の実装です。これにより、テナント キー シークレットがクライアント側のバンドル コードで公開されることはありません。 `AzureFunctionTokenProvider` は、現在のユーザー オブジェクトと共に、`/api/GetAzureToken` によって追加された Azure 関数の URL を取り込みます。 後で、tenantId、documentId、userId または userName をオプションのパラメーターとして渡して、Azure 関数に対して `GET` 要求を行います。

```javascript
const config = {
  tenantId: "myTenantId",
  tokenProvider: new AzureFunctionTokenProvider(
    "myAzureFunctionUrl" + "/api/GetAzureToken",
    { userId: "userId", userName: "Test User" }
  ),
  orderer: "https://myOrdererUrl",
  storage: "https://myStorageUrl",
};

const clientProps = {
  connection: config,
};

const client = new AzureClient(clientProps);
```

### <a name="adding-custom-data-to-tokens"></a>トークンへのカスタム データの追加

ユーザー オブジェクトでは、省略可能な追加のユーザーの詳細も保持することができます。 次に例を示します。

```javascript
const userDetails = {
  email: "xyz@outlook.com",
  address: "Redmond",
};

const config = {
  tenantId: "myTenantId",
  tokenProvider: new AzureFunctionTokenProvider(
    "myAzureFunctionUrl" + "/api/GetAzureToken",
    { userId: "UserId", userName: "Test User", additionalDetails: userDetails }
  ),
  orderer: "https://myOrdererUrl",
  storage: "https://myStorageUrl",
};
```

Azure 関数は、テナントのシークレット キーを使用して署名された特定のユーザーのトークンを生成し、シークレット自体を公開せずにクライアントに返します。

## <a name="managing-containers"></a>コンテナーを管理する

`AzureClient` API は、それぞれコンテナーを作成および取得する `createContainer` 関数と `getContainer` 関数を公開します。 どちらの関数も、コンテナー データ モデルを定義する "_コンテナー スキーマ_" を取り込みます。 `getContainer` 関数の場合、取り込むコンテナーのコンテナー `id` の追加パラメーターがあります。

コンテナーの作成シナリオでは、次のパターンを使用できます。

```javascript
const schema = {
  initialObjects: {
    /* ... */
  },
  dynamicObjectTypes: [
    /*...*/
  ],
};
const azureClient = new AzureClient(clientProps);
const { container, services } = await azureClient.createContainer(
  schema
);
const id = await container.attach();
```

`container.attach()` 呼び出しは、コンテナーが実際にサービスに接続され、その BLOB ストレージに記録される場合です。 これは、このコンテナー インスタンスの一意識別子である `id` を返します。

同じコラボレーション セッションに参加するクライアントは、同じコンテナー `id` で `getContainer` を呼び出す必要があります。

```javascript
const { container, services } = await azureClient.getContainer(
  id,
  schema
);
```

Fluid によって出力されるログの記録を開始する方法の詳細については、「[ログとテレメトリ](https://fluidframework.com/docs/testing/telemetry/)」を参照してください。

取り込まれるコンテナーは、コンテナー スキーマで定義されている `initialObjects` を保持します。 スキーマを確立し、`container` オブジェクトを使用する方法の詳細については、fluidframework.com の「[データ モデリング](https://fluidframework.com/docs/build/data-modeling/)」を参照してください。

## <a name="getting-audience-details"></a>対象ユーザーの詳細の取得

`createContainer` および `getContainer` を呼び出すと、`container` オブジェクト (上記) と `services` オブジェクトの 2 つの値が返されます。

`container` には Fluid データ モデルが含まれており、これはサービスに依存しません。 `AzureClient` によって返されるこのコンテナー オブジェクトに対して記述したコードは、クライアントで別のサービスに再利用できます。 この例として、`TinyliciousClient` を使用してシナリオのプロトタイプを作成した場合、Fluid コンテナー内の共有オブジェクトと対話するすべてのコードを `AzureClient` の使用に移行するときに再利用できます。

`services` オブジェクトには、Azure Fluid Relay サービスに固有のデータが含まれます。 このオブジェクトには、現在コンテナーに接続しているユーザーの名簿を管理するために使用できる `audience` 値が含まれます。

次のコードは、`audience` オブジェクトを使用して、現在コンテナー内にあるすべてのメンバーの更新されたビューを維持する方法を示しています。

```javascript
const { audience } = containerServices;
const audienceDiv = document.createElement("div");

const onAudienceChanged = () => {
  const members = audience.getMembers();
  const self = audience.getMyself();
  const memberStrings = [];
  const useAzure = process.env.FLUID_CLIENT === "azure";

  members.forEach((member) => {
    if (member.userId !== (self ? self.userId : "")) {
      if (useAzure) {
        const memberString = `${member.userName}: {Email: ${member.additionalDetails ? member.additionalDetails.email : ""},
                        Address: ${member.additionalDetails ? member.additionalDetails.address : ""}}`;
        memberStrings.push(memberString);
      } else {
        memberStrings.push(member.userName);
      }
    }
  });
  audienceDiv.innerHTML = `
            Current User: ${self ? self.userName : ""} <br />
            Other Users: ${memberStrings.join(", ")}
        `;
};

onAudienceChanged();
audience.on("membersChanged", onAudienceChanged);
```

`audience` により、ユーザー ID とユーザー名を含む `AzureMember` オブジェクトを返す次の 2 つの関数が提供されます。

- `getMembers` は、コンテナーに接続されているすべてのユーザーのマップを返します。 これらの値は、メンバーがコンテナーに参加する場合またはコンテナーから離れる場合にいつでも変更されます。
- `getMyself` は、このクライアント上の現在のユーザーを返します。

`audience` では、メンバーの名簿が変更された場合のイベントも生成されます。 `membersChanged` は名簿の変更に対して発生しますが、`memberAdded` と `memberRemoved` は、`clientId` および `member` の値の変更によるそれぞれの変更に対して発生します。 これらのイベントが発生すると、`getMembers` に対する新しい呼び出しによって、更新されたメンバー名簿が返されます。

サンプルの`AzureMember` オブジェクトは、次のようになります。

```json
{
  "userId": "0e662aca-9d7d-4ff0-8faf-9f8672b70f15",
  "userName": "Test User",
  "connections": [
    {
      "id": "c699c3d1-a4a0-4e9e-aeb4-b33b00544a71",
      "mode": "write"
    },
    {
      "id": "0e662aca-9d7d-4ff0-8faf-9f8672b70f15",
      "mode": "write"
    }
  ],
  "additionalDetails": {
    "email": "xyz@outlook.com",
    "address": "Redmond"
  }
}
```

`AzureMember` オブジェクトは、ユーザー ID、名前、追加の詳細に加えて、`connections` の配列も保持します。 ユーザーが 1 つのクライアントのみを使用してセッションにログインしている場合、`connections`には、クライアントの ID を含む 1 つの値のみが含まれ、読み取り/書き込みモードになります。 ただし、同じユーザーが複数のクライアントからログインしている場合 (つまり、異なるデバイスからログインしている場合、または同じコンテナーで複数のブラウザー タブを開いている場合)、`connections` はクライアントごとの複数の値を保持します。 上記のデータ例では、名前が "Test User" で、ID が "0e662aca-9d7d-4ff0-8faf-9f8672b70f15" のユーザーが、現在 2 つの異なるクライアントからコンテナーを開いていることがわかります。 `additionalDetails` フィールドの値は、`AzureFunctionTokenProvider` トークン生成で提供された値と一致します。

これらの関数とイベントを組み合わせて、現在のセッション内のユーザーのリアルタイム ビューを表示できます。

**お疲れさまでした。** これで、Fluid コンテナーが Azure Fluid Relay サービスに正常に接続され、コラボレーション セッション内のメンバーに関するユーザーの詳細が取り込まれました。
