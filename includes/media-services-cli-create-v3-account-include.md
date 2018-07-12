---
title: インクルード ファイル
description: インクルード ファイル
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 04/13/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 9ecb07a2cb278f6cde4ffdc3b252cb9e816d08da
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38733306"
---
## <a name="create-a-media-services-account"></a>Media Services アカウントを作成する

最初に、Media Services アカウントを作成する必要があります。 このセクションでは、Azure CLI を使用してアカウントを作成するために必要な作業について説明します。

### <a name="create-a-resource-group"></a>リソース グループの作成

リソース グループを作成するには、次のコマンドを使用します。 Azure リソース グループは、Azure Media Services のアカウントや関連するストレージ アカウントなど、各種リソースのデプロイと管理の対象となる論理上のコンテナーです。

```azurecli-interactive
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

Media Services アカウントの作成では、Azure Storage アカウント リソースの名前を指定する必要があります。 指定されたストレージ アカウントは、Media Services アカウントに関連付けられます。 

1 つの**プライマリ** ストレージ アカウントを持つ必要があります。Media Services アカウントに関連付けられた任意の数の **セカンダリ** ストレージ アカウントを持つことができます。 Media Services は、**汎用 v2** (GPv2) アカウントまたは**汎用 v1** (GPv1) アカウントをサポートします。 BLOB のみのアカウントを**プライマリ**として使用することはできません。 ストレージ アカウントについて詳しくは、「[Azure Storage アカウントの種類](../articles/storage/common/storage-account-options.md)」をご覧ください。 

Media Services で使用されるストレージ アカウントの使用方法について詳しくは、「[ストレージ アカウント](../articles/media-services/latest/storage-account-concept.md)」をご覧ください。

Media Services アカウントに関連付けるストレージ アカウントは、以下のコマンドで作成します。 以下のスクリプトの `storageaccountforams` は、実際の値に置き換えてください。 アカウント名は 24 文字未満にする必要があります。

```azurecli-interactive
az storage account create --name storageaccountforams \  
--kind StorageV2 \
--sku Standard_RAGRS \
--resource-group amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Media Services アカウントを作成する

新しい Media Services アカウントは、以下の Azure CLI コマンドで作成します。 `amsaccount` と `storageaccountforams` には、実際のストレージ アカウントに割り当てた値を、`amsResourceGroup` には、リソース グループに割り当てた値を指定してください。

```azurecli-interactive
az ams account create --name amsaccount --resource-group amsResourceGroup --storage-account storageaccountforams
```
