---
title: Azure CLI のサンプル スクリプト - アカウント資格情報のリセット | Microsoft Docs
description: Azure CLI スクリプトを使用して、アカウントの資格情報をリセットし、app.config 設定を取得します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/20/2019
ms.author: juliako
ms.openlocfilehash: 63f2abe7a3890efbaf4c79186467a3eb20d8afda
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382989"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Azure CLI の例: アカウント資格情報のリセット

この記事の Azure CLI スクリプトは、アカウントの資格情報をリセットし、app.config 設定を取得する方法を示しています。

## <a name="prerequisites"></a>前提条件

[Media Services アカウントを作成する](create-account-cli-how-to.md)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

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
* [資格情報をリセットする](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
