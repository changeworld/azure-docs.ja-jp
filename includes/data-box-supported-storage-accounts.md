---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/git14/2020
ms.author: alkohli
ms.openlocfilehash: 25c2ea04cd062554a975c63aae9b97846e646d68
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027249"
---
Data Box デバイスでサポートされるストレージ アカウントとストレージの種類の一覧を次に示します。 ストレージ アカウントのすべての種類とそれらの完全な機能のすべての一覧については、「[ストレージ アカウントの種類](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts)」を参照してください。

インポート注文については、サポートされているストレージ アカウントを次の表に示します。

| **ストレージ アカウント/サポートされるストレージの種類** | **ブロック BLOB** |**ページ BLOB** _ |_ *Azure Files** |**メモ**|
| --- | --- | -- | -- | -- |
| クラシック Standard | Y | Y | Y |
| 汎用 v1 Standard  | Y | Y | Y | ホットとクールの両方がサポートされます。|
| 汎用 v1 Premium  |  | Y| | |
| 汎用 v2 Standard  | Y | Y | Y | ホットとクールの両方がサポートされます。|
| 汎用 v2 Premium  |  |Y | | |
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
| Blob ストレージ Standard |Y | | |ホット、クール |
| ブロック BLOB ストレージ Premium |Y | | |ホット、クール |
| ページ BLOB ストレージ Premium | |Y | | |

> [!IMPORTANT]
> - 汎用アカウントの場合、Data Box では、インポート注文に対しキュー、テーブル、およびディスクのストレージの種類をサポートしていません。 エクスポート注文の場合は、Data Box では、汎用アカウントに対しキュー、テーブル、ディスク、および Azure Data Lake Gen 2 のストレージの種類をサポートしていません。
> - Data Box では、Blob Storage アカウントとブロック BLOB ストレージ アカウントの追加 BLOB がサポートされていません。
> - Data Box では、Premium File Storage アカウントがサポートされていません。
> - ページ BLOB にアップロードされるデータは、VHD など、512 バイトに揃えられている必要があります。
> - 最大 80 TB をエクスポートできます。
> - ファイル履歴と BLOB スナップショットはエクスポートされません。