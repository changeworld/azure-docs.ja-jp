---
title: Azure API for FHIR を検索する
description: さまざまな検索パラメーター、修飾子、およびその他の FHIR 検索ツールを使用して検索する方法
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/03/2021
ms.author: cavoeg
ms.openlocfilehash: 33dcd9ace7af6d4ff820654fef20aa0a5aa3ff9d
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108756793"
---
# <a name="fhir-search-examples"></a>FHIR 検索の例

次に、FHIR 検索操作の使用例を示します。これには、検索パラメーターと修飾子、チェーンと逆チェーン検索、複合検索、検索結果に対する次のエントリセットの表示、要求を使用した検索などが含まれ `POST` ます。 検索の詳細については、「 [FHIR 検索の概要](overview-of-search.md)」を参照してください。
   
## <a name="search-result-parameters"></a>検索結果のパラメーター

### <a name="_include"></a>_include

`_include` リソースの指定されたパラメーターを含むリソースを検索します。 たとえば、リソース全体を検索して、 `MedicationRequest` 特定の患者の規定 (パラメーター) に関する情報が含まれているものだけを見つけることができ `reference` `patient` ます。

```rest
 GET [your-fhir-server]/MedicationRequest?_include=MedicationRequest:patient

```

> [!NOTE]
> **_include** と **_revinclude** は100の項目に制限されています。

### <a name="_revinclude"></a>_revinclude

`_revinclude` は、検索結果を参照しているリソース全体を検索して、の上に追加の検索 `_include` を実行 `_include` します。 たとえば、リソースを検索でき `MedicationRequest` ます。 返された各リソースについて、 `DetectedIssue` 次のような臨床問題を示すリソースを検索し `patient` ます。

```rest
GET [your-fhir-server]/MedicationRequest?_revinclude=DetectedIssue:patient

```
### <a name="_elements"></a>_elements

`_elements` 不要なデータを省略して、応答サイズを減らすために、検索結果をフィールドのサブセットに絞り込みます。 パラメーターは、基本要素のコンマ区切りリストを受け入れます。

```rest
GET [your-fhir-server]/Patient?_elements=identifier,active

```

この要求では、患者のバンドルが返されますが、各リソースに含まれるのは、id と患者のアクティブな状態のみです。 この返された応答のリソースには、 `meta.tag` `SUBSETTED` 結果の不完全なセットであることを示す値が含まれます。

## <a name="search-modifiers"></a>検索修飾子

### <a name="not"></a>: not

`:not` 属性が true ではないリソースを見つけることができます。 たとえば、性別が女性ではない患者を検索することができます。

```rest
GET [your-fhir-server]/Patient?gender:not=female

```

戻り値として、性別が女性ではないすべての患者のエントリを取得します。これには、空の値 (性別なしで指定されたエントリ) が含まれます。 これは、性別が男性である患者を検索することとは異なります。これは、特定の性別を持たないエントリが含まれないためです。

### <a name="missing"></a>: 不足しています

`:missing` 値がの場合、指定された要素の値を持たないすべてのリソースを返し、 `true` 値がの場合は、指定された要素を含むすべてのリソースを返し `false` ます。 単純なデータ型の要素の場合、 `:missing=true` は、要素が拡張機能を持ち、空の値を持つすべてのリソースで一致します。 たとえば、 `Patient` 生年月日に情報が不足しているリソースをすべて検索する場合は、次のようにします。

```rest
GET [your-fhir-server]/Patient?birthDate:missing=true

```

### <a name="exact"></a>: exact
`:exact` はパラメーターに使用され、 `string` 大文字と小文字の区別や文字連結などのパラメーターに正確に一致する結果を返します。

```rest
GET [your-fhir-server]/Patient?name:exact=Jon

```

この要求 `Patient` では、と同じ名前を持つリソースが返さ `Jon` れます。 リソースにやなどの名前の患者がある場合 `Jonathan` `joN` 、指定された値と完全に一致しないため、リソースを無視してスキップします。

