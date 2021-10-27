---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 204e2b629740bba53154f3a43fa4e723e3a73248
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994348"
---
## <a name="connections"></a>接続

`connectionStringSetting`/`connection` プロパティと `leaseConnectionStringSetting`/`leaseConnection` プロパティは、アプリを Azure Cosmos DB に接続する方法を指定する環境構成への参照です。 以下を指定できます。

- [接続文字列](#connection-string)を含むアプリケーション設定の名前。
- まとめて [ID ベースの接続](#identity-based-connections)を定義する、複数のアプリケーション設定の共有プレフィックスの名前。 このオプションは、[バージョン4.x 以降の拡張機能]の `connection` および `leaseConnection` バージョンでのみ使用できます。

構成された値が、1 つの設定に完全一致し、プレフィックスがその他の設定とも一致する場合は、完全一致が使用されます。

### <a name="connection-string"></a>接続文字列

お使いのデータベース アカウントの接続文字列を、バインディング構成の接続プロパティで指定した値と同じ名前のアプリケーション設定に格納する必要があります。

### <a name="identity-based-connections"></a>ID ベースの接続

[バージョン 4.x 以上の拡張機能]を使用する場合は、シークレットを含む接続文字列を使う代わりに、アプリで [Azure Active Directory ID](../articles/active-directory/fundamentals/active-directory-whatis.md) を使用できます。 これを行うには、トリガーおよびバインディング構成の接続プロパティにマップされる共通のプレフィックスに設定を定義します。

このモードでは、拡張機能に次のプロパティが必要です。

| プロパティ                  | 環境変数テンプレート                       | 説明                                | 値の例                                        |
|---------------------------|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| アカウント エンドポイント | `<CONNECTION_NAME_PREFIX>__accountEndpoint` | Azure Cosmos DB アカウント エンドポイント URI。 | <database_account_name>.documents.azure.com |

接続をカスタマイズするために、追加のプロパティを設定できます。 「[ID ベース接続に共通のプロパティ](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections)」を参照してください。

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-cosmos-permissions](./functions-cosmos-permissions.md)]

[バージョン4.x 以降の拡張機能]: ../articles/azure-functions/functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher
