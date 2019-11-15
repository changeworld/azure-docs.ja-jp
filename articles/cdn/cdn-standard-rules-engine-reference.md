---
title: Azure CDN 標準ルール エンジンのリファレンス | Microsoft Docs
description: Azure CDN 標準ルール エンジンの一致条件とアクションに関するリファレンス ドキュメント。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 6fb7e704f3d33cff8c29386b8aba9d8289037cbb
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615834"
---
# <a name="azure-cdn-from-microsoft-rules-engine-reference"></a>Microsoft Azure CDN ルール エンジンのリファレンス

この記事では、Azure Content Delivery Network (CDN) [標準ルール エンジン](cdn-standard-rules-engine.md)で使用できる一致条件と機能について詳しく説明します。

ルール エンジンは、特定の種類の要求が CDN によって処理される方法について、最終的な決定を下すように設計されています。

**一般的な使用法**:

- カスタムのキャッシュ ポリシーをオーバーライドまたは定義します。
- 要求をリダイレクトします。
- HTTP 要求ヘッダーや応答ヘッダーの変更

## <a name="terminology"></a>用語集

ルールは、[**一致条件**](cdn-standard-rules-engine-match-conditions.md)と[**アクション**](cdn-standard-rules-engine-actions.md)を使用して定義します。 次の図では、これらの要素が強調表示されています。

 ![CDN ルールの構造](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

各ルールには、最大で 4 つの一致条件と 3 つのアクションを含めることができます。 CDN エンドポイントあたり最大 5 つのルールがあります。 また、既定で適用される**グローバル ルール**というルールがあります。 これは一致条件を含まないルールであり、そこで定義されたアクションは常にトリガーされます。 このルールは、現在の 5 つのルール制限に含まれます。

## <a name="syntax"></a>構文

特殊文字が扱われる方法は、一致条件またはアクションでテキスト値を処理する方法によって異なります。 一致条件または機能は、次のいずれかのようにテキストを解釈する場合があります。

1. [**リテラル値**](#literal-values)
2. [**ワイルドカード値**](#wildcard-values)


### <a name="literal-values"></a>リテラル値

リテラル値として解釈されるテキストでは、"%" 記号を除くすべての特殊文字が、一致する必要のある値の一部として扱われます。 つまり、`\'*'\` に設定されたリテラルの一致条件は、完全に一致する値 (つまり、`\'*'\`) が見つかった場合にのみ満たされます。

パーセント記号は、URL エンコードを示すために使用されます (例: `%20`)。

### <a name="wildcard-values"></a>ワイルドカード値

ワイルドカード値として解釈されるテキストでは、特殊文字に付加的な意味が割り当てられます。 次の表は、以下の一連の文字がどのように解釈されるのかを示しています。

Character | 説明
----------|------------
\ | バックスラッシュは、このテーブルで指定されているすべて文字からのエスケープに使用されます。 エスケープする特殊文字の直前にバックスラッシュを指定する必要があります。<br/>たとえば、次の構文では、アスタリスクをエスケープします。`\*`
% | パーセント記号は、URL エンコードを示すために使用されます (例: `%20`)。
\* | アスタリスクは、1 つまたは複数の文字を表すワイルドカードです。
スペース | 空白文字は、指定した値またはパターンのいずれかで一致条件を満たすことができることを示します。
'値' | 一重引用符には、特別な意味はありません。 ただし、一連の一重引用符することで、値はリテラル値として扱われることを示します。 次のように使用できます。<br><br/>- 指定した値が比較対象値の任意の部分と一致するたびに、一致条件を満たすようにします。  たとえば、`'ma'` は、次の文字列のいずれかと一致します。 <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- 特殊文字をリテラル文字として指定できます。 たとえば、空白文字を一重引用符で囲むことで、リテラルの空白文字を指定できます (つまり、`' '` または `'sample value'`)。<br/>- 空白の値を指定できます。 一重引用符のセット ('') を指定することで、空白の値を指定します。<br /><br/>**重要:**<br/>- 指定した値にワイルドカードが含まれていない場合は、自動的にリテラル値と見なされるので、一重引用符のセットを指定する必要はありません。<br/>- バックスラッシュがこの表の別の文字をエスケープしない場合は、一重引用符のセット内に指定すると無視されます。<br/>- 特殊文字をリテラル文字として指定するもう 1 つの方法は、バックスラッシュ (`\`) を使用してエスケープすることです。

## <a name="next-steps"></a>次の手順

- [標準ルール エンジンの一致条件](cdn-standard-rules-engine-match-conditions.md)
- [標準ルール エンジンのアクション](cdn-standard-rules-engine-actions.md)
- [標準ルール エンジンを使用して HTTPS を適用する](cdn-standard-rules-engine.md)
- [Azure CDN の概要](cdn-overview.md)
