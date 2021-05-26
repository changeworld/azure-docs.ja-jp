---
title: Azure API for FHIR を検索する
description: さまざまな検索パラメーター、修飾子、その他の FHIR 検索ツールを使用して検索する方法
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/21/2021
ms.author: cavoeg
ms.openlocfilehash: 6e3a074c24305209047fbd3e741fdb81256374e5
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110460105"
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

`:not` では、属性が true ではないリソースを検索できます。 たとえば、性別が女性ではない患者を検索することができます。

```rest
GET [your-fhir-server]/Patient?gender:not=female

```

戻り値として、性別が女性ではないすべての患者のエントリを取得します。これには、空の値 (性別なしで指定されたエントリ) が含まれます。 これは、性別が男性である患者を検索することとは異なります。これは、特定の性別を持たないエントリが含まれないためです。

### <a name="missing"></a>: 不足しています

`:missing` 値がの場合、指定された要素の値を持たないすべてのリソースを返し、 `true` 値がの場合は、指定された要素を含むすべてのリソースを返し `false` ます。 単純なデータ型の要素の場合、 `:missing=true` は、要素が拡張機能を持ち、空の値を持つすべてのリソースで一致します。 たとえば、 `Patient` 生年月日に情報が不足しているリソースをすべて検索する場合は、次のようにします。

```rest
GET [your-fhir-server]/Patient?birthdate:missing=true

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

複数の参照パラメーターを対象とする一連の検索操作を実行するには、一連の参照パラメーターを、ピリオドを使用して1つずつサーバー要求に追加することで "チェーン" でき `.` ます。 たとえば、特定の を含むリソースへの参照を使用してすべての `DiagnosticReport` `subject` `Patient` リソースを表示する場合 `name` は、  

```rest
 GET [your-fhir-server]/DiagnosticReport?subject:Patient.name=Sarah

```

この要求では、"Sarah" という名前の患者のサブジェクトを持つすべてのリソースが返されます。 フィールドが `.` パラメーターの参照 `Patient` パラメーターに対してチェーン検索を実行した後の `subject` 期間。

通常の検索 (連鎖検索ではない) のもう 1 つの一般的な使用は、特定の患者に対してすべての遭遇を見つけることです。 `Patient`多くの場合、 には 1 つ以上の `Encounter` がサブジェクトと一緒に含されます。 指定された を持 `Encounter` つ をすべての `Patient` リソースで検索するには `id` :

```rest
GET [your-fhir-server]/Encounter?subject=Patient/78a14cbe-8968-49fd-a231-d43e6619399f

```

チェーン検索を使用すると、 などの特定の情報に一致するリソース `Encounter` `Patient` を検索できます `birthdate` 。

```rest
GET [your-fhir-server]/Encounter?subject:Patient.birthdate=1987-02-20

```

これにより、1 人の患者だけでなく、指定された生年月日の値を持つすべての患者でリソース `Encounter` を検索できます。 

さらに、チェーン検索は、1 つの要求で複数の条件を検索できる シンボル を使用して、1 つの要求で複数回 `&` 実行できます。 このような場合、チェーン検索では、すべての条件を一度に満たすだけの条件を検索するのではなく、各パラメーターを "個別に" 検索します。 これは OR 操作であり、AND 操作ではありません。 たとえば、特定の名前または特定の状態の医師を持つすべての患者を取得する場合:

```rest
GET [your-fhir-server]/Patient?general-practitioner.name=Sarah&general-practitioner.address-state=WA

```

これにより、"Sarah" が として含まれるすべてのリソースと、WA 状態のアドレスを持つすべてのリソース `Patient` `generalPractitioner` `Patient` `generalPractitioner` が返されます。 言い換えると、Ny 州の Sarah と州 WA の Bill を両方とも返された結果として取得できます。 チェーン検索では、すべての条件を満たしている必要はなんら存在しないので、 パラメーターごとに個別に評価されます。

検索が、すべての条件をグループとして扱う AND 操作である必要があるシナリオについては、以下の複合検索 **例を** 参照してください。

## <a name="reverse-chain-search"></a>リバース チェーン検索

チェーン検索を使用すると、参照するリソースのプロパティに基づいてリソースを検索できます。 逆チェーン検索を使用すると、これをもう1つの方法で実行できます。 パラメーターを使用して、リソースを参照するリソースのプロパティに基づいてリソースを検索でき `_has` ます。 たとえば、 `Observation` リソースには、患者の `patient` リソースを参照する検索パラメーターがあります。 特定ので参照されているすべての患者のリソースを検索するには、次のようにし `Observation` `code` ます。

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

この要求 `2823-3` では、コード (この場合は potassium) を含むコンポーネントを指定します。 シンボルの後に、"次の値以下" に を使用し、値の範囲として を使用するコンポーネントの値の範囲 `$` `lt` `9.2` を指定します。 

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
## <a name="next-steps"></a>次のステップ

>[!div class="nextstepaction"]
>[FHIR 検索の概要](overview-of-search.md)