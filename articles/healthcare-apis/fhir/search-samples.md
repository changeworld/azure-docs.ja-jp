---
title: Azure API for FHIR を検索する
description: さまざまな検索パラメーター、修飾子、その他の FHIR 検索ツールを使用して検索する方法
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/21/2021
ms.author: cavoeg
ms.openlocfilehash: 5be1be72e47af10868867e0dce8b747911509381
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111810808"
---
# <a name="fhir-search-examples"></a>FHIR 検索の例

検索パラメーターと修飾子、チェーンとリバース チェーン検索、複合検索、検索結果の次のエントリ セットの表示、要求による検索など、FHIR 検索操作の使用例を次に示します。 `POST` 検索の詳細については [、「FHIR Search の概要」を参照してください](overview-of-search.md)。
   
## <a name="search-result-parameters"></a>検索結果のパラメーター

### <a name="_include"></a>_include

`_include` は、リソースの指定されたパラメーターを含むリソースを検索します。 たとえば、リソース間で検索して、特定の患者の治療に関する情報を含むもの (パラメーター) のみを `MedicationRequest` 検索 `reference` できます `patient` 。 次の例では、これにより、 から参照されているすべての患者と `MedicationRequests` すべての患者がプルされます `MedicationRequests` 。

```rest
 GET [your-fhir-server]/MedicationRequest?_include=MedicationRequest:patient

```

> [!NOTE]
> **_include** と **_revinclude** は 100 項目に制限されます。

### <a name="_revinclude"></a>_revinclude

`_revinclude` では、逆方向を として検索できます `_include` 。 たとえば、患者を検索し、その患者を参照しているすべての遭遇を取り消します。

```rest
GET [your-fhir-server]/Patient?_revinclude=Encounter:subject

```
### <a name="_elements"></a>_elements

`_elements` では、不要なデータを省略することで、検索結果をフィールドのサブセットに絞り込み、応答サイズを小さくします。 パラメーターは、基本要素のコンマ区切りのリストを受け取ります。

```rest
GET [your-fhir-server]/Patient?_elements=identifier,active

```

この要求では、患者のバンドルが返されますが、各リソースには識別子と患者のアクティブな状態だけが含まれます。 返されるこの応答のリソースには、 の値が含まれるので、結果のセットが `meta.tag` `SUBSETTED` 不完全である必要があります。

## <a name="search-modifiers"></a>検索修飾子

### <a name="not"></a>:not

`:not` では、属性が true ではないリソースを検索できます。 たとえば、性別が女性ではない患者を検索できます。

```rest
GET [your-fhir-server]/Patient?gender:not=female

```

戻り値として、空の値 (性別なしで指定されたエントリ) を含め、性別が女性ではないすべての患者エントリを取得します。 これは、性別が男性の患者を検索する場合とは異なります。これは、特定の性別を持つエントリが含まれるのではなからです。

### <a name="missing"></a>:missing

`:missing` は、値が の場合、指定した要素の値を持たなかったすべてのリソースを返し、値が の場合は、指定した要素を含むすべてのリソース `true` を返します `false` 。 単純なデータ型要素の場合、 は、 要素が拡張と一緒に存在するが、空の値を持つすべての `:missing=true` リソースで一致します。 たとえば、生年月日に関する情報が欠落しているすべてのリソースを検索する場合は、次 `Patient` の操作を行います。

```rest
GET [your-fhir-server]/Patient?birthdate:missing=true

```

### <a name="exact"></a>:exact
`:exact` はパラメーターに使用され、大文字と小文字の区別や文字連結など、パラメーターに正確に一致 `string` する結果を返します。

```rest
GET [your-fhir-server]/Patient?name:exact=Jon

```

この要求は、 `Patient` とまったく同じ名前のリソースを返します `Jon` 。 リソースに や などの名前を持つ Patients がある場合、検索は無視され、指定された値と完全には一致しなさらないので `Jonathan` `joN` 、リソースはスキップされます。

### <a name="contains"></a>:contains
`:contains` はパラメーターに使用され、検索するフィールド内の文字列内の任意の場所で、指定された値の部分的に一致するリソース `string` を検索します。 `contains` は大文字と小文字を区別しません。文字連結が可能です。 次に例を示します。

```rest
GET [your-fhir-server]/Patient?address:contains=Meadow

```

この要求では、文字列 "大名" を含む値を持つフィールド `Patient` `address` を持つすべてのリソースが返されます。 つまり、検索結果として "Ers" や "59 のスパークリスト ST" などの値を含むアドレスが返される可能性があります。

## <a name="chained-search"></a>チェーン検索 

複数の参照パラメーターに対応する一連の検索操作を実行するには、一連の参照パラメーターをサーバー要求にピリオド を使用して 1 つ 1 つ追加することで、一連の参照パラメーターを "チェーン" できます `.` 。 たとえば、特定の を含むリソースへの参照を使用してすべての `DiagnosticReport` `subject` `Patient` リソースを表示する場合 `name` は、  

```rest
 GET [your-fhir-server]/DiagnosticReport?subject:Patient.name=Sarah

```

この要求では `DiagnosticReport` 、"Sarah" という名前の患者のサブジェクトを持つすべてのリソースが返されます。 フィールドが `.` パラメーターの参照 `Patient` パラメーターに対してチェーン検索を実行した後の `subject` 期間。

通常の検索 (連鎖検索ではない) のもう 1 つの一般的な使用は、特定の患者に対してすべての遭遇を見つけることです。 `Patient`多くの場合、 には 1 つ以上の `Encounter` がサブジェクトと一緒に含されます。 指定された を持 `Encounter` つ をすべての `Patient` リソースで検索するには `id` :

