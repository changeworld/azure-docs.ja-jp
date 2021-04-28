---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 07b0897de3fce6a108836f1e72ce956a3a8092f1
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2021
ms.locfileid: "107893180"
---
<!--Create a media services account -->

新しい Media Services アカウントは、以下の Azure CLI コマンドで作成します。 次の値を置き換えることができます: `amsaccount` と `storageaccountforams` (ストレージ アカウントに指定した値に一致している必要があります)、`amsResourceGroup` (リソース グループに指定した値に一致している必要があります)。

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```
