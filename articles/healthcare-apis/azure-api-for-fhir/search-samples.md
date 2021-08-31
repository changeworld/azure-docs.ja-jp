---
title: Azure API for FHIR を検索する
description: さまざまな検索パラメーター、修飾子、その他の FHIR 検索ツールを使用して検索する方法
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/21/2021
ms.author: cavoeg
ms.openlocfilehash: 731b85d0486c3d9fa7ba9e3af4cc2f047912fcdc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780733"
---
# <a name="fhir-search-examples-for-azure-api-for-fhir"></a>Azure API for FHIR の FHIR 検索例

以下に、FHIR 検索操作の使用例として、検索パラメーターおよび修飾子、チェーンおよび逆チェーン検索、複合検索、検索結果の次のエントリ セットの表示、`POST` 要求を使用した検索などについて説明します。 検索の詳細については、「[FHIR 検索の概要](overview-of-search.md)」を参照してください。
   
## <a name="search-result-parameters"></a>検索結果のパラメーター

### <a name="_include"></a>_include

`_include` を使用してリソース全体を検索し、リソースの指定したパラメーターが含まれるものを見つけます。 たとえば、`MedicationRequest` リソース全体を検索して、特定の患者 (`reference` パラメーター `patient`) の処方箋に関する情報が含まれるものだけを見つけることができます。 次の例では、すべての `MedicationRequests`、そして `MedicationRequests` から参照されるすべての患者を取得します。

```rest
 GET [your-fhir-server]/MedicationRequest?_include=MedicationRequest:patient

```

> [!NOTE]
> **_include** と **_revinclude** は、100 項目に制限されます。

### <a name="_revinclude"></a>_revinclude

`_revinclude` を使用すると、`_include` とは逆方向を検索できます。 たとえば、患者を検索し、次に逆方向で、その患者を参照するすべての受診を含めることができます。

```rest
GET [your-fhir-server]/Patient?_revinclude=Encounter:subject

```
### <a name="_elements"></a>_elements

`_elements` を使用して検索結果をフィールドのサブセットに絞り込み、不要なデータを省略することで応答サイズを削減します。 このパラメーターには、基本要素のコンマ区切りリストを指定できます。

```rest
GET [your-fhir-server]/Patient?_elements=identifier,active

```

この要求では、患者のバンドルが返されますが、各リソースには、識別子とその患者のアクティブな状態のみが含まれます。 この返される応答のリソースには、`SUBSETTED` の `meta.tag` 値が含まれ、不完全な結果セットであることを示します。

## <a name="search-modifiers"></a>検索修飾子

### <a name="not"></a>:not

`:not` を使用して、属性が true ではないリソースを見つけることができます。 たとえば、性別が女性ではない患者を検索することができます。

```rest
GET [your-fhir-server]/Patient?gender:not=female

```

戻り値として、空の値 (性別なしで指定されているエントリ) を含め、性別が女性ではないすべての患者エントリを取得します。 これは、性別が男性である患者を検索するのとは異なります。その場合は、特定の性別が指定されていないエントリは含まれません。

### <a name="missing"></a>:missing

`:missing` を使用すると、値が `true` の場合は、指定した要素の値を持たないすべてのリソースが返され、値が `false` の場合は、指定した要素が含まれるすべてのリソースが返されます。 単純なデータ型の要素の場合、`:missing=true` は、その要素が拡張ありで存在するが、値が空のすべてのリソースで一致します。 たとえば、生年月日の情報が不足している `Patient` リソースをすべて見つけるには、次のように指定します。

```rest
GET [your-fhir-server]/Patient?birthdate:missing=true

```

### <a name="exact"></a>:exact
`:exact` は `string` パラメーターに使用し、大文字と小文字の区別や文字連結など、パラメーターに正確に一致する結果が返されます。

```rest
GET [your-fhir-server]/Patient?name:exact=Jon

```