```rest
GET [your-fhir-server]/Encounter?subject=Patient/78a14cbe-8968-49fd-a231-d43e6619399f

```

チェーン検索を使用すると、 などの特定の情報に一致するリソース `Encounter` `Patient` を検索できます `birthdate` 。

```rest
GET [your-fhir-server]/Encounter?subject:Patient.birthdate=1987-02-20

```

これにより、1 人の患者だけでなく、指定された生年月日の値を持つすべての患者でリソース `Encounter` を検索できます。 

さらに、チェーン検索は、1 つの要求で複数の条件を検索できる シンボル を使用して、1 つの要求で複数回 `&` 実行できます。 このような場合、チェーン検索では、すべての条件を一度に満たすだけの条件を検索するのではなく、各パラメーターを "個別に" 検索します。

```rest
GET [your-fhir-server]/Patient?general-practitioner:Practitioner.name=Sarah&general-practitioner:Practitioner.address-state=WA

```

これにより、"Sarah" を として持ち、WA 状態のアドレスを持つ が含まれる `Patient` `generalPractitioner` `generalPractitioner` すべてのリソースが返されます。 言い換えると、患者が NY 州の Sarah を持ち、州 WA の Bill が両方とも患者の と参照していた場合、 `generalPractitioner` が返されます。

検索が、すべての条件をグループとして扱う AND 操作である必要があるシナリオについては、以下の複合検索 **例を** 参照してください。

## <a name="reverse-chain-search"></a>リバース チェーン検索

チェーン検索を使用すると、参照するリソースのプロパティに基づいてリソースを検索できます。 逆チェーン検索を使用すると、逆の方法で行います。 パラメーターを使用して、リソースを参照するリソースのプロパティに基づいてリソースを検索 `_has` できます。 たとえば、リソース `Observation` には、Patient リソースを `patient` 参照する検索パラメーターがあります。 によって参照される、特定の で参照される Patient `Observation` リソースを検索するには `code` :

```rest
GET [base]/Patient?_has:Observation:patient:code=527

```

この要求は、コード で参照される Patient リソース `Observation` を返します `527` 。 

さらに、リバース チェーン検索には再帰構造を指定できます。 たとえば、観察に特定のユーザーからの監査イベントがあるすべての患者を検索する場合は、次の操作 `Observation` `janedoe` を実行できます。

```rest
GET [base]/Patient?_has:Observation:patient:_has:AuditEvent:entity:agent:Practitioner.name=janedoe

``` 

> [!NOTE]
> Cosmos によってAzure API for FHIRされたオープンソースの FHIR サーバーでは、チェーン検索とリバース チェーン検索は MVP 実装です。 この実装では、Cosmos DBで連鎖検索を実行するために、検索式について説明し、サブクエリを発行して、一致したリソースを解決します。 これは、式の各レベルに対して行われます。 クエリから 100 を超える結果が返された場合は、エラーがスローされます。

## <a name="composite-search"></a>複合検索

一度に複数の条件を満たすリソースを検索するには、単一のパラメーター値のシーケンスをシンボル と結合する複合検索を使用します `$` 。 返される結果は、結合された検索パラメーターで指定された条件すべてと一致するリソースの交差部分になります。 このような検索パラメーターは複合検索パラメーターと呼ばれ、入れ子構造内の複数のパラメーターを結合する新しいパラメーターを定義します。 たとえば、9.2 以下の値を持つ を含むすべてのリソースを検索 `DiagnosticReport` `Observation` する場合は、次のようになります。

```rest
GET [your-fhir-server]/DiagnosticReport?result.code-value-quantity=2823-3$lt9.2

``` 

この要求では、 のコードを含むコンポーネントを指定します。 `2823-3` この場合、この場合は大きな要素になります。 シンボルの後に、"次の値以下" に を使用し、値の範囲として を使用するコンポーネントの値の範囲 `$` `lt` `9.2` を指定します。 

## <a name="search-the-next-entry-set"></a>次のエントリ セットを検索する

1 つの検索クエリごとに返されるエントリの最大数は 1,000 です。 ただし、検索クエリに一致するエントリが 1,000 を超える場合があります。また、返された最初の 1000 エントリの後に次のエントリ セットを表示することもできます。 このような場合は、次の結果のように、 で `url` `searchset` 継続トークン値を `Bundle` 使用します。

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

また、 フィールドに指定された URL に対して GET 要求を実行します `relation: next` 。

```rest
GET [your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd

```

これにより、検索結果の次のエントリ セットが返されます。 は検索結果エントリの完全なセットであり、継続トークンは、検索クエリに返されるエントリの最大数に対する制限により、最初のセットに表示しないエントリを取得するためにサーバーによって提供されるリンクです。 `searchset` `url`

## <a name="search-using-post"></a>POST を使用して検索する

上記のすべての検索例で要求が `GET` 使用されています。 を使用して要求を使用して検索 `POST` 操作を実行できます `_search` 。

```rest
POST [your-fhir-server]/Patient/_search?_id=45

```

この要求では、値 `Patient` が `id` 45 のすべてのリソースが返されます。 GET 要求と同様に、サーバーは条件を満たす一連のリソースを決定し、HTTP 応答でバンドル リソースを返します。

クエリ パラメーターがフォーム本文として送信される POST を使用した検索のもう 1 つの例を次に示します。

```rest
POST [your-fhir-server]/Patient/_search
content-type: application/x-www-form-urlencoded

name=John

```
## <a name="next-steps"></a>次の手順

>[!div class="nextstepaction"]
>[FHIR 検索の概要](overview-of-search.md)