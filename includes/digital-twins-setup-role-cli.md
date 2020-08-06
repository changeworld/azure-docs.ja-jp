---
author: baanders
description: Azure Digital Twins 設定でのロールの要件のためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 1714fac465531a2d3a5829e7080132510ccd1ec4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407480"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>前提条件:権限の要件

この記事のすべての手順を完了するには、Azure サブスクリプションの所有者として分類されている必要があります。 

Cloud Shell で次のコマンドを実行することによって、アクセス許可レベルを確認できます。

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

所有者である場合は、出力の `roleDefinitionName` 値が *Owner* になります。

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="az role assignment list コマンドの出力を示す Cloud Shell ウィンドウ":::

この値が *Contributor* または *Owner* 以外の何かであることがわかった場合は、次のいずれかの方法で処理を続行できます。
* サブスクリプションの所有者に連絡し、その所有者に、この記事の手順を自分の代わりに完了するよう要求します。
* 自分で処理を続行するためのアクセス許可が付与されるように、サブスクリプションの所有者またはサブスクリプションのユーザー アクセス管理者ロールを持つだれかに連絡し、自分をサブスクリプションの所有者に昇格させるよう要求します。 これが適切かどうかは、ユーザーの組織やその中でのロールによって異なります。