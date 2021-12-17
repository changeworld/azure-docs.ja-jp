---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: 1f026d0a8d038e532b9b0ec070a4849ebc8e8530
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803418"
---
ここでは、ストレージ アカウントにコピーするデータのサイズ制限を示します。 アップロードするデータがこれらの制限に準拠していることを確認してください。 これらの制限に関する最新情報については、「[BLOB ストレージのスケーラビリティとパフォーマンスのターゲット](../articles/storage/blobs/scalability-targets.md)」と「[Azure Files のスケーラビリティとパフォーマンスのターゲット](../articles/storage/files/storage-files-scale-targets.md)」を参照してください。 

| Azure ストレージ アカウントにコピーするデータのサイズ                      | 既定の制限          |
|---------------------------------------------------------------------|------------------------|
| ブロック BLOB とページ BLOB                                            | 上限は [Azure サブスクリプションに対して定義されているストレージの上限](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)と同じであり、Data Box を含むすべてのソースのデータが含まれています。   |
| Azure Files                                                          | <ul><li>Data Box で、Data Box の注文を作成する前に有効にした場合、大きなファイル共有 (100 TiB) がサポートされます。 <ul><li>順序の作成の前に大きなファイル共有を有効にしなかった場合、サポートされるファイル共有の最大サイズは 5 TiB です。</li><li>データのアップロード中のパフォーマンスを向上させるために、[ストレージ アカウントで大きいファイルの共有を有効にし、共有容量を 100 TiB に増やす](../articles/storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account)ことをお勧めします。 大きいファイルの共有は、ローカル冗長ストレージ (LRS) があるストレージ アカウントでのみサポートされます。</li></ul><li>Data Box では Azure Premium ファイル共有がサポートされます。これにより、ストレージ アカウント内のすべての共有で合計 100 TiB が許可されます。<ul><li>コピー ログと監査ログで使用される領域があるため、使用可能な最大容量は若干少なくなります。 コピー ログと監査ログ用に、それぞれ少なくとも 100 GiB が予約されています。 詳細については、[Azure Data Box、Azure Data Box Heavy の監査ログ](../articles/databox/data-box-audit-logs.md)に関する記事を参照してください。</li><li>*StorageAccount_AzFile* の下のすべてのフォルダーはこの制限に従う必要があります。 詳細については、「[Azure ファイル共有を作成する](../articles/storage/files/storage-how-to-create-file-share.md)」を参照してください。</li></ul></li></ul> |
