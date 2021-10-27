---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/11/2021
ms.author: mahender
ms.openlocfilehash: 8cef6aa5daa8bbbdda6971724343e65ea7e47aef
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992775"
---
Azure Functions サービスでホストされている場合、ID ベースの接続では、[マネージド ID](../articles/app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json) が使用されます。 ユーザー割り当て ID を `credential` および `clientID` プロパティで指定できますが、システム割り当て ID が既定で使用されます。 ローカル開発などの他のコンテキストで実行する場合は、代わりに開発者 ID が使用されますが、カスタマイズすることもできます。 [ID ベースの接続によるローカル開発](../articles/azure-functions/functions-reference.md#local-development-with-identity-based-connections)に関するページをご覧ください。

#### <a name="grant-permission-to-the-identity"></a>ID にアクセス許可を付与する

使用されている ID が何であれ、目的のアクションを実行するためのアクセス許可が必要です。 これらのアクセス許可を提供する組み込みロールまたはカスタム ロールを使用して、[Azure RBAC でロールを割り当てる](../articles/role-based-access-control/role-assignments-steps.md)必要があります。

> [!IMPORTANT]
> すべてのコンテキストに必要ではない一部のアクセス許可がターゲット サービスによって公開される場合があります。 可能であれば、**最小限の特権の原則** に従い、必要な特権だけを ID に付与します。 たとえば、アプリがデータ ソースからの読み取りのみを行う必要がある場合は、読み取りアクセス許可のみを持つロールを使用します。 サービスへの書き込みも可能なロールを割り当てることは、読み取り操作に対するアクセス許可が過剰になるため、不適切です。 同様に、ロールの割り当てが、読み取る必要のあるリソースだけに限定されていることを確認する必要があります。
