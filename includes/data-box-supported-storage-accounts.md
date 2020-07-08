---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: f230fc247c6ad94bfdfb3cdbc0f897d66313b039
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "83696519"
---
Data Box デバイスでサポートされるストレージ アカウントとストレージの種類の一覧を次に示します。 ストレージ アカウントのすべての種類とそれらの完全な機能のすべての一覧については、「[ストレージ アカウントの種類](/azure/storage/common/storage-account-overview#types-of-storage-accounts)」を参照してください。

| **ストレージ アカウント/サポートされるストレージの種類** | **ブロック BLOB** |**ページ BLOB*** |**Azure Files** |**メモ**|
| --- | --- | -- | -- | -- |
| クラシック Standard | Y | Y | Y |
| 汎用 v1 Standard  | Y | Y | Y | ホットとクールの両方がサポートされます。|
| 汎用 v1 Premium  |  | Y| | |
| 汎用 v2 Standard  | Y | Y | Y | ホットとクールの両方がサポートされます。|
| 汎用 v2 Premium  |  |Y | | |
| Blob ストレージ Standard |Y | | |ホットとクールの両方がサポートされます。 |

\* *- ページ BLOB にアップロードされるデータは、VHD などの 512 バイトに揃えられている必要があります。*
