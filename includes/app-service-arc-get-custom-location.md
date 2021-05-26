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
ms.openlocfilehash: a20a4a4b3c618a03ba7b5f788395bb541d2c143d
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371991"
---
クラスター管理者から、カスタムの場所に関する次の情報を取得します ([カスタムの場所の作成](../articles/app-service/manage-create-arc-environment.md#create-a-custom-location)に関するページを参照してください)。

```azurecli-interactive
customLocationGroup="<resource-group-containing-custom-location>"
customLocationName=<name-of-custom-location>
```

次の手順用にカスタムの場所の ID を取得します。

```azurecli-interactive
customLocationId=$(az customlocation show \
    --resource-group $customLocationGroup \
    --name $customLocationName \
    --query id \
    --output tsv)
```