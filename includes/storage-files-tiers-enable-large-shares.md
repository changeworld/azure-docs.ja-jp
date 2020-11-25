---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 327e86642041a607ada7c1173bb053b12a41832c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011729"
---
既定では、Standard ファイル共有に使用できるのは最大 5 TiB だけですが、共有の制限は 100 TiB に増やすことができます。 これを行うには、"*大きなファイル共有*" 機能をストレージ アカウント レベルで有効にする必要があります。 Premium ストレージ アカウント (*FileStorage* ストレージアカウント) の場合は、すべての Premium ファイル共有が既に 100 TiB の最大容量までプロビジョニングできるようになっているため、大きなファイル共有機能のフラグはありません。

大きなファイル共有を有効にできるのは、ローカル冗長またはゾーン冗長の Standard ストレージ アカウントだけです。 大きなファイル共有機能フラグを有効にした後では、冗長性レベルを geo 冗長ストレージまたは geo ゾーン冗長ストレージに変更することはできません。

既存のストレージ アカウントで大きなファイル共有を有効にするには、ストレージ アカウントの目次で **[構成]** ビューに移動し、大きなファイル共有のロッカー スイッチを有効に切り替えます。

![Azure portal での大きなファイル共有有効化ロッカー スイッチのスクリーンショット](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

[`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) PowerShell コマンドレットおよび [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) Azure CLI コマンドを使用して、100 TiB のファイル共有を有効にすることもできます。 大きなファイル共有を有効にする詳しい手順については、「[大きなファイル共有の有効化と作成](../articles/storage/files/storage-files-how-to-create-large-file-share.md)」を参照してください。

新しいストレージ アカウントにファイル共有を作成にする方法の詳細については、[Azure ファイル共有の作成](../articles/storage/files/storage-how-to-create-file-share.md)に関する記事を参照してください。