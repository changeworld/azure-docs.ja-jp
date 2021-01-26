---
title: Azure CLI のサンプル スクリプト - 変換の作成 | Microsoft Docs
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
ms.openlocfilehash: 4784bb8a396a30e9002c789c7aad382640f04a5c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "95996902"
---
# <a name="create-a-transform"></a>変換を作成する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事の Azure CLI スクリプトは、変換の作成方法を示しています。 トランスフォームは、ビデオまたはオーディオ ファイルを処理するためのタスクの単純なワークフローを記述します (多くの場合、"レシピ" と呼ばれます)。 必要な名前と "レシピ" を持つトランスフォームが既に存在するかどうかを必ず確認する必要があります。 その場合は再利用する必要があります。

## <a name="prerequisites"></a>前提条件

[Media Services アカウントを作成する](./create-account-howto.md)

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

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

[変換とジョブについての詳細情報](transforms-jobs-concept.md)
