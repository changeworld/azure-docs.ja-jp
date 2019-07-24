---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180979"
---
## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトが実行された後で、次のコマンドを使用して、リソース グループ、Azure Cache for Redis インスタンス、およびリソース グループ内のすべての関連リソースを削除できます。

```azurecli
az group delete --name contosoGroup
```