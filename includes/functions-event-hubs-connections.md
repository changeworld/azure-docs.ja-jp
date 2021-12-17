---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 3d1ebc666a1d4c6763b080decdd50fd44b3837dc
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993316"
---
## <a name="connections"></a>接続

`connection` プロパティは、アプリを Event Hubs に接続する方法を指定する環境構成への参照です。 次が指定されている場合があります。

- [接続文字列](#connection-string)を含むアプリケーション設定の名前
- 合わせて [ID ベースの接続](#identity-based-connections)を定義する、複数のアプリケーション設定の共有のプレフィックスの名前。

構成された値が、1 つの設定に完全一致し、その他の設定のプレフィックスとも一致する場合、完全一致が使用されます。

### <a name="connection-string"></a>接続文字列

この接続文字列を取得するには、イベント ハブ自体ではなく、"[名前空間](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)" の **[接続情報]** をクリックします。 接続文字列は、イベントハブ自体ではなく、Event Hubs 名前空間のものである必要があります。

トリガーに使用する場合、接続文字列には、機能を有効にするために少なくとも "読み取り" アクセス許可が必要です。 出力バインディングに使用する場合、接続文字列には、イベント ストリームにメッセージを送信するための "送信" アクセス許可が必要です。

この接続文字列は、バインディング構成の `connection` プロパティで指定した値と同じ名前のアプリケーション設定に格納する必要があります。

### <a name="identity-based-connections"></a>ID ベースの接続

シークレットを含む接続文字列を使用するのではなく、[拡張機能のバージョン 5.x 以降](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)を使用している場合は、アプリで [Azure Active Directory の ID](../articles/active-directory/fundamentals/active-directory-whatis.md) を使用できます。 これを行うには、トリガーとバインドの構成の `connection` プロパティにマップされる共通のプレフィックスに設定を定義します。

このモードでは、拡張機能に次のプロパティが必要です。

| プロパティ                  | 環境変数テンプレート                       | 説明                                | 値の例                                        |
|---------------------------|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| 完全修飾名前空間 | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | 完全修飾 Event Hubs の名前空間。 | <event_hubs_namespace>.servicebus.windows.net |

接続のカスタマイズには、プロパティを追加設定します。 [ID ベースの接続に共通のプロパティ](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections)に関するページを参照してください。

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-event-hubs-permissions](./functions-event-hubs-permissions.md)]
