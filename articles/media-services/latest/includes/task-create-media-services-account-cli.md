---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 26ae372b6e431247d2038445daafcb3c997a232d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513295"
---
<!--Create a media services account -->

新しい Media Services アカウントは、以下の Azure CLI コマンドで作成します。 次の値を置き換えることができます: `amsaccount` と `storageaccountforams` (ストレージ アカウントに指定した値に一致している必要があります)、`amsResourceGroup` (リソース グループに指定した値に一致している必要があります)。

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```
