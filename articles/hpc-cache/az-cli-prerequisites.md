---
title: Azure HPC Cache の Azure CLI の前提条件
description: Azure CLI を使用して Azure HPC Cache を作成または変更する前のセットアップ手順
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: femila
ms.openlocfilehash: 90d139cf2e839544eb94545f010b2fa0d917e8cb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016114"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Azure HPC Cache 向けに Azure CLI を設定する

Azure CLI を使用して Azure HPC Cache を作成または管理する前に、次の手順に従って環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Azure HPC Cache には、Azure CLI のバージョン 2.7 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="set-default-resource-group-optional"></a>既定のリソース グループを設定する (省略可能)

ほとんどの hpc-cache コマンドでは、キャッシュのリソース グループを渡す必要があります。 既定のリソース グループを設定するには、[az config](/cli/azure/reference-index#az_config) を使用します。

## <a name="next-steps"></a>次のステップ

Azure CLI 拡張機能をインストールしてログインすると、Azure CLI を使用して Azure HPC Cache システムを作成および管理できます。

* [Azure HPC キャッシュを作成する](hpc-cache-create.md)
* [Azure CLI hpc-cache のドキュメント](/cli/azure/hpc-cache)
