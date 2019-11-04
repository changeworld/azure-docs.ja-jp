---
title: 論文エンティティの属性 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API で論文エンティティに使用できる属性について説明します。
services: cognitive-services
author: DarrinEide
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c300a6477daa5759a68d5d11d40b1a71b46bd808
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793836"
---
# <a name="paper-entity"></a>論文エンティティ

<sub> *次の属性は、論文エンティティに固有です。(Ty = '0') </sub>

名前 | 説明 | 種類 | Operations
--- | --- | --- | ---
AA.AfId | 著者の所属 ID | Int64 | 等しい
AA.AfN | 著者の所属名 | string | Equals、StartsWith
AA.AuId | 著者 ID | Int64 | 等しい
AA.AuN | 標準化された著者名 | string | Equals、StartsWith
AA.DAuN | 元の著者名 | string | なし
AA.DAfN | 元の所属名 | string | なし
AA.S | 著者一覧の位置を示す数値 | Int32 | 等しい
CC | 引用数 | Int32 | なし  
C.CId | 会議シリーズ ID | Int64 | 等しい
C.CN | 会議シリーズ名 | string | Equals、StartsWith
D | YYYY-MM-DD 形式の公開日 | Date | Equals、IsBetween
E | 拡張メタデータ (次の表を参照) | string | 該当なし  
ECC | 推定引用数 | Int32 | なし
F.DFN | 元の研究分野の名前 | string | なし
F.FId | 研究分野の ID | Int64 | 等しい
F.FN | 元の研究分野の名前 | string | Equals、StartsWith
Id | 論文 ID | Int64 | 等しい
J.JId | ジャーナル ID | Int64 | 等しい
J.JN | ジャーナル名 | string | Equals、StartsWith
Pt | パブリケーションの形態 (0: 不明、1: ジャーナル記事、2: 特許、3: カンファレンス ペーパー、4: 書籍の章、5: 書籍、6: 書籍の参照エントリ、7: データ セット、8: リポジトリ | string | 等しい
RId | 参照されている論文 ID の一覧 | Int64[] | 等しい
Ti | 正規化されたタイトル | string | Equals、StartsWith
W | タイトル内の一意の単語 | String[] | 等しい
Y | 公開年 | Int32 | Equals、IsBetween

## <a name="extended-metadata-attributes"></a>拡張メタデータの属性 ##

名前 | 説明               
--- | ---
BT | BibTex ドキュメントの種類 ('a': ジャーナル記事、'b': 書籍、'c': 書籍の章、'p': カンファレンス ペーパー)
BV | BibTex 上の会場名
CC | 引用のコンテキスト - 参照されている論文 ID の一覧とそれに対応する論文のコンテキスト (例: [{123:[“brown foxes are known for jumping as referenced in paper 123”, “the lazy dogs are a historical misnomer as shown in paper 123”]})
DN | 元の論文タイトル
DOI | デジタル オブジェクト識別子
FP | 発行中の論文の最初のページ
I | パブリケーションの号
IA | 逆要約
IA.IndexLength | インデックスの項目数 (要約のワード カウント)
IA.InvertedIndex | 要約の単語と、対応する元の要約の位置の一覧 (例: [{“the”:[0, 15, 30]}, {“brown”:[1]}, {“fox”:[2]}])
LP | 発行中の論文の最後のページ
PB | Publisher
S | ソース - 静的ランク順になった論文の Web ソースの一覧
S.Ty | ソースの種類 (1:HTML、2:Text、3:PDF、4:DOC、5:PPT、6:XLS、7:PS)
S.U | ソース URL
V | パブリケーションの巻
VFN | ジャーナルまたは会議場の完全名
VSN | ジャーナルまたは会議場の省略名
