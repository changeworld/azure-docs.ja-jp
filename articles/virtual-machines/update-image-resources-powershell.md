---
title: PowerShell を使用してイメージ リソースを一覧表示、更新、削除する
description: Azure PowerShell を使用して、共有イメージ ギャラリー内のイメージ リソースを一覧表示、更新、および削除します。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/27/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: c6bc4fea05f79c446f318eb4cbc86beb160ea108
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793967"
---
# <a name="list-update-and-delete-image-resources-using-powershell"></a>PowerShell を使用してイメージ リソースを一覧表示、更新、削除する 

Azure PowerShell を使用して、共有イメージ ギャラリーのリソースを管理できます。

[!INCLUDE [virtual-machines-common-gallery-list-ps.md](../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]

## <a name="next-steps"></a>次のステップ

[Azure Image Builder (プレビュー)](./linux/image-builder-overview.md) は、イメージ バージョンの作成の自動化に役立ちます。イメージ バージョンの更新や、[既存のイメージ バージョンからの新しいイメージ バージョンの作成](./linux/image-builder-gallery-update-image-version.md)に使用することさえできます。