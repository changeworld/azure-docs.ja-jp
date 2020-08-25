---
title: Azure CLI を使用して Azure Media Services 資産にコンテンツをアップロードする
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
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7b362ba30baf13211dc41a6043dcedd05bd506ea
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585418"
---
# <a name="create-an-asset"></a>アセットを作成する

この記事では、Media Services 資産を作成する方法について説明します。  エンコードとストリーミング用のメディア コンテンツを保持するには、資産を使用します。  Media Services 資産の詳細については、「[Azure Media Services v3 のアセット](assets-concept.md)」を参照してください

## <a name="prerequisites"></a>前提条件

「[Media Services アカウントを作成する](./create-account-howto.md)」の手順に従って、資産を作成するために必要な Media Services アカウントとリソース グループを作成します。

## <a name="methods"></a>メソッド

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="cli-shell"></a>[CLI シェル](#tab/clishell/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

---

## <a name="next-steps"></a>次のステップ

[資産を管理する](manage-asset-concept.md)
