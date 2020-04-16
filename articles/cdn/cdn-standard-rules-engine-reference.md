---
title: Azure CDN の Standard ルール エンジン リファレンス | Microsoft Docs
description: Azure Content Delivery Network (Azure CDN) の Standard ルール エンジンの一致条件とアクションに関するリファレンス ドキュメント。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 6d4fa4451c3db3d6f2a506eabd5676d18b0219f4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259903"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Azure CDN の Standard ルール エンジン リファレンス

Azure Content Delivery Network (Azure CDN) の [Standard ルール エンジン](cdn-standard-rules-engine.md)では、ルールは、1 つまたは複数の一致条件とアクションから構成されます。 この記事では、Azure CDN の Standard ルール エンジンで利用できる一致条件と機能について詳しく説明します。

ルール エンジンは、特定の種類の要求が Standard Azure CDN によって処理される方法について、最終的な決定を下すように設計されています。

**ルールの一般的な用途**:

- カスタムのキャッシュ ポリシーをオーバーライドまたは定義します。
- 要求をリダイレクトします。
- HTTP の要求ヘッダーや応答ヘッダーを変更します。

## <a name="terminology"></a>用語

ルール エンジンでルールを定義するには、[一致条件](cdn-standard-rules-engine-match-conditions.md)と[アクション](cdn-standard-rules-engine-actions.md)を設定します。

 ![Azure CDN ルールの構造](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

各ルールには、最大で 4 つの一致条件と 3 つのアクションを含めることができます。 各 Azure CDN エンドポイントには、最大 5 つのルールを含めることができます。 

Azure CDN エンドポイントの現在の 5 ルール制限には既定の*グローバル ルール*が含まれています。 グローバル ルールに一致条件がなく、グローバル ルールに定義されているアクションが常にトリガーされます。

## <a name="syntax"></a>構文

ルールにおける特殊文字の扱いは、一致条件とアクションによるテキスト値の扱いの違いによって変わります。 一致条件またはアクションでは、次のいずれかのようにテキストが解釈されます。

- [リテラル値](#literal-values)
- [ワイルドカード値](#wildcard-values)


### <a name="literal-values"></a>リテラル値

リテラル値として解釈されるテキストでは、 *% 記号を除く*すべての特殊文字が、ルールで一致する必要のある値の一部として扱われます。 たとえば、`'*'` に設定されたリテラル一致条件は、正確な値 `'*'` が見つかった場合にのみ満たされます。

パーセント記号は、URL エンコードを示すために使用されます (例: `%20`)。

### <a name="wildcard-values"></a>ワイルドカード値

ワイルドカード値として解釈されるテキストでは、特殊文字に付加的な意味が割り当てられます。 次の表は、Standard ルール エンジンにおける特殊文字の解釈についてまとめたものです。

文字 | 説明
----------|------------
\ | バックスラッシュは、このテーブルで指定されているすべて文字からのエスケープに使用されます。 エスケープする特殊文字の直前にバックスラッシュを指定する必要があります。 たとえば、次の構文では、アスタリスクをエスケープします。`\*`
% | パーセント記号は、URL エンコードを示すために使用されます (例: `%20`)。
\* | アスタリスクは、1 つまたは複数の文字を表すワイルドカードです。
空白 | 空白文字は、指定した値とパターンのいずれかで一致条件が満たされることを示します。
一重引用符 | 一重引用符には特別な意味はありません。 ただし、一重引用符値がセットになった場合、リテラル値として扱われることを示します。 一重引用符は次のように使用できます。<ul><li>指定した値が比較対象値の一部と一致するときに一致条件が満たされるようにします。  たとえば、`'ma'` は、次の文字列のいずれかと一致します。 <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template.**ma**p</li></ul><li>特殊文字をリテラル文字として指定できるようにします。 たとえば、一組の一重引用符で空白文字を囲むことで、リテラルの空白文字を指定できます (つまり、`' '` または `'<sample value>'`)。</li><li>空の値を指定できるようにします。 一重引用符のセット ( **''** ) を指定することで、空の値を指定します。</li></ul>**重要**:<br /><ul><li>指定した値にワイルドカードが含まれていない場合、自動的にリテラル値としてみなされます。 リテラル値には、単一引用符のセットを指定する必要はありません。</li><li>バックスラッシュがこの表にある別の文字のエスケープに使用されない場合、そのバックスラッシュは、一組の一重引用符で指定されても無視されます。</li><li>特殊文字をリテラル文字として指定するもう 1 つの方法は、バックスラッシュ (`\`) を使用してエスケープすることです。</li></ul>

## <a name="next-steps"></a>次のステップ

- [Standard ルール エンジンの一致条件](cdn-standard-rules-engine-match-conditions.md)
- [Standard ルール エンジンのアクション](cdn-standard-rules-engine-actions.md)
- [Standard ルール エンジンを使用して HTTPS を適用する](cdn-standard-rules-engine.md)
- [Azure CDN の概要](cdn-overview.md)
