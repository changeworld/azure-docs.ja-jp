---
title: Azure CLI のサンプル スクリプト - 変換の作成 | Microsoft Docs
description: トランスフォームは、ビデオまたはオーディオ ファイルを処理するためのタスクの単純なワークフローを記述します (多くの場合、"レシピ" と呼ばれます)。 この記事の Azure CLI スクリプトは、変換の作成方法を示しています。
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
ms.date: 05/01/2019
ms.author: juliako
ms.custom: devx-track-azurecli
ms.openlocfilehash: 538f48d427a4d8b51f77ae50bb0bee95909f0b09
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494470"
---
# <a name="cli-example-create-a-transform"></a>CLI の例: 変換の作成

この記事の Azure CLI スクリプトは、変換の作成方法を示しています。 トランスフォームは、ビデオまたはオーディオ ファイルを処理するためのタスクの単純なワークフローを記述します (多くの場合、"レシピ" と呼ばれます)。 必要な名前と "レシピ" を持つトランスフォームが既に存在するかどうかを必ず確認する必要があります。 その場合は再利用する必要があります。

## <a name="prerequisites"></a>前提条件 

[Media Services アカウントを作成する](./create-account-howto.md)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> 指定できるのは、カスタム Standard Encoder プリセットの JSON ファイルに対するパスだけです。[StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) については、[カスタム変換を使用したエンコード](custom-preset-cli-howto.md)の例を参照してください。
>
> [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) を使用している場合にファイル名を渡すことはできません。

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>次のステップ

[az ams transform (CLI)](/cli/azure/ams/transform?view=azure-cli-latest)