この要求では、`Jon` と名前が完全に同じ `Patient` リソースが返されます。 リソースに `Jonathan` や `joN` などの名前の患者があっても、指定した値と完全一致しないため、検索により、そのリソースは無視およびスキップされます。

### <a name="contains"></a>:contains
`:contains` は `string` パラメーターに使用し、指定した値が、検索対象フィールド内の文字列のいずれかの場所で部分的に一致するリソースを検索します。 `contains` は大文字と小文字が区別されず、文字の連結も許可されます。 次に例を示します。

```rest
GET [your-fhir-server]/Patient?address:contains=Meadow

```

この要求では、値に文字列 "Meadow" が含まれる `address` フィールドを持つすべての `Patient` リソースが返されます。 これは、"Meadowers" や "59 Meadow ST" などの値が含まれる住所が検索結果として返される可能性があることを意味します。

## <a name="chained-search"></a>チェーン検索 

複数の参照パラメーターを対象とする一連の検索操作を実行するには、一連の参照パラメーターをピリオド `.` を使用して 1 つずつサーバー要求に追加することで "チェーン" できます。 たとえば、特定の `name` が含まれる `Patient` リソースへの `subject` 参照を持つすべての `DiagnosticReport` リソースを表示する場合は、次のように指定します。  

```rest
 GET [your-fhir-server]/DiagnosticReport?subject:Patient.name=Sarah

```

この要求では、"Sarah" という名前の患者件名を持つ `DiagnosticReport` リソースがすべて返されます。 フィールド `Patient` の後のピリオド `.` により、`subject` パラメーターの参照パラメーターでチェーン検索が実行されます。

通常の検索 (チェーン検索ではない) のもう 1 つの一般的な用途は、特定の患者のすべての受診を見つけることです。 `Patient` には、多くの場合、件名を持つ `Encounter` が 1 つ以上含まれます。 提供される `id` を持つ `Patient` のすべての `Encounter` リソースを検索するには、次のように指定します。

```rest
GET [your-fhir-server]/Encounter?subject=Patient/78a14cbe-8968-49fd-a231-d43e6619399f

```

チェーン検索を使用すると、`birthdate` など、特定の `Patient` 情報に一致するすべての `Encounter` リソースを見つけることができます。

```rest
GET [your-fhir-server]/Encounter?subject:Patient.birthdate=1987-02-20

```

これにより、1 人の患者に対して `Encounter` リソースを検索するだけでなく、指定した生年月日の値を持つすべての患者を検索することができます。 

さらに、チェーン検索は、記号 `&` を使用することで、1 回の要求で複数回実行できるため、1 回の要求で複数の条件を検索することが可能です。 このような場合、チェーン検索では、すべての条件を一度に満たす条件を検索するのではなく、各パラメーターを "個別に" 検索します。

```rest
GET [your-fhir-server]/Patient?general-practitioner:Practitioner.name=Sarah&general-practitioner:Practitioner.address-state=WA

```

このため、"Sarah" が `generalPractitioner` であり、`generalPractitioner` の住所が WA 州のすべての `Patient` リソースが返されます。 つまり、患者の `generalPractitioner` として NY 州の Sarah と WA 州の Bill の両方が参照されている場合、何も返されません。

検索がすべての条件をグループとして扱う AND 操作である必要がある場合は、次の **複合検索** の例を参照してください。

## <a name="reverse-chain-search"></a>逆チェーン検索

チェーン検索を使用すると、参照するリソースのプロパティに基づいてリソースを検索できます。 逆チェーン検索を使用すると、その逆を行うことができます。 `_has` パラメーターを使用して、それらを参照するリソースのプロパティに基づいてリソースを検索できます。 たとえば、`Observation` リソースに、Patient リソースを参照する検索パラメーター `patient` があるとします。 特定の `code` で `Observation` により参照されるすべての Patient リソースを検索するには、次のように指定します。

```rest
GET [base]/Patient?_has:Observation:patient:code=527

```

この要求では、`Observation` により参照される Patient リソースがコード `527` で返されます。 

