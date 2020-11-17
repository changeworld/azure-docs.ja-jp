---
author: baanders
description: 基本的な Azure Digital Twins インスタンスをクリーンアップするためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: ab179c5f0c56a7d6fd2ebd41e13d27a832512b54
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358096"
---
このチュートリアルで作成したリソースが不要であれば、次の手順に従って削除してください。

[Azure Cloud Shell](https://shell.azure.com) から [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) コマンドを使用すると、リソース グループ内の Azure リソースをすべて削除できます。 このコマンドにより、リソース グループと Azure Digital Twins インスタンスが削除されます。

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。

Azure Cloud Shell を開き、次のコマンドを実行すると、リソース グループとそこに含まれる内容がすべて削除されます。

```azurecli-interactive
az group delete --name <your-resource-group>
```