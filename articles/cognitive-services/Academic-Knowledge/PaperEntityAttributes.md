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
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: fc3bb5987c31fe718951a3cae02ed7c4ddc29957
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123080"
---
# <a name="paper-entity"></a>論文エンティティ

> [!NOTE]
> 以下の属性は論文エンティティに固有です。 (Ty = '0')

名前 | 説明 | 種類 | Operations
--- | --- | --- | ---
AA.AfId | 著者の所属 ID | Int64 | 等しい
AA.AfN | 著者の所属名 | string | Equals、StartsWith
AA.AuId | 著者 ID | Int64 | 等しい
AA.AuN | 標準化された著者名 | string | Equals、StartsWith
AA.DAuN | 元の著者名 | string | なし
AA.DAfN | 元の所属名 | string | なし
AA.S | 著者一覧の位置を示す数値 | Int32 | 等しい
BT | BibTex ドキュメントの種類 ('a': ジャーナル記事、'b': 書籍、'c': 書籍の章、'p': カンファレンス ペーパー) | string | なし
BV | BibTex 上の会場名 | string | なし
C.CId | 会議シリーズ ID | Int64 | 等しい
C.CN | 会議シリーズ名 | string | Equals、StartsWith
CC | 引用数 | Int32 | なし  
CitCon | 引用コンテキスト</br></br>参照されている論文 ID の一覧とそれに対応する論文のコンテキスト (例: [{123:["brown foxes are known for jumping as referenced in paper 123", "the lazy dogs are a historical misnomer as shown in paper 123"]}) | カスタム | なし
D | YYYY-MM-DD 形式の公開日 | Date | Equals、IsBetween
DN | 元の論文タイトル | string | なし
DOI | デジタル オブジェクト識別子 | string | Equals、StartsWith
E | 拡張メタデータ</br></br>**重要**:この属性は非推奨となっており、レガシ アプリケーション用としてのみサポートされています。 この属性を個別に要求すると (つまり、属性 = Id、Ti、E)、すべての拡張メタデータ属性が、"*シリアル化された JSON 文字列*" で返されます。</br></br>拡張メタデータに含まれるすべての属性が最上位レベルの属性として使用できるようになり、そのように要求できるようになりました (つまり、属性 = Id、Ti、DOI、IA)。 | [拡張](#extended) | なし
ECC | 推定引用数 | Int32 | なし
F.DFN | 元の研究分野の名前 | string | なし
F.FId | 研究分野の ID | Int64 | 等しい
F.FN | 元の研究分野の名前 | string | Equals、StartsWith
FP | 発行中の論文の最初のページ | string | 等しい
I | パブリケーションの号 | string | 等しい
IA | 逆要約 | [InvertedAbstract](#invertedabstract) | なし
Id | 論文 ID | Int64 | 等しい
J.JId | ジャーナル ID | Int64 | 等しい
J.JN | ジャーナル名 | string | Equals、StartsWith
LP | 発行中の論文の最後のページ | string | 等しい
PB | Publisher | string | なし
Pt | パブリケーションの形態 (0: 不明、1: ジャーナル記事、2: 特許、3: カンファレンス ペーパー、4: 書籍の章、5: 書籍、6: 書籍の参照エントリ、7: データ セット、8: リポジトリ | string | 等しい
RId | 参照されている論文 ID の一覧 | Int64[] | 等しい
S | 関連性で並べ替えられた、論文のソース URL の一覧 | [Source](#source)[] | なし
Ti | 正規化されたタイトル | string | Equals、StartsWith
V | パブリケーションの巻 | string | 等しい
VFN | ジャーナルまたは会議場の完全名 | string | なし
VSN | ジャーナルまたは会議場の省略名 | string | なし
W | タイトル内の一意の単語 | String[] | 等しい
Y | 公開年 | Int32 | Equals、IsBetween

## <a name="extended"></a>拡張
> [!IMPORTANT]
> この属性は非推奨となっており、レガシ アプリケーション用としてのみサポートされています。 この属性を個別に要求すると (つまり、属性 = Id、Ti、E)、すべての拡張メタデータ属性が、"*シリアル化された JSON 文字列*" で返されます。</br></br>拡張メタデータに含まれるすべての属性が最上位レベルの属性として使用できるようになり、そのように要求できるようになりました (つまり、属性 = Id、Ti、DOI、IA)。

> [!IMPORTANT]
> 個々の拡張属性を、"E." スコープを使用して要求すること、つまり "E.DN" のサポートは、非推奨となる予定です。 これは技術的にはまだサポートされていますが、個々の拡張属性を、"E." スコープを使用して要求すると、属性値が JSON 応答の 2 つの場所で ("E" オブジェクトの一部として、および最上位レベルの属性として) 返されます。

名前 | 説明 | 種類 | Operations
--- | --- | --- | ---
BT | BibTex ドキュメントの種類 ('a': ジャーナル記事、'b': 書籍、'c': 書籍の章、'p': カンファレンス ペーパー) | string | なし
BV | BibTex 上の会場名 | string | なし
CC | 引用コンテキスト</br></br>参照されている論文 ID の一覧とそれに対応する論文のコンテキスト (例: [{123:["brown foxes are known for jumping as referenced in paper 123", "the lazy dogs are a historical misnomer as shown in paper 123"]}) | カスタム | なし
DN | 元の論文タイトル | string | なし
DOI | デジタル オブジェクト識別子 | string | なし
FP | 発行中の論文の最初のページ | string | なし
I | パブリケーションの号 | string | なし
IA | 逆要約 | [InvertedAbstract](#invertedabstract) | なし
LP | 発行中の論文の最後のページ | string | なし
PB | Publisher | string | なし
S | 関連性で並べ替えられた、論文のソース URL の一覧 | [Source](#source)[] | なし
V | パブリケーションの巻 | string | なし
VFN | ジャーナルまたは会議場の完全名 | string | なし
VSN | ジャーナルまたは会議場の省略名 | string | なし

## <a name="invertedabstract"></a>InvertedAbstract

> [!IMPORTANT]
> InvertedAbstract 属性を戻り属性として直接要求することはできません。 個々の属性が必要な場合は、最上位レベルの "IA" 属性を要求する必要があります。つまり、IA.IndexLength を取得するには、属性 = IA を要求します。

名前 | 説明 | 種類 | Operations
--- | --- | --- | ---
IndexLength | インデックスの項目数 (要約のワード カウント) | Int32 | なし
InvertedIndex | 要約の単語と、対応する元の要約の位置の一覧 (例: [{"the":[0, 15, 30]}, {"brown":[1]}, {"fox":[2]}]) | カスタム | なし

## <a name="source"></a>source

> [!IMPORTANT]
> ソース属性を戻り属性として直接要求することはできません。 個々の属性が必要な場合は、最上位レベルの "S" 属性を要求する必要があります。つまり、S.U を取得するには、属性 = S を要求します。

名前 | 説明 | 種類 | Operations
--- | --- | --- | ---
Ty | ソース URL の種類 (1:HTML、2:Text、3:PDF、4:DOC、5:PPT、6:XLS、7:PS) | string | なし
U | ソース URL | string | なし
