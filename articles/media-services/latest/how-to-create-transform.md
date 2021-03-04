---
title: Azure CLI のサンプル スクリプト - 変換の作成
description: トランスフォームは、ビデオまたはオーディオ ファイルを処理するためのタスクの単純なワークフローを記述します (多くの場合、"レシピ" と呼ばれます)。 この記事の Azure CLI スクリプトは、変換の作成方法を示しています。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4623610960d8f21a2dab3293c7499a2112416254
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101718915"
---
# <a name="create-a-transform"></a>変換を作成する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事の Azure CLI スクリプトは、変換の作成方法を示しています。 トランスフォームは、ビデオまたはオーディオ ファイルを処理するためのタスクの単純なワークフローを記述します (多くの場合、"レシピ" と呼ばれます)。 必要な名前と "レシピ" を持つトランスフォームが既に存在するかどうかを必ず確認する必要があります。 その場合は再利用する必要があります。

## <a name="prerequisites"></a>前提条件

[Media Services アカウントを作成する](./create-account-howto.md)

## <a name="cli"></a>[CLI](#tab/cli/)

> [!NOTE]
> 指定できるのは、カスタム Standard Encoder プリセットの JSON ファイルに対するパスだけです。[StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) については、[カスタム変換を使用したエンコード](custom-preset-cli-howto.md)の例を参照してください。
>
> [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) を使用している場合にファイル名を渡すことはできません。

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="rest"></a>[REST](#tab/rest/)

[!INCLUDE [task general transform creation](./includes/task-create-basic-audio-rest.md)]

---

## <a name="next-steps"></a>次のステップ

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
