---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 288539542c5b811206bd3e128dbceca317b2a27a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130137851"
---
## <a name="connections"></a>接続

`connection` プロパティは、アプリを Service Bus に接続する方法を指定する環境構成への参照です。 次のものが指定されている場合があります。

- [接続文字列](#connection-string)を含むアプリケーション設定の名前
- まとめて [ID ベースの接続](#identity-based-connections)を定義する、複数のアプリケーション設定の共有プレフィックスの名前。

構成された値が、1 つの設定に完全一致し、その他の設定のプレフィックスとも一致する場合、完全一致が使用されます。

### <a name="connection-string"></a>接続文字列

接続文字列は、[管理資格情報の取得](../articles/service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)に関する記事の手順に従って取得します。 接続文字列は、特定のキューまたはトピックに限らず、Service Bus 名前空間のものである必要があります。

この接続文字列は、バインディング構成の `connection` プロパティで指定した値と一致する名前のアプリケーション設定に格納する必要があります。

アプリ設定の名前が "AzureWebJobs" で始まる場合は、名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyServiceBus" に設定した場合、Functions ランタイムは "AzureWebJobsMyServiceBus" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の "AzureWebJobsServiceBus" という名前の既定の Service Bus 接続文字列を使用します。

### <a name="identity-based-connections"></a>ID ベースの接続

[バージョン 5.x 以上の拡張機能](../articles/azure-functions/functions-bindings-service-bus.md#service-bus-extension-5x-and-higher)を使用する場合は、シークレットを含む接続文字列を使う代わりに、アプリで [Azure Active Directory ID](../articles/active-directory/fundamentals/active-directory-whatis.md) を使用することができます。 これを行うには、トリガーおよびバインディング構成の `connection` プロパティにマップされる共通のプレフィックスに設定を定義します。

このモードでは、拡張機能に次のプロパティが必要です。

| プロパティ                  | 環境変数テンプレート                       | 説明                                | 値の例 |
|---------------------------|-----------------------------------------------------|--------------------------------------------|---------|
| 完全修飾名前空間 | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | Service Bus の完全修飾名前空間。 | <service_bus_namespace>.servicebus.windows.net  |

接続をカスタマイズするために、追加のプロパティを設定できます。 「[ID ベース接続に共通のプロパティ](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections)」を参照してください。

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-service-bus-permissions](./functions-service-bus-permissions.md)]