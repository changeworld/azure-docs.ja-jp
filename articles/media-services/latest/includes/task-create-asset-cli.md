---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 76c6a67b0bad6095a6340ccab4fbd49c7cad1698
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2021
ms.locfileid: "107893248"
---
<!--Create a media services asset CLI-->

以下の Azure CLI コマンドは、新しい Media Services アセットを作成します。 値 `assetName` `amsAccountName` および `resourceGroup` を、操作中の値に置き換えてください。

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```
