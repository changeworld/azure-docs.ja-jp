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
ms.openlocfilehash: 8a2e5defd0672516d52d4f3477641f39eca63368
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597828"
---
既定では、Standard ファイル共有に使用できるのは最大 5 TiB だけですが、共有の制限は 100 TiB に増やすことができます。 これを行うには、"*大きなファイル共有*" 機能をストレージ アカウント レベルで有効にする必要があります。 Premium ストレージ アカウント (*FileStorage* ストレージアカウント) の場合は、すべての Premium ファイル共有が既に 100 TiB の最大容量までプロビジョニングできるようになっているため、大きなファイル共有機能のフラグはありません。

大きなファイル共有を有効にできるのは、ローカル冗長またはゾーン冗長の Standard ストレージ アカウントだけです。 大きなファイル共有機能フラグを有効にした後では、冗長性レベルを geo 冗長ストレージまたは geo ゾーン冗長ストレージに変更することはできません。

既存のストレージ アカウントで大きなファイル共有を有効にするには、ストレージ アカウントの目次で **[構成]** ビューに移動し、大きなファイル共有のロッカー スイッチを有効に切り替えます。

![Azure portal での大きなファイル共有有効化ロッカー スイッチのスクリーンショット](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

[`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) PowerShell コマンドレットおよび [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) Azure CLI コマンドを使用して、100 TiB のファイル共有を有効にすることもできます。

新しいストレージ アカウントで大きなファイル共有を有効にする方法の詳細については、[Azure ファイル共有の作成](../articles/storage/files/storage-how-to-create-file-share.md)に関する記事をご覧ください。