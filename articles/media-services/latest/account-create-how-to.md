---
title: Azure Media Services アカウントの作成
description: このチュートリアルでは、Azure Media Services アカウントを作成する手順について説明します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/4/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7c6486c156cfc9cec9065854c55b2d8e2788fdc0
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110062569"
---
# <a name="create-a-media-services-account"></a>Media Services アカウントを作成する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure で暗号化、エンコード、分析、管理、およびメディア コンテンツのストリーミングを開始するには、Media Services アカウントを作成する必要があります。 Media Services アカウントは、1 つまたは複数のストレージ アカウントに関連付ける必要があります。 この記事では、新しい Azure Media Services アカウントを作成する手順について説明します。

[!INCLUDE [note 2020-05-01 API](./includes/note-2020-05-01-account-creation.md)]

 Azure portal または CLI のいずれかを使用して、Media Services アカウントを作成できます。 使用するメソッドのタブを選択します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

<!-- NOTE: The following are in the includes folder and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

## <a name="portal"></a>[ポータル](#tab/portal/)

[!INCLUDE [the media services account and storage account must be in the same subscription](./includes/note-account-storage-same-subscription.md)]

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [the media services account and storage account must be in the same subscription](./includes/note-account-storage-same-subscription.md)]

## <a name="set-the-azure-subscription"></a>Azure サブスクリプションを設定する

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

Media Services アカウントの作成では、Azure Storage アカウント リソースの名前を指定する必要があります。 指定されたストレージ アカウントは、Media Services アカウントに関連付けられます。 Media Services で使用されるストレージ アカウントの使用方法について詳しくは、「[ストレージ アカウント](storage-account-concept.md)」をご覧ください。

1 つの **プライマリ** ストレージ アカウントを持つ必要があります。Media Services アカウントに関連付けられた任意の数の **セカンダリ** ストレージ アカウントを持つことができます。 Media Services は、**汎用 v2** (GPv2) アカウントまたは **汎用 v1** (GPv1) アカウントをサポートします。 BLOB のみのアカウントを **プライマリ** として使用することはできません。 ストレージ アカウントについて詳しくは、「[Azure Storage アカウントの種類](../../storage/common/storage-account-overview.md)」をご覧ください。

この例では、General Purpose v2、Standard LRS アカウントを作成します。 ストレージ アカウントで実験する場合は、`--sku Standard_LRS` を使用します。 ただし、運用環境用の SKU を選択する場合は、ビジネス継続性のために地理的レプリケーションを提供する `--sku Standard_RAGRS` を検討してください。 詳細については、[ストレージ アカウント](/cli/azure/storage/account)に関するページを参照してください。

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account"></a>Media Services アカウントを作成する

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

---

## <a name="next-steps"></a>次のステップ

[ファイルのストリーミング](stream-files-dotnet-quickstart.md)