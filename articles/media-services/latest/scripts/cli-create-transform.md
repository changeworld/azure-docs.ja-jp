---
title: Azure CLI のサンプル スクリプト - 変換の作成 | Microsoft Docs
description: Azure CLI スクリプトを使用して変換を作成します。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/11/2018
ms.author: juliako
ms.openlocfilehash: 8cc6b779729bf0c596a7c1b7318eed6f47dffb1d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
---
# <a name="cli-example-create-a-transform"></a>CLI の例: 変換の作成

この記事の Azure CLI スクリプトは、変換の作成方法を示しています。 変換では、ビデオまたはオーディオ ファイルを処理するためのタスクの単純なワークフローを記述します (多くの場合、"レシピ" と呼ばれます)。 必要な名前と "レシピ" を持つ変換が既に存在するかどうかを必ず確認する必要があります。 その場合は再利用する必要があります。

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.20 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>次の手順

他の例については、[Azure CLI のサンプル](../cli-samples.md)をご覧ください。
