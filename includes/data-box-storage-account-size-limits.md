---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: acaebcea59e765f5544f1bfbd692c6508f66a84a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "91025124"
---
ここでは、ストレージ アカウントにコピーするデータのサイズ制限を示します。 アップロードするデータがこれらの制限に準拠していることを確認してください。 これらの制限に関する最新情報については、「[BLOB ストレージのスケーラビリティとパフォーマンスのターゲット](../articles/storage/blobs/scalability-targets.md)」と「[Azure Files のスケーラビリティとパフォーマンスのターゲット](../articles/storage/files/storage-files-scale-targets.md)」を参照してください。

| Azure ストレージ アカウントにコピーするデータのサイズ                      | 既定の制限          |
|---------------------------------------------------------------------|------------------------|
| ブロック BLOB とページ BLOB                                            | 上限は [Azure サブスクリプションに対して定義されているストレージの上限](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)と同じであり、Data Box を含むすべてのソースのデータが含まれています。 |
| Azure Files                                                          | Data Box で、Data Box の順序を作成する前に有効にした場合、大きなファイル共有 (100 TiB) がサポートされます。 <br> 順序の作成の前に有効にしなかった場合、サポートされるファイル共有の最大サイズは 5 TiB です。 <br> Premium ファイル共有は、まだサポートされていません。<br> *StorageAccount_AzureFiles* の下のすべてのフォルダーはこの制限に従う必要があります。 <br> 詳しくは、「[大きなファイル共有の有効化と作成](../articles/storage/files/storage-files-how-to-create-large-file-share.md)」を参照してください。      |
