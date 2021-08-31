---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: 092484b09af008770260c64cd6b7e0ea15265811
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802564"
---
Data Box デバイスでサポートされるストレージ アカウントとストレージの種類の一覧を次に示します。 全種類のストレージ アカウントを対象とする全機能の完全一覧については、「[ストレージ アカウントの種類](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts)」を参照してください。

インポート注文については、サポートされているストレージ アカウントを次の表に示します。

| **ストレージ アカウント/サポートされるストレージの種類** | **ブロック BLOB** |**ページ BLOB** _ |_ *Azure Files** |**メモ**|
| --- | --- | -- | -- | -- |
| クラシック Standard | Y | Y | Y |
| 汎用 v1 Standard  | Y | Y | Y | ホットとクールの両方がサポートされます。|
| 汎用 v1 Premium  |  | Y| | |
| 汎用 v2 Standard  | Y | Y | Y | ホットとクールの両方がサポートされます。|
| 汎用 v2 Premium  |  |Y | | |
| Azure Premium FileStorage |  |  | Y |  |  
| Blob ストレージ Standard |Y | | |ホットとクールの両方がサポートされます。 |

\* *- ページ BLOB にアップロードされるデータは、VHD などの 512 バイトに揃えられている必要があります。*

エクスポートの順序については、サポートされているストレージ アカウントを次の表に示します。

| **ストレージ アカウント/サポートされるストレージの種類** | **ブロック BLOB** |**ページ BLOB** _ |_ *Azure Files** |**サポートされているアクセス層**|
| --- | --- | -- | -- | -- |
| クラシック Standard | Y | Y | Y | |
| 汎用 v1 Standard  | Y | Y | Y | ホット、クール|
| 汎用 v1 Premium  |  | Y| | |
| 汎用 v2 Standard  | Y | Y | Y | ホット、クール|
| 汎用 v2 Premium  |  |Y | | |
| Azure Premium FileStorage |  |  | Y |  |
| Blob ストレージ Standard |Y | | |ホット、クール |
| ブロック BLOB ストレージ Premium |Y | | |ホット、クール |
| ページ BLOB ストレージ Premium | |Y | | |

> [!IMPORTANT]
> - 汎用アカウントの場合、Data Box では、インポート注文に対しキュー、テーブル、およびディスクのストレージの種類をサポートしていません。 エクスポート注文の場合は、Data Box では、汎用アカウントに対しキュー、テーブル、ディスク、および Azure Data Lake Gen 2 のストレージの種類をサポートしていません。
> - Data Box では、Blob Storage アカウントとブロック BLOB ストレージ アカウントの追加 BLOB がサポートされていません。
> - Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポートは、Data Box ではサポートされていません。
> - ページ BLOB にアップロードされるデータは、VHD など、512 バイトに揃えられている必要があります。
> - 最大 80 TB をエクスポートできます。
> - ファイル履歴と BLOB スナップショットはエクスポートされません。
> - アーカイブ BLOB はエクスポートできません。 エクスポート前に、アーカイブ層で BLOB をリハイドレートします。 詳細については、「[アーカイブ済み BLOB をオンライン層にリハイドレートする](../articles/storage/blobs/storage-blob-rehydration.md#rehydrate-an-archived-blob-to-an-online-tier)」を参照してください。