さらに、逆チェーン検索では、再帰構造を持つことができます。 たとえば、`Observation` の所見に特定ユーザー `janedoe` の監査イベントがあるすべての患者を検索する場合は、次のように指定します。

```rest
GET [base]/Patient?_has:Observation:patient:_has:AuditEvent:entity:agent:Practitioner.name=janedoe

``` 

> [!NOTE]
> Azure API for FHIR と、Cosmos によってサポートされるオープンソースの FHIR サーバーでは、チェーン検索と逆チェーン検索は MVP 実装です。 Cosmos DB でチェーン検索を実現するために、この実装では、検索式を処理し、サブクエリを発行して、一致したリソースを解決します。 これは、式の各レベルに対して行われます。 クエリで 100 を超える結果が返される場合は、エラーがスローされます。

## <a name="composite-search"></a>複合検索

一度に複数の条件を満たすリソースを検索するには、1 つのパラメーター値のシーケンスを記号 `$` で結合する複合検索を使用します。 返される結果は、結合した検索パラメーターで指定したすべての条件に一致するリソースの積集合になります。 このような検索パラメーターは複合検索パラメーターと呼ばれ、入れ子構造の複数のパラメーターを結合する新しいパラメーターを定義します。 たとえば、カリウム値が 9.2 以下の `Observation` が含まれるすべての `DiagnosticReport` リソースを検索する場合は、次のように指定します。

```rest
GET [your-fhir-server]/DiagnosticReport?result.code-value-quantity=2823-3$lt9.2

``` 

この要求では、`2823-3` のコードが含まれる成分を指定します。この場合はカリウムになります。 `$` 記号の後に、"以下" の `lt`、カリウムの値の範囲の `9.2` を使用して、成分の値の範囲を指定します。 

## <a name="search-the-next-entry-set"></a>次のエントリ セットを検索する

1 つの検索クエリで返すことができるエントリの最大数は 1000 です。 しかしながら、検索クエリに一致するエントリが 1000 より多い場合に、返された最初の 1000 エントリの後の次のエントリ セットを確認したいことがあります。 このような場合は、次の `Bundle` 結果の値のように、`searchset` の継続トークン `url` 値を使用します。

```json
    "resourceType": "Bundle",
    "id": "98731cb7-3a39-46f3-8a72-afe945741bd9",
    "meta": {
        "lastUpdated": "2021-04-22T09:58:16.7823171+00:00"
    },
    "type": "searchset",
    "link": [
        {
            "relation": "next",
            "url": "[your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd"
        },
        {
            "relation": "self",
            "url": "[your-fhir-server]/Patient?_sort=_lastUpdated"
        }
    ],

```

次に、フィールド`relation: next` の下で、提供された URL に対して GET 要求を実行します。

```rest
GET [your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd

```

こうすることで、検索結果の次のエントリ セットが返されます。 `searchset` は検索結果エントリの完全なセットであり、継続トークン `url` は、最初のセットには表示されないエントリを取得できるようにサーバーから提供されるリンクです。これは、検索クエリに対して返されるエントリの最大数に制限があるためです。

## <a name="search-using-post"></a>POST を使用した検索

ここまで、すべての検索例で `GET` 要求を使用しました。 `POST` 要求で `_search` を使用して検索操作を行うこともできます。

```rest
POST [your-fhir-server]/Patient/_search?_id=45

```

この要求では、`id` の値が 45 の `Patient` リソースがすべて返されます。 GET 要求と同様に、サーバーによって、条件を満たすリソースのセットが特定され、HTTP 応答でバンドル リソースが返されます。

POST を使用した検索で、クエリ パラメーターをフォーム本文として送信する別の例を次に示します。

```rest
POST [your-fhir-server]/Patient/_search
content-type: application/x-www-form-urlencoded

name=John

```
## <a name="next-steps"></a>次のステップ

>[!div class="nextstepaction"]
>[FHIR 検索の概要](overview-of-search.md)