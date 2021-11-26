---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 965a2abb8a56894efc700cea75097526daf4e544
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132529806"
---
## <a name="connections"></a>接続

`connection` プロパティは、アプリを Azure キューに接続する方法を指定する環境構成への参照です。 次が指定されている場合があります。

- [接続文字列](#connection-string)を含むアプリケーション設定の名前
- まとめて [ID ベースの接続](#identity-based-connections)を定義する、複数のアプリケーション設定の共有プレフィックスの名前。

構成された値が、1 つの設定に完全一致し、プレフィックスがその他の設定とも一致する場合は、完全一致が使用されます。

### <a name="connection-string"></a>接続文字列

接続文字列を取得するには、「[ストレージ アカウント アクセス キーを管理する](../articles/storage/common/storage-account-keys-manage.md)」の手順に従います。

この接続文字列は、バインド構成の `connection` プロパティで指定した値と同じ名前のアプリケーション設定に格納する必要があります。

アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "AzureWebJobsMyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。

### <a name="identity-based-connections"></a>ID ベースの接続

[バージョン 5.x 以上の拡張機能](../articles/azure-functions/functions-bindings-storage-queue.md#storage-extension-5x-and-higher)を使用する場合は、シークレットを含む接続文字列の代わりに、アプリで [Azure Active Directory ID](../articles/active-directory/fundamentals/active-directory-whatis.md) を使用することができます。 これを行うには、トリガーおよびバインド構成の `connection` プロパティにマップされる共通のプレフィックスに設定を定義します。

このモードでは、拡張機能に次のプロパティが必要です。

| プロパティ                  | 環境変数テンプレート                       |説明                                | 値の例 |
|---------------------------|-----------------------------------------------------|--------------------------------------------|---------|
| Queue サービス URI | `<CONNECTION_NAME_PREFIX>__queueServiceUri`<sup>1</sup>  | HTTPS スキームを使用して接続している Queue サービスのデータ プレーン URI。 | https://<storage_account_name>.queue.core.windows.net |

<sup>1</sup> `<CONNECTION_NAME_PREFIX>__serviceUri` をエイリアスとして使用できます。 両方の形式が指定された場合、`queueServiceUri` の形式が使用されます。 `serviceUri` 形式は、全体の接続構成が BLOB、キュー、テーブル間で使用される場合は、指定できません。

接続をカスタマイズするために、追加のプロパティを設定できます。 「[ID ベース接続に共通のプロパティ](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections)」を参照してください。

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-queue-permissions](./functions-queue-permissions.md)]