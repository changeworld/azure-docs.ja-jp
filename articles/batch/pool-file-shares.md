---
title: Azure Batch プールの Azure ファイル共有
description: Azure Batch の Linux または Windows プールの計算ノードから Azure Files 共有をマウントする方法
ms.topic: how-to
ms.date: 08/23/2021
ms.openlocfilehash: 2c5cbf1a3e5d74927ddc74c4838c31f68a348876
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772116"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Batch プールと共に Azure ファイル共有を使用する

Azure Files はクラウドで、[Server Message Block (SMB) プロトコル](../storage/files/storage-files-introduction.md)を介してアクセスできる、完全に管理されたファイル共有を提供します。 Batch プールのコンピューティング ノードに Azure ファイル共有をマウントして使用することができます。

## <a name="considerations-for-use-with-batch"></a>Batch と共に使用するための考慮事項

Premium 以外の Azure Files を使用する場合は、比較的少数の並行タスクを実行するプールがあるときに Azure ファイル共有の使用を検討します。 [パフォーマンスとスケールの対象](../storage/files/storage-files-scale-targets.md)を確認し、必要なプール サイズとアセット ファイル数を仮定して、(Azure Storage アカウントを利用する) Azure Files を使用する必要があるかを判断します。

Azure ファイル共有は[コスト効率が高く](https://azure.microsoft.com/pricing/details/storage/files/)、グローバルに冗長になるように、別のリージョンへのデータ レプリケーションで構成できます。

オンプレミス コンピューターから Azure ファイル共有を同時にマウントできます。 ただし、特に REST API を使用する場合は、[コンカレンシーの影響](../storage/blobs/concurrency-manage.md)について確実に理解しておきます。

Azure ファイル共有の一般的な[計画に関する考慮事項](../storage/files/storage-files-planning.md)についても確認してください。

## <a name="create-a-file-share"></a>ファイル共有を作成する

Batch アカウントに関連付けられたストレージ アカウント、または別個のストレージ アカウントに、ファイル共有を作成できます。 詳細については、「[Azure ファイル共有を作成する](../storage/files/storage-how-to-create-file-share.md)」を参照してください。

## <a name="mount-an-azure-file-share-on-a-batch-pool"></a>Batch プールに Azure ファイル共有をマウントする

プールに Azure ファイル共有をマウントする方法の詳細については、「[仮想ファイル システムを Batch プールにマウントする](virtual-file-mount.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- Batch のデータの読み書きのその他のオプションについては、「[ジョブとタスク出力を保持する](batch-task-output.md)」を参照してください。
- また、Batch コンテナー ワークロードにファイル システムをデプロイするための [Shipyard レシピ](https://github.com/Azure/batch-shipyard/tree/master/recipes)が含まれる [Batch Shipyard](https://github.com/Azure/batch-shipyard) ツールキットも調べてください。
