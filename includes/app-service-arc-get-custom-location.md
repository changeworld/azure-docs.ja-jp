---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: d4d6783645f719f21d97f18abc9bc900a368fd8f
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112361744"
---
クラスター管理者から、カスタムの場所に関する次の情報を取得します ([カスタムの場所の作成](../articles/app-service/manage-create-arc-environment.md#create-a-custom-location)に関するページを参照してください)。

```azurecli-interactive
customLocationGroup="<resource-group-containing-custom-location>"
customLocationName="<name-of-custom-location>"
```

次の手順用にカスタムの場所の ID を取得します。

```azurecli-interactive
customLocationId=$(az customlocation show \
    --resource-group $customLocationGroup \
    --name $customLocationName \
    --query id \
    --output tsv)
```
