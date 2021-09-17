---
title: Azure CLI を使用したイメージ リソースの一覧表示、更新、削除
description: Azure CLI を使用して、共有イメージ ギャラリーのイメージ リソースを一覧表示、更新、削除します。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: f011e4e57ed5516bf5d7ce0332614ac80cef64dd
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698682"
---
# <a name="list-update-and-delete-image-resources"></a>イメージ リソースの一覧表示、更新、削除 

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

Azure CLI を使用して、共有イメージ ギャラリー リソースを管理できます。

[!INCLUDE [virtual-machines-common-gallery-list-cli.md](../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>次のステップ

[Azure Image Builder (プレビュー)](./image-builder-overview.md) は、イメージ バージョンの作成の自動化に役立ちます。イメージ バージョンの更新や、[既存のイメージ バージョンからの新しいイメージ バージョンの作成](./linux/image-builder-gallery-update-image-version.md)に使用することさえできます。