---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 10/07/2021
ms.author: alkohli
ms.openlocfilehash: 8c823b6709dbd5010ec48c2fbca185d9a211cb40
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000112"
---
Data Box デバイスでサポートされるストレージ アカウントとストレージの種類の一覧を次に示します。 全種類のストレージ アカウントを対象とする全機能の完全一覧については、「[ストレージ アカウントの種類](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts)」を参照してください。

#### <a name="supported-storage-accounts-for-imports"></a>インポートでサポートされているストレージ アカウント

インポート注文については、サポートされているストレージ アカウントを次の表に示します。

| **ストレージ アカウント/サポートされるストレージの種類** | **ブロック BLOB** |**ページ BLOB**<sup>1</sup> |**Azure Files** |**サポートされているアクセス層**|
| --- | --- | -- | -- | -- |
| クラシック Standard | Y | Y | Y |
| 汎用 v1 Standard  | Y | Y | Y | ホット、クール |
| 汎用 v1 Premium  |  | Y| | |
| 汎用 v2 Standard<sup>2</sup>  | Y | Y | Y | ホット、クール|
| 汎用 v2 Premium  |  |Y | |  |
| Azure Premium FileStorage |  |  | Y |  |  
| Blob ストレージ Standard | Y | | | ホット、クール |


<sup>1</sup> "*ページ BLOB にアップロードされるデータは、VHD など、512 バイトに揃えられている必要があります。* "

<sup>2</sup> "*Azure Data Lake Storage Gen2 (ADLS Gen2) はインポートではサポートされていますが、エクスポートではサポートされていません。* "


#### <a name="supported-storage-accounts-for-exports"></a>エクスポートでサポートされているストレージ アカウント

エクスポートの順序については、サポートされているストレージ アカウントを次の表に示します。

| **ストレージ アカウント/サポートされるストレージの種類** | **ブロック BLOB** |**ページ BLOB** _ |_ *Azure Files** |**サポートされているアクセス層**|
| --- | --- | -- | -- | -- |
| クラシック Standard | Y | Y | Y | |
| 汎用 v1 Standard  | Y | Y | Y | ホット、クール |
| 汎用 v1 Premium  |  | Y| | |
| 汎用 v2 Standard  | Y | Y | Y | ホット、クール |
| 汎用 v2 Premium  |  |Y | | |
| Azure Premium FileStorage |  |  | Y |  |
| Blob ストレージ Standard |Y | | | ホット、クール |
| ブロック BLOB ストレージ Premium |Y | | | ホット、クール |
| ページ BLOB ストレージ Premium | |Y | | |

#### <a name="caveats-for-storage-accounts"></a>ストレージ アカウントに関する注意事項

- 汎用アカウントの場合:
  - Data Box では、インポート注文に対し、キュー、テーブル、およびディスクのストレージの種類をサポートしていません。
  - Data Box では、エクスポート注文に対し、キュー、テーブル、ディスク、および Azure Data Lake Gen2 のストレージの種類をサポートしていません。
- Data Box では、Blob Storage アカウントとブロック BLOB ストレージ アカウントの追加 BLOB がサポートされていません。
- Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポートは、Data Box ではサポートされていません。
- ページ BLOB にアップロードされるデータは、VHD など、512 バイトに揃えられている必要があります。
- エクスポートの場合:
  - 最大 80 TB をエクスポートできます。
  - ファイル履歴と BLOB スナップショットはエクスポートされません。
  - アーカイブ BLOB はエクスポートできません。 エクスポート前に、アーカイブ層で BLOB をリハイドレートします。 詳細については、「[アーカイブ済み BLOB をオンライン層にリハイドレートする](../articles/storage/blobs/archive-rehydrate-overview.md)」を参照してください。