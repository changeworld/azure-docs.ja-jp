---
title: アセットにコンテンツをアップロードする - CLI
description: このトピックの Azure CLI スクリプトは、コンテンツのアップロード先の Media Services 資産を作成する方法を示しています。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/16/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0b722b302434a547e56c12e92eaa134fa76ae07e
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105963706"
---
# <a name="create-an-asset"></a>アセットを作成する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、Media Services 資産を作成する方法について説明します。  エンコードとストリーミング用のメディア コンテンツを保持するには、資産を使用します。  Media Services 資産の詳細については、「[Azure Media Services v3 のアセット](assets-concept.md)」を参照してください

## <a name="prerequisites"></a>前提条件

「[Media Services アカウントを作成する](./account-create-how-to.md)」の手順に従って、資産を作成するために必要な Media Services アカウントとリソース グループを作成します。

## <a name="methods"></a>メソッド

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST](#tab/rest/)

### <a name="using-rest"></a>REST の使用

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>cURL の使用

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>次のステップ

[Media Services の概要](media-services-overview.md)
