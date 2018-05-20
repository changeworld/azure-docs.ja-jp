---
title: Azure CLI スクリプトの例 - Azure Media Services アカウントを作成する | Microsoft Docs
description: Azure CLI スクリプトを使って、Azure Media Services アカウントを作成します。
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
ms.openlocfilehash: 783dc0fd37f98a12d9240ad4b3ee72aa98212eff
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
---
# <a name="cli-example-create-an-azure-media-services-account"></a>CLI の例: Azure Media Services アカウントを作成する

このトピックの Azure CLI スクリプトは、Azure Media Services アカウントの作成方法を示しています。

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.20 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/media-services-create-account/Create-Account.sh "Create Account")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | ストレージ アカウントを作成します。 |
| [az ams account create](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest#az-ams-account-create) | Media Services アカウントを作成します。 |
| [az ams account sp create](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-create) | パスワードのあるサービス プリンシパルを作成し、Azure Media Services アカウントへのアクセスを構成します。 
| [az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |


## <a name="next-steps"></a>次の手順

他の例については、[Azure CLI のサンプル](../cli-samples.md)をご覧ください。
