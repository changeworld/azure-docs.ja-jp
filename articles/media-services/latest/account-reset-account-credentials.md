---
title: アカウントの資格情報をリセットする - CLI
description: Azure CLI スクリプトを使用して、アカウントの資格情報をリセットし、app.config 設定を取得します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: d5604f177484d33255d2923d72b00fae124c0f9a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783595"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Azure CLI の例: アカウント資格情報のリセット

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事の Azure CLI スクリプトは、アカウントの資格情報をリセットし、app.config 設定を取得する方法を示しています。

## <a name="prerequisites"></a>前提条件

[Media Services アカウントを作成する](./create-account-howto.md)

## <a name="example-script"></a>サンプル スクリプト

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>次のステップ

* [az ams](/cli/azure/ams)
* [資格情報をリセットする](/cli/azure/ams/account/sp#az_ams_account_sp_reset_credentials)
