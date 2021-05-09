---
author: baanders
description: Azure Digital Twins インスタンスをクリーンアップするためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 0d8cc30c0511098caf7b6c47d7f7bd400dc32f1b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799955"
---
* **このチュートリアルで作成したリソースがすべて不要である場合**、この記事で使用した Azure Digital Twins インスタンスとその他すべてのリソースを [az group delete](/cli/azure/group#az_group_delete) コマンドで削除できます。 そのリソース グループとそこに含まれるすべての Azure リソースが削除されます。
    
    > [!IMPORTANT]
    > リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。
    
    [Azure Cloud Shell](https://shell.azure.com) を開き、次のコマンドを実行すると、リソース グループとそこに含まれる内容がすべて削除されます。
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```