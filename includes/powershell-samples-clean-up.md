---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 10/19/2021
ms.author: cephalin
ms.openlocfilehash: a1128c7e2e8a89c8d60e629437a12e2c1378babc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456000"
---
## <a name="clean-up-resources"></a>リソースをクリーンアップする

前の手順では、リソース グループ内に Azure リソースを作成しました。 今後これらのリソースが不要である場合は、次の PowerShell コマンドを実行してリソース グループを削除します。

```azurecli-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

このコマンドの実行には、少し時間がかかる場合があります。