---
author: baanders
description: Azure Digital Twins の制限のインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 21d910fb0e0992b35aa19ce65fc216734e30265c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515920"
---
### <a name="functional-limits"></a>機能制限

次の表は、現在のプレビューにおける Azure Digital Twins の機能制限を示しています。

| 領域 | 機能 | 既定の制限 | 調整可能? |
| --- | --- | --- | --- |
| Azure リソース | 1 リージョン内の Azure Digital Twins インスタンスの数 (サブスクリプションごと) | 10 | はい |
| Digital Twins | Azure Digital Twins インスタンス内のツインの数 | 200,000 | はい |
| ルーティング | 1 つの Azure Digital Twins インスタンスのエンドポイントの数 | 6 | いいえ |
| ルーティング | 1 つの Azure Digital Twins インスタンスのルート数 | 6 | はい |
| モデル | 1 つの Azure Digital Twins インスタンス内のモデルの数 | 10,000 | はい |
| モデル | 1 回の API 呼び出しでアップロードできるモデルの数 | 250 | いいえ |
| モデル | 1 つのページで返される項目の数 | 100 | いいえ |
| クエリ | 1 つのページで返される項目の数 | 100 | いいえ |
| クエリ | クエリ内の `AND` / `OR` 式の数 | 50 | はい |
| クエリ | `IN` / `NOT IN` 句内の配列項目の数 | 50 | はい |
| クエリ | クエリの文字数 | 8,000 | はい |
| クエリ | クエリ内の `JOINS` の数 | 1 | はい |

### <a name="rate-limits"></a>転送率の制限

次の表には、さまざまな API の転送率の制限が示されています。

| API | 機能 | 既定の制限 | 調整可能? |
| --- | --- | --- | --- |
| モデル API | 1 秒あたりの要求回数 | 100 | はい |
| Digital Twins API | 1 秒あたりの要求回数 | 1,000 | はい |
| クエリ API | 1 秒あたりの要求回数 | 500 | はい |
| クエリ API | 1 秒あたりのクエリ単位数 | 4,000 | はい |
| イベント ルート API | 1 秒あたりの要求回数 | 100 | はい |

### <a name="other-limits"></a>その他の制限

Azure Digital Twins モデルの DTDL ドキュメント内のデータ型とフィールドに関する制限事項については、GitHub の仕様ドキュメントを参照してください。[Digital Twins Definition Language (DTDL) - バージョン 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。
 
プレビュー期間中のクエリの作成に関するクエリの待ち時間の詳細とその他のガイドラインについては、"[*ツイン グラフのクエリを実行する方法*](../articles/digital-twins/how-to-query-graph.md)" に関する記事を参照してください。