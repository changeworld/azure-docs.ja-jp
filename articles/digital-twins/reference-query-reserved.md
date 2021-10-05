---
title: Azure Digital Twins クエリ言語リファレンス - 予約されているキーワード
titleSuffix: Azure Digital Twins
description: Azure Digital Twins クエリ言語の予約されているキーワードのリファレンス ドキュメント
author: baanders
ms.author: baanders
ms.date: 9/1/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 6ffba46cacf452a4769709b115177bab41d5eca7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837766"
---
# <a name="azure-digital-twins-query-language-reference-reserved-keywords"></a>Azure Digital Twins クエリ言語リファレンス: 予約されているキーワード

このドキュメントには、[Azure Digital Twins クエリ言語](concepts-query-language.md)で **予約されているキーワード** の一覧が含まれています。 これらの単語は、[二重の角かっこでエスケープ処理されている](#escaping-reserved-keywords-in-queries)場合を除き、クエリで識別子として使用することはできません。 

## <a name="list-of-reserved-keywords"></a>予約されているキーワードの一覧

Azure Digital Twins クエリ言語リファレンスで予約されているキーワードには次があります。

* ALL 
* AND
* AS
* ASC
* AVG
* BY
* [COUNT]
* DESC
* DEVICES_JOBS
* DEVICES_MODULES
* デバイス
* ENDS_WITH
* false
* FROM
* GROUP
* IN
* IS_BOOL
* IS_DEFINED
* IS_NULL
* IS_NUMBER
* IS_OBJECT
* IS_PRIMITIVE
* IS_STRING
* [MAX]
* [MIN]
* NOT
* NOT_IN
* NULL
* OR
* ORDER
* SELECT
* STARTS_WITH
* [SUM]
* TOP
* TRUE
* WHERE
* IS_OF_MODEL

## <a name="escaping-reserved-keywords-in-queries"></a>クエリで予約済みキーワードをエスケープ処理する

クエリで予約済みキーワードを識別子として使用するには、`[[<keyword>]]` のように、二重の角かっこで囲み、キーワードをエスケープ処理します。

たとえば、一組のデジタル ツインに `GROUP` というプロパティがありますが、この単語は予約済みのキーワードです。 このプロパティ値でフィルター処理するには、下の図のように、クエリで使用される箇所でプロパティ名をエスケープ処理する必要があります。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="ReservedKeywordExample":::