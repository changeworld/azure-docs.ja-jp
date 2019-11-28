---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 0fda881b805eb3a967cf3b05f6c6df8c65d20730
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905788"
---
| リソース | ターゲット        |
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

<sup>1</sup> 1 回のオブジェクト スループットはいくつかの要因に左右されます。それにはコンカレンシー、要求サイズ、パフォーマンス レベル、アップロード元の速度、ダウンロード先などがありますが、それらに限定されません。 [高スループット ブロック BLOB](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) パフォーマンス拡張を活用するには、サイズが 4 MiB 以上のプット BLOB またはプット ブロック要求を使用します (プレミアムパフォーマンス ブロック BLOB ストレージまたは Data Lake Storage Gen2 の場合は 256 KiB 以上)。
