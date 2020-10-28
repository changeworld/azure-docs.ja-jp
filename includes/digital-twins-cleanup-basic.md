---
author: baanders
description: 基本的な Azure Digital Twins インスタンスをクリーンアップするためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 29a07ffa917153c0cb062d34e2807d43d039a373
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494676"
---
このチュートリアルで作成したリソースが不要であれば、次の手順に従って削除してください。

[Azure Cloud Shell](https://shell.azure.com) から [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) コマンドを使用すると、リソース グループ内の Azure リソースをすべて削除できます。 リソース グループと Azure Digital Twins インスタンスが削除されます。

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 

Azure Cloud Shell を開き、次のコマンドを実行すると、リソース グループとそこに含まれる内容がすべて削除されます。

```azurecli-interactive
az group delete --name <your-resource-group>
```