---
author: baanders
description: Azure Digital Twins インスタンスをクリーンアップするためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 0783f25813fa8e4a65a33975017e2b2d03a4dd2c
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110075496"
---
* **このチュートリアルで作成したリソースがすべて不要である場合**、この記事で使用した Azure Digital Twins インスタンスとその他すべてのリソースを [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) コマンドで削除できます。 そのリソース グループとそこに含まれるすべての Azure リソースが削除されます。
    
    > [!IMPORTANT]
    > リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。
    
    [Azure Cloud Shell](https://shell.azure.com) を開き、次のコマンドを実行すると、リソース グループとそこに含まれる内容がすべて削除されます。
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```