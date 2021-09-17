---
title: PowerShell を使用してイメージ リソースを一覧表示、更新、削除する
description: Azure PowerShell を使用して、共有イメージ ギャラリー内のイメージ リソースを一覧表示、更新、および削除します。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/27/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 91664719f8af1cd44336a7701cd561eeea6d8279
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698416"
---
# <a name="list-update-and-delete-image-resources-using-powershell"></a>PowerShell を使用してイメージ リソースを一覧表示、更新、削除する 

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

Azure PowerShell を使用して、共有イメージ ギャラリーのリソースを管理できます。

[!INCLUDE [virtual-machines-common-gallery-list-ps.md](../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]

## <a name="next-steps"></a>次のステップ

[Azure Image Builder (プレビュー)](./image-builder-overview.md) は、イメージ バージョンの作成の自動化に役立ちます。イメージ バージョンの更新や、[既存のイメージ バージョンからの新しいイメージ バージョンの作成](./linux/image-builder-gallery-update-image-version.md)に使用することさえできます。