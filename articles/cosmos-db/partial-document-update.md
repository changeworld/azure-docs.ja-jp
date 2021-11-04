---
title: Azure Cosmos DB の部分的ドキュメント更新
description: Azure Cosmos DB の部分的ドキュメント更新について説明します。
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: abhishgu
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7f5925ffa70977d13a2eefb25dc48898ac513f6e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434087"
---
# <a name="partial-document-update-in-azure-cosmos-db"></a>Azure Cosmos DB の部分的ドキュメント更新
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB の部分的ドキュメント更新機能 (パッチ API とも呼ばれます) には、コンテナー内のドキュメントを変更する便利な方法が用意されています。 現在のところ、ドキュメントを更新するには、クライアントを使用して読み取り、(必要に応じて) オプティミスティック同時実行制御チェックを実行し、ローカルでドキュメントを更新して、それをドキュメント全体の置換 API の呼び出しとしてネットワーク経由で送信します。 

部分的ドキュメント更新機能により、このエクスペリエンスが大幅に向上します。 全ドキュメントの置き換え操作を行うことなく、クライアントからドキュメント内の変更されたプロパティとフィールドのみを送信することができます。 この機能の主な利点は次のとおりです。

- **開発者の生産性の向上**: 使いやすい API が用意されており、ドキュメントを条件付きで更新する機能があります。 
- **パフォーマンスの向上**: クライアント側の余計な CPU サイクルを回避し、エンドツーエンドの待機時間とネットワーク帯域幅を削減します。
- **マルチリージョンの書き込み**: 同じドキュメント内の個別のパスを部分的に更新することで、自動的かつ透過的な競合解決をサポートします。
 
## <a name="supported-operations"></a>サポート対象の操作

次の表は、この機能でサポートされている操作をまとめたものです。

> [!NOTE]
> "*ターゲット パス*" は、JSON ドキュメント内の場所を指します。

| **操作の種類** | **説明** |
| ------------ | -------- |
| **追加**      | `Add` により、ターゲット パスに応じて以下のいずれかが実行されます。 <br/><ul><li>ターゲット パスに存在しない要素が指定されている場合は、それが追加されます。</li><li>ターゲット パスに存在する要素が指定されている場合、その値は置き換えられます。</li><li>ターゲット パスが有効な配列インデックスの場合、指定されたインデックスの配列に新しい要素が挿入されます。 既存の要素は右にシフトされます。</li><li>指定されたインデックスが配列の長さと等しい場合は、配列に要素が追加されます。 インデックスを指定する代わりに、`-` 文字を使用することもできます。 また、配列に要素が追加されます。</li></ul> <br/> **注**: 配列の長さよりも大きいインデックスを指定すると、エラーが発生します。|
| **Set**      | `Set` の操作は、配列データ型の場合を除き、`Add` と似ています。ターゲット パスが有効な配列インデックスの場合、そのインデックスの既存の要素は更新されます。| 
| **Replace**      | `Replace` の操作は、"_厳密な_" 置換のみのセマンティクスに従うことを除き、`Set` と似ています。 ターゲット パスに存在しない要素や配列が指定された場合は、エラーが発生します。  | 
| **Remove**     | `Remove` により、ターゲット パスに応じて以下のいずれかが実行されます。 <br/><ul><li>ターゲット パスに存在しない要素が指定されている場合は、エラーが発生します。 </li><li> ターゲット パスに存在する要素が指定されている場合、それは削除されます。 </li><li> ターゲット パスが配列インデックスの場合、それは削除され、指定されたインデックスより上の要素は 1 つ左にシフトされます。</li></ul> <br/> **注**: 配列の長さ以上のインデックスを指定すると、エラーが発生します。  |
| **Increment**     | この演算子を使うと、指定された値だけフィールドがインクリメントされます。 正の値と負の値の両方を指定できます。 フィールドが存在しない場合は、フィールドが作成され、指定された値が設定されます。 |

## <a name="supported-modes"></a>サポートされているモード

部分的ドキュメント更新機能は、次の操作モードをサポートしています。 コード例については、[概要](partial-document-update-getting-started.md)のドキュメントを参照してください。

