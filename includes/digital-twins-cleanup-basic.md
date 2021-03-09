---
author: baanders
description: Azure Digital Twins インスタンスをクリーンアップするためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: c3c1b814b357a2e4b724590261657e485852f99c
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575691"
---
* **このチュートリアルで作成したリソースがすべて不要である場合**、この記事で使用した Azure Digital Twins インスタンスとその他すべてのリソースを [az group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) コマンドで削除できます。 そのリソース グループとそこに含まれるすべての Azure リソースが削除されます。
    
    > [!IMPORTANT]
    > リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。
    
    [Azure Cloud Shell](https://shell.azure.com) を開き、次のコマンドを実行すると、リソース グループとそこに含まれる内容がすべて削除されます。
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```