### <a name="contains"></a>: 次のものが含まれます
`:contains` はパラメーターに使用され、 `string` 検索対象のフィールド内の文字列の任意の場所で、指定された値と部分的に一致するリソースを検索します。 `contains` 大文字と小文字が区別されず、文字を連結できます。 次に例を示します。

```rest
GET [your-fhir-server]/Patient?address:contains=Meadow

```

この要求は `Patient` `address` 、文字列 "Meadow" を含む値を持つフィールドを含むすべてのリソースを返します。 これは、検索結果として返される "Meadowers" や "59 Meadow ST" などの値を含むアドレスを持つことができることを意味します。

## <a name="chained-search"></a>チェーン検索 

複数の参照パラメーターを対象とする一連の検索操作を実行するには、一連の参照パラメーターを、ピリオドを使用して1つずつサーバー要求に追加することで "チェーン" でき `.` ます。 たとえば、 `DiagnosticReport` `subject` `Patient` 特定のを含むリソースへの参照を含むすべてのリソースを表示する場合は、次のように指定し `name` ます。  

```rest
 GET [your-fhir-server]/DiagnosticReport?subject:Patient.name=Sarah

```

この要求は、"Sarah" という名前の患者の件名を持つすべてのリソースを返します。 フィールドの後の期間は、 `.` `Patient` パラメーターの参照パラメーターに対して連結された検索を実行し `subject` ます。

チェーン検索のもう1つの一般的な用途は、特定の患者のすべての検出を見つけることです。 `Patient`には、多くの場合、サブジェクトを持つ1つ以上のが含まれ `Encounter` ます。 指定されたを持つのすべてのリソースを検索するには、次のように `Encounter` `Patient` 入力し `id` ます。

```rest
GET [your-fhir-server]/Encounter?subject=Patient/78a14cbe-8968-49fd-a231-d43e6619399f

```

チェーン検索を使用する `Encounter` と、次のような特定の情報に一致するすべてのリソースを見つけることができ `Patient` `birthdate` ます。

```rest
GET [your-fhir-server]/Encounter?subject:Patient.birthDate=1987-02-20

```

これにより、 `Encounter` 1 人の患者のリソースだけでなく、誕生日の値が指定されたすべての患者のリソースを検索することができます。 

また、チェーン検索は、1つの要求で複数回実行できます。シンボル `&` を使用すると、1つの要求で複数の条件を検索できます。 このような場合、連鎖検索では、すべての条件を一度に満たす条件を検索するのではなく、各パラメーターを個別に検索します。 AND 演算ではなく、または操作です。 たとえば、特定の名前または特定の州の専門家を持つすべての患者を取得する場合は、次のようにします。

```rest
GET [your-fhir-server]/Patient?general-practitioner.name=Sarah&general-practitioner.address-state=WA

```

これにより、 `Patient` として "Sarah" を持つすべてのリソース `generalPractitioner` と、州 WA を持つアドレスを持つすべてのリソースが返さ `Patient` `generalPractitioner` れます。 つまり、州 NY から Sarah を取得し、返された結果として州 WA の両方から請求を行うことができます。 チェーン検索では、すべての条件を満たしている必要はなく、パラメーターごとに個別に評価されます。

検索がすべての条件をグループとして扱う AND 操作である必要がある場合は、次の **複合検索** の例を参照してください。

## <a name="reverse-chain-search"></a>逆チェーン検索

[チェーン検索] を使用すると、参照しているリソースのプロパティに基づいてリソースを検索できます。 逆チェーン検索を使用すると、これをもう1つの方法で実行できます。 パラメーターを使用して、リソースを参照するリソースのプロパティに基づいてリソースを検索でき `_has` ます。 たとえば、 `Observation` リソースには、患者の `patient` リソースを参照する検索パラメーターがあります。 特定ので参照されているすべての患者のリソースを検索するには、次のようにし `Observation` `code` ます。

```rest
GET [base]/Patient?_has:Observation:patient:code=527

```

この要求では、コードでによって参照される患者のリソースが返され `Observation` `527` ます。 

また、逆チェーン検索では、再帰構造を使用できます。 たとえば、 `Observation` 観測に特定のユーザーの監査イベントが含まれているすべての患者を検索する場合は、 `janedoe` 次のようにします。

```rest
GET [base]/Patient?_has:Observation:patient:_has:AuditEvent:entity:user=janedoe

``` 

> [!NOTE]
> Cosmos でサポートされている Azure API for FHIR とオープンソースの FHIR サーバーでは、チェーン検索と逆連鎖検索は MVP 実装です。 Cosmos DB でチェーン検索を実行するために、この実装は検索式を処理し、サブクエリを発行して、一致したリソースを解決します。 これは、式の各レベルに対して行われます。 クエリが100を超える結果を返す場合は、エラーがスローされます。 既定では、チェーン検索は機能フラグの背後にあります。 チェーン化された検索を Cosmos DB で使用するには、ヘッダーのヘッダーを使用します。

## <a name="composite-search"></a>複合検索

一度に複数の条件を満たすリソースを検索するには、1つのパラメーター値のシーケンスをシンボルと結合する複合検索を使用し `$` ます。 返される結果は、結合された検索パラメーターによって指定されたすべての条件に一致するリソースの交差部分になります。 このような検索パラメーターは複合検索パラメーターと呼ばれ、入れ子構造の複数のパラメーターを結合する新しいパラメーターを定義します。 たとえば、が9.2 以下の potassium 値を含むすべてのリソースを検索する場合は、次のようにし `DiagnosticReport` `Observation` ます。

```rest
GET [your-fhir-server]/DiagnosticReport?result.code-value-quantity=2823-3$lt9.2

``` 

この要求 `2823-3` では、コード (この場合は potassium) を含むコンポーネントを指定します。 記号の後に `$` 、を使用してコンポーネントの値の範囲を指定します。これは、 `lt` "以下" または potassium 値の範囲に対して使用し `9.2` ます。 

## <a name="search-the-next-entry-set"></a>次のエントリセットを検索する

1つの検索クエリで返すことができるエントリの最大数は1000です。 ただし、検索クエリに一致するエントリが1000以上ある場合、返された最初の1000エントリの後にあるエントリの次のセットを確認することができます。 このような場合は、次のようにの継続トークンの `url` 値を `searchset` として使用し `Bundle` ます。

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

次のフィールドで、指定された URL に対して GET 要求を実行し `relation: next` ます。

```rest
GET [your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd

```

これにより、検索結果の次のエントリセットが返されます。 `searchset`は検索結果エントリの完全なセットであり、継続トークン `url` は、検索クエリに対して返されるエントリの最大数に制限があるため、最初のセットに表示されないエントリを取得するためにサーバーによって提供されるリンクです。

## <a name="search-using-post"></a>POST を使用した検索

前述のすべての検索例では、 `GET` 要求を使用しています。 次のものを使用して、要求を使用して検索操作を実行することもでき `POST` `_search` ます。

```rest
POST [your-fhir-server]/Patient/_search?_id=45

```

この要求 `Patient` では、値が45のすべてのリソースが返さ `id` れます。 GET 要求と同様に、サーバーは、条件を満たすリソースのセットを判断し、HTTP 応答でバンドルリソースを返します。

クエリパラメーターがフォーム本文として送信される POST を使用して検索するもう1つの例を次に示します。

```rest
POST [your-fhir-server]/Patient/_search
content-type: application/x-www-form-urlencoded

name=John

```
## <a name="next-steps"></a>次のステップ

>[!div class="nextstepaction"]
>[FHIR 検索の概要](overview-of-search.md)