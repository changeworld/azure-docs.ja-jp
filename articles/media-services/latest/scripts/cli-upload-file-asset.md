---
title: Azure CLI スクリプトのサンプル - コンテナーへのファイルのアップロード | Microsoft Docs ドキュメント
description: Azure CLI スクリプトを使用して、ローカル ファイルをストレージ コンテナーにアップロードします。
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
ms.openlocfilehash: bb619987ec3ccc0ed8efcc927c72225d1e88f4ee
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
---
# <a name="cli-example-upload-a-local-file-to-a-container"></a>CLI サンプル: コンテナーへのローカル ファイルのアップロード 

この記事の Azure CLI スクリプトでは、ローカル ファイルをストレージ コンテナーにアップロードする方法を示します。

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.20 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/upload-file-asset/UploadFile-Asset.sh "Upload a file")]

## <a name="next-steps"></a>次の手順

他の例については、[Azure CLI のサンプル](../cli-samples.md)をご覧ください。
