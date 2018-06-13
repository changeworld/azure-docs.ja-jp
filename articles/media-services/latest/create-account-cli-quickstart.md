---
title: クイック スタート - CLI 2.0 で Azure Media Services アカウントを作成する | Microsoft Docs
description: Azure Media Services アカウントを作成するには、このクイック スタートの手順に従います。
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: f7e5cb28f90466e9366c0a32e2a333e6823b9396
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
ms.locfileid: "33779720"
---
# <a name="quickstart-create-an-azure-media-services-account"></a>クイック スタート - Azure Media Services アカウントを作成する

> [!NOTE]
> Azure Media Services の最新バージョン (2018-03-30) はプレビューです。 このバージョンは v3 とも呼ばれます。 

開発者でもメディア コンテンツ作成者でも、Azure でメディア コンテンツを保存、暗号化、エンコード、管理、ストリーム配信するには、Media Services アカウントを作成する必要があります。 Media Services アカウントを作成するときは、Azure Storage アカウント リソースの ID を指定する必要があります。 指定されたストレージ アカウントは、Media Services アカウントに関連付けられます。 このストレージ アカウント リソースは、Media Services アカウントと同じリージョンに配置する必要があります。  

このクイック スタートでは、CLI 2.0 を使って新しい Azure Media Services アカウントを作成する手順について説明します。  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Azure にログインする

[Azure portal](http://portal.azure.com) にログインし、以降の手順で示すように CLI コマンドを実行するために **CloudShell** を起動します。

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="set-the-azure-subscription"></a>Azure サブスクリプションを設定する

次のコマンドで、Media Services アカウントで使用する Azure サブスクリプション ID を指定します。 [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) に移動することで、アクセス権があるサブスクリプションの一覧を表示できます。

```azurecli-interactive
az account set --subscription <mySubscriptionId>
```

## <a name="create-an-azure-resource-group"></a>Azure リソース グループを作成する

次のコマンドでは、ストレージ アカウントと Media Services アカウントを保持するリソース グループを作成します。 *myresourcegroup* プレースホルダーは、リソース グループに使う名前に置き換えます。

```azurecli-interactive
az group create -n <myresourcegroup> -l westus2
```

## <a name="create-an-azure-storage-account"></a>Azure Storage アカウントの作成

Media Services アカウントを作成するときは、Azure Storage アカウント リソースの ID を指定する必要があります。 指定されたストレージ アカウントは、Media Services アカウントに関連付けられます。 

1 つの**プライマリ** ストレージ アカウントを持つ必要があります。Media Services アカウントに関連付けられた任意の数の **セカンダリ** ストレージ アカウントを持つことができます。 Media Services は、**汎用 v2** (GPv2) アカウントまたは**汎用 v1** (GPv1) アカウントをサポートします。 BLOB のみのアカウントを**プライマリ**として使用することはできません。 ストレージ アカウントについて詳しくは、「[Azure Storage アカウントの種類](../../storage/common/storage-account-options.md)」をご覧ください。 

次のコマンドでは、Media Services アカウント (プライマリ) と関連付けるストレージ アカウントを作成します。 次のスクリプトの *storageaccountforams* プレースホルダーを置き換えます。 "account_name" は 24 文字未満にする必要があります。

```azurecli-interactive
az storage account create -n <storageaccountforams> -g <myresourcegroup>
```

## <a name="create-an-azure-media-services-account"></a>Azure Media Services アカウントの作成

次に示すのは新しい Media Services アカウントを作成する Azure CLI コマンドです。 次の強調表示された値を置き換える必要があります。

* *myamsaccountname*
* *myresourcegroup*
* *storageaccountforams*

```azurecli-interactive
az ams create -n <myamsaccountname> -g <myresourcegroup> --storage-account <storageaccountforams>
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートで作成した Media Services アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、リソース グループを削除します。

**CloudShell** で、次のコマンドを実行します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ファイルのストリーミング](stream-files-dotnet-quickstart.md)
