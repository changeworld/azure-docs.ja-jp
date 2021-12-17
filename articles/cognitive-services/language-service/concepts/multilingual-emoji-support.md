---
title: 言語向けの Azure 認知サービスでの多言語および絵文字のサポート
titleSuffix: Azure Cognitive Services
description: 言語サービス機能での多言語および絵文字エンコーディングに起因するオフセットについて説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 48bda7b1e5ab5eaa62c525997530acd9af31a39a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017386"
---
# <a name="multilingual-and-emoji-support-in-language-service-features"></a>言語サービス機能での多言語および絵文字のサポート

多言語および絵文字のサポートにより、複数の[コード ポイント](https://wikipedia.org/wiki/Code_point)を使用して書記素と呼ばれる単一の表示文字を表す Unicode エンコードに至りました。 たとえば、🌷 や 👍 のような絵文字では、複数の文字を使用して形を構成し、スキン トーンなどの視覚属性用の追加文字を使用できます。 同様に、ヒンディー語の `अनुच्छेद` は、5 つの文字と 3 つの結合記号としてエンコードされます。

言語サービス機能では、多言語と絵文字エンコーディングのさまざまな長さが原因で、応答でオフセットが返される場合があります。

## <a name="offsets-in-the-api-response"></a>API 応答のオフセット

オフセットが API 応答を返すたびに、次の点に注意してください。

* 応答内の要素は、呼び出されたエンドポイントに固有のものである可能性があります。 
* HTTP POST/GET ペイロードは [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp) でエンコードされます。これは、クライアント側コンパイラまたはオペレーティング システムの既定の文字エンコードである場合もそうでない場合もあります。
* オフセットでは、文字の数ではなく [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) 標準に基づく書記素の数が参照されます。

## <a name="extracting-substrings-from-text-with-offsets"></a>オフセットを使用したテキストからの部分文字列の抽出

文字ベースの部分文字列メソッドを .NET [substring ()](/dotnet/api/system.string.substring) メソッドなどで使用すると、オフセットによる問題が発生する可能性があります。 問題の 1 つとして、オフセットが原因で、部分文字列メソッドが複数文字の書記素エンコードの末尾ではなく途中で終了することがあります。

.NET では、[StringInfo](/dotnet/api/system.globalization.stringinfo) クラスを使用することを検討してください。これにより、個々の文字オブジェクトではなく、一連のテキスト要素として文字列を操作できます。 任意のソフトウェア環境で、書記素スプリッター ライブラリを検索することもできます。 

言語サービス機能では、便宜上、これらのテキスト要素も返されます。

オフセットを返すエンドポイントは、`stringIndexType`パラメーターをサポートします。 このパラメーターは、要求された文字列反復スキームに一致するように、API 出力内の `offset` と `length` 属性を調整します。 現時点では以下の 3 種類がサポートされています。

- `textElement_v8` (既定値): [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) 標準の定義に従って書記素を繰り返します
- `unicodeCodePoint`: Python 3 の既定のスキームである[Unicode コード ポイント](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564)を繰り返します
- `utf16CodeUnit`: Javascript、Java、.NET の既定のスキームである [UTF-16 コード単位](https://unicode.org/faq/utf_bom.html#UTF16)を繰り返します

要求された `stringIndexType` が、選択したプログラミング環境に一致する場合は、標準の substring または slice メソッドを使用して部分文字列を抽出できます。 

## <a name="see-also"></a>関連項目

* [言語サービスの概要](../overview.md)
