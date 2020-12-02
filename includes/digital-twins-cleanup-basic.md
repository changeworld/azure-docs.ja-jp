---
author: baanders
description: 基本的な Azure Digital Twins インスタンスをクリーンアップするためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 4c03ef942896dda63f678018cdd257024cfbb6d4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011297"
---
このチュートリアルで作成したリソースが不要であれば、次の手順に従って削除してください。

[Azure Cloud Shell](https://shell.azure.com) から [az group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) コマンドを使用すると、リソース グループ内の Azure リソースをすべて削除できます。 このコマンドにより、リソース グループと Azure Digital Twins インスタンスが削除されます。

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。

Azure Cloud Shell を開き、次のコマンドを実行すると、リソース グループとそこに含まれる内容がすべて削除されます。

```azurecli-interactive
az group delete --name <your-resource-group>
```