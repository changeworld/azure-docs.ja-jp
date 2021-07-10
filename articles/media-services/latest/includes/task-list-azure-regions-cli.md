---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 05/13/2021
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 0e216c73efce9a4f11f1ba0886ddcdb9324ed107
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103253"
---
<!--List Azure regions CLI-->

このコマンドを使用すると、アカウントで使用可能なリージョンの一覧が表示されます。

```azurecli
az account list-locations --query "[].{DisplayName:displayName, Name:name}" -o table
```
