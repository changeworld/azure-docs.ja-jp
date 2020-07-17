---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "75392437"
---
| リソース | 移行先        |
|----------|---------------|
| 単一の BLOB コンテナーの最大サイズ | ストレージ アカウントの最大容量と同じ |
| ブロック BLOB 内または追加 BLOB 内の最大ブロック数 | 50,000 ブロック |
| ブロック BLOB 内の最大ブロック サイズ | 100 MiB |
| ブロック BLOB の最大サイズ | 50,000 x 100 MiB (約 4.75 TiB) |
| 追加 BLOB のブロックの最大サイズ | 4 MiB |
| 追加 BLOB の最大サイズ | 50,000 x 4 MiB (約 195 GiB) |
| ページ BLOB の最大サイズ | 8 TiB |
| BLOB コンテナーごとの保存されるアクセス ポリシーの最大数 | 5 |
|1 つの BLOB のターゲット要求率 | 最大 500 要求/秒 |
|1 つのページ BLOB のターゲット スループット | 最大 60 MiB/秒 |
|1 つのブロック BLOB のターゲット スループット |ストレージ アカウントイングレス/エグレスの上限まで<sup>1</sup> |

<sup>1</sup> 1 つの BLOB のスループットはいくつかの要因に左右されます。それにはコンカレンシー、要求サイズ、パフォーマンス レベル、アップロード元の速度、ダウンロード先などがありますが、それらに限定されません。 [ 高スループット ブロック BLOB](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) のパフォーマンス機能強化を活用するには、もっと大きな BLOB またはブロックをアップロードします。 具体的には、[Put Blob](/rest/api/storageservices/put-blob) または [Put Block](/rest/api/storageservices/put-block) 操作を呼び出すとき、標準ストレージ アカウントの 4 MiB を超える BLOB またはブロック サイズを指定します。 プレミアム ブロック BLOB や Data Lake Storage Gen2 ストレージ アカウントの場合、256 KiB を超えるブロックまたは BLOB サイズを使用します。