* **単一ドキュメントのパッチ**: 1 つのドキュメントに対して、その ID とパーティション キーに基づいてパッチを適用できます。 1 つのドキュメントに対して複数のパッチ操作を実行することができます。 [上限は 10 操作です](partial-document-update-faq.yml#is-there-a-limit-to-the-number-of-partial-document-update-operations-)。

* **複数ドキュメントのパッチ**: 同じパーティション キー内の複数のドキュメントに、[トランザクションの一部](transactional-batch.md)としてパッチを適用できます。 このトランザクションは、すべての操作が記述された順序で成功した場合にのみコミットされます。 いずれかの操作が失敗した場合は、トランザクション全体がロールバックされます。

* **条件付き更新**: 前述のモードでは、SQL のようなフィルター述語 (たとえば、*from c where c.taskNum = 3*) を追加して、述語に指定された前提条件が満たされない場合に操作が失敗するようにすることもできます。 

* また、サポートされている SDK の一括 API を使用して、複数のドキュメントに対して 1 つ以上のパッチ操作を実行することもできます。


## <a name="similarities-and-differences"></a>類似点と相違点

### <a name="add-vs-set"></a>追加と設定

`Set` 操作は、`Array` を除くすべてのデータ型で `Add` と似ています。 任意の (有効な) インデックスで `Add` 操作を行うと、指定されたインデックスに要素が追加され、配列内の既存の要素はすべて右にシフトされます。 これは、指定されたインデックスの既存の要素を更新する `Set` 操作とは対照的です。 

### <a name="add-vs-replace"></a>追加と置換

`Add` 操作を使うと、(`Array` データ型を含め) プロパティがまだない場合は追加されます。 プロパティが存在しない場合、`Replace` 操作は失敗します (`Array` データ型にも適用されます)。

### <a name="set-vs-replace"></a>設定と置換

`Set` 操作を使うと、(`Array` があった場合を除き) プロパティがまだない場合は追加されます。 プロパティが存在しない場合、`Replace` 操作は失敗します (`Array` データ型にも適用されます)。

> [!NOTE]
> `Replace` は、ユーザーがプロパティの一部が常に存在することを期待し、それをアサートまたは強制できる場合に適した候補です。

## <a name="partial-document-update-specification"></a>部分的ドキュメント更新の仕様

部分的ドキュメント更新機能のクライアント向けコンポーネントは、トップレベルの REST API として実装されています。 Azure Cosmos DB によって `Conditional Add` 操作がどのようにモデル化されるかについての例を次に示します。

```bash
PATCH /dbs/{db}/colls/{coll}/documents/{doc}
HTTP/1.1
Content-Type:application/json-patch+json

{
   "condition":"from c where (c.TotalDue = 0 OR NOT IS_DEFINED(c.TotalDue))", 
   "operations":[ 
      { 
         "op":"add", 
         "path":"amount", 
         "value":80000 
      }
   ]
} 
```

> [!NOTE]
> このケースでは、`TotalDue` の値が 0 であるか、存在しない場合 (条件) に "*のみ*"、パス `amount` の値は `80000` (操作) に設定されます。

## <a name="document-level-vs-path-level-conflict-resolution"></a>ドキュメント レベルとパス レベルの競合解決  

Azure Cosmos DB アカウントが複数の書き込みリージョンで構成されている場合、[競合と競合解決ポリシー](conflict-resolution-policies.md)はドキュメント レベルで適用され、Last Write Wins (`LWW`) が既定の競合解決ポリシーになります。 部分的ドキュメント更新の場合、複数のリージョンにまたがるパッチ操作により、より詳細なパス レベルでの競合の検出と解決が行われます。

このことは、例を挙げるとわかりやすくなります。

Azure Cosmos DB に次のようなドキュメントがあるとします。

```json
{  
   "id":1, 
   "name":"John Doe", 
   "email":"jdoe@contoso.com", 
   "phone":[  
      "12345",
      "67890"
   ], 
   "level":"gold"
} 
```

次のパッチ操作は、異なるリージョンの異なるクライアントから同時に発行されています。

- 属性 `/level` を platinum に `Set`  
- 67890 を `/phone` から `Remove`

:::image type="content" source="./media/partial-document-update/patch-multi-region-conflict-resolution.png" alt-text="複数リージョンの同時の部分的更新操作での競合解決を示す画像。" border="false" lightbox="./media/partial-document-update/patch-multi-region-conflict-resolution.png":::

Patch 要求はドキュメント内の競合しないパスに対して行われたので、自動的かつ透過的に競合が解決されます (ドキュメント レベルの Last Writer Wins とは対照的です)。    

競合の解決後、クライアントには次のようなドキュメントが表示されます。

```json
{  
   "id":1, 
   "name":"John Doe", 
   "email":"jdoe@contoso.com", 
   "phone":[  
      "12345"
   ], 
   "level":"platinum",
} 
```

> [!NOTE]
> 複数のリージョンでドキュメントの同じプロパティに対して同時にパッチが適用されている場合、通常の[競合解決ポリシー](conflict-resolution-policies.md)が適用されます。

## <a name="next-steps"></a>次のステップ

- .NET、Java、Node を使用した部分的ドキュメント更新の[概要](partial-document-update-getting-started.md)について説明します。
- 部分的ドキュメント更新について[よく寄せられる質問](partial-document-update-faq.yml)
