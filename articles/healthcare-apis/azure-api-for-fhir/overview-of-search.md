---
title: Azure API for FHIR での検索の概要
description: この記事では、Azure API for FHIR で実装されている FHIR 検索の概要について説明します
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/29/2021
ms.author: cavoeg
ms.openlocfilehash: b8fc847edc18e9103534961051d550340dac9e98
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2021
ms.locfileid: "133366027"
---
# <a name="overview-of-search-in-azure-api-for-fhir"></a>Azure API for FHIR での検索の概要

FHIR の仕様により、FHIR リソースの検索の基礎が定義されます。 この記事では、FHIR でのリソース検索について、いくつかの主要な側面について説明します。 FHIR リソースの検索の詳細については、HL7 FHIR 仕様の [検索](https://www.hl7.org/fhir/search.html) に関するページを参照してください。 この記事全体を通して、検索構文の例を示します。 各検索は FHIR サーバーに対して行います。通常、URL は `https://<FHIRSERVERNAME>.azurewebsites.net` です。 この例では、この URL にプレースホルダー {{FHIR_URL}} を使用します。 

FHIR 検索は、特定のリソースの種類、指定された[コンパートメント](https://www.hl7.org/fhir/compartmentdefinition.html)、すべてのリソースに対して実行できます。 FHIR で検索を実行する最も簡単な方法は、`GET` 要求を使用する方法です。 たとえば、データベース内のすべての患者をプルする場合は、次の要求を使用できます。 

```rest
GET {{FHIR_URL}}/Patient
```

`POST` を使用して検索することもできます。これは、クエリ文字列が長すぎる場合に便利です。 `POST` を使用して検索するには、検索パラメーターをフォーム本文として送信できます。 これにより、クエリ文字列では表示と理解が困難になる可能性がある、より長く複雑な一連のクエリ パラメーターが可能になります。

検索要求が成功すると、`searchset` 型の FHIR バンドル応答が返されます。 検索に失敗した場合は、`OperationOutcome` にエラーの詳細が表示され、検索が失敗した理由を理解するのに役立ちます。

次のセクションでは、検索に関連するさまざまな側面について説明します。 これらの詳細を確認したら、Azure API for FHIR で実行できる検索の例がある[サンプル ページ](search-samples.md)を参照してください。

## <a name="search-parameters"></a>検索パラメーター

検索を行う場合は、リソースのさまざまな属性に基づいて検索します。 これらの属性は検索パラメーターと呼ばれます。 各リソースには、定義された検索パラメーターのセットがあります。 検索パラメーターを正しく検索するには、データベースで検索パラメーターを定義してインデックスを作成する必要があります。

各検索パラメーターには、定義済みの[データ型](https://www.hl7.org/fhir/search.html#ptypes)があります。 さまざまなデータ型のサポートの概要を次に示します。

> [!WARNING]
> 現在、チェーン検索を使用して_sortを使用Azure API for FHIR問題が発生しています。 詳細については、「オープンソースの問題 [#2344 」を参照してください](https://github.com/microsoft/fhir-server/issues/2344)。 これは、2021 年 12 月のリリース中に解決される予定です。 

| **検索パラメーターの種類**  | **Azure API for FHIR** | **Azure Healthcare API の FHIR サービス** | **解説**|
| -------------------------  | -------------------- | ------------------------- | ------------|
|  number                    | はい                  | はい                       |
|  date                      | はい                  | はい                       |
|  string                    | はい                  | はい                       |
|  token                     | はい                  | はい                       |
|  reference                 | はい                  | はい                       |
|  複合                 | 部分的              | 部分的                   | サポートされている複合型の一覧については、この記事の後半で説明します。 |
|  数量                  | はい                  | はい                       |
|  uri                       | はい                  | はい                       |
|  special                   | いいえ                   | いいえ                        |

### <a name="common-search-parameters"></a>一般的な検索パラメーター

すべてのリソースに適用される[一般的な検索パラメーター](https://www.hl7.org/fhir/search.html#all)があります。 それらを、Azure API for FHI でのサポートとともに以下に示しています。

| **一般的な検索パラメーター** | **Azure API for FHIR** | **Azure Healthcare API の FHIR サービス** | **解説**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _id                         | はい                  | はい                       
| _lastUpdated                | はい                  | はい                       |
| _tag                        | はい                  | はい                       |
| _type                       | はい                  | はい                       |
| _security                   | はい                  | はい                       |
| _profile                    | はい                  | はい                       |
| _has                        | 部分的              | はい                       | _has のサポートは、 Azure API for FHIR の MVP と Cosmos DB でサポートされている OSS バージョンにあります。 詳細については、以下のチェーンに関するセクションを参照してください。 |
| _query                      | いいえ                   | いいえ                        |
| _filter                     | いいえ                   | いいえ                        |
| _list                       | いいえ                   | いいえ                        |
| _text                       | いいえ                   | いいえ                        |
| _content                    | いいえ                   | いいえ                        |

### <a name="resource-specific-parameters"></a>リソース固有のパラメーター

Azure API for FHIR では、FHIR 仕様で定義されているほぼすべての[リソース固有の検索パラメーター](https://www.hl7.org/fhir/searchparameter-registry.html)がサポートされています。 サポートされない唯一の検索パラメーターは、次のリンクにあります。

* [STU3 でサポートされていない検索パラメーター](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/Stu3/unsupported-search-parameters.json)

* [R4 でサポートされていない検索パラメーター](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/R4/unsupported-search-parameters.json)

次の要求を使用して、[FHIR 機能ステートメント](https://www.hl7.org/fhir/capabilitystatement.html)の検索パラメーターの現在のサポートを確認することもできます。

```rest
GET {{FHIR_URL}}/metadata
```

機能ステートメントで検索パラメーターを表示するには、`CapabilityStatement.rest.resource.searchParam` に移動して各リソースと `CapabilityStatement.rest.searchParam` の検索パラメーターを表示し、すべてのリソースの検索パラメーターを検索します。

> [!NOTE]
> Azure API for FHIR では、FHIR 仕様で定義されていない検索パラメーターを自動的に作成またはインデックス付けしません。 ただし、独自の[検索パラメーター](how-to-do-custom-search.md)を定義するサポートが提供されています。

### <a name="composite-search-parameters"></a>複合検索パラメーター
複合検索を使用すると、値ペアに対して検索できます。 たとえば、人物が身長 60 インチという測定値を検索する場合は、測定の 1 つのコンポーネントに高さのコード **および** 60 の値が含まれている必要があります。 観察結果には、値 60 と身長のコードに該当するエントリが、異なるコンポーネント セクションに含まれている可能性がありますが、体重 60 と身長 48 が格納された測定結果を取得したくはありません。 

Azure API for FHIRでは、次の検索パラメーター型のペアがサポートされています。

* 参照、トークン
* トークン、日付
* トークン、数字、数字
* トークン、数量
* トークン、文字列
* トークン、トークン

詳細については、HL7 [複合検索パラメーター](https://www.hl7.org/fhir/search.html#composite)に関するページを参照してください。 

> [!NOTE]
> 複合検索パラメーターでは、FHIR 仕様に基づく修飾子はサポートされていません。

 ### <a name="modifiers--prefixes"></a>修飾子とプレフィックス

[修飾子](https://www.hl7.org/fhir/search.html#modifiers)を使用すると、検索パラメーターを変更できます。 次に示されているのは、すべての FHIR 修飾子の概要と、Azure API for FHIR でのサポートです。 

| **修飾子** | **Azure API for FHIR** | **Azure Healthcare API の FHIR サービス** | **解説**|
| -------------------------  | -------------------- | ------------------------- | ------------|
|  :missing     | はい                  | はい                       |
|  :exact       | はい                  | はい                       |
|  :contains    | はい                  | はい                       |
|  :text        | はい                  | はい                       |
|  :type (reference) | はい             | はい                       |
|  :not         | はい                  | はい                       |
|  :below (uri) | はい                  | はい                       |
|  :above (uri) | はい                  | はい                       |
|  :in (token)  | いいえ                   | いいえ                        |
|  :below (token) | いいえ                 | いいえ                        |
|  :above (token) | いいえ                 | いいえ                        |
|  :not-in (token) | いいえ                | いいえ                        |

特定の順序 (数値、日付、数量) を持つ検索パラメーターの場合は、パラメーターの[プレフィックス](https://www.hl7.org/fhir/search.html#prefix)を使用すると、一致を見つけるのに役立ちます。 Azure API for FHIR では、すべてのプレフィックスがサポートされています。

 ### <a name="search-result-parameters"></a>検索結果のパラメーター
返されたリソースを管理するために、検索で使用できる検索結果パラメーターがあります。 各検索結果パラメーターの使い方の詳細については [HL7](https://www.hl7.org/fhir/search.html#return) の Web サイトを参照してください。 

| **検索結果のパラメーター**  | **Azure API for FHIR** | **Azure Healthcare API の FHIR サービス** | **解説**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _elements                     | はい                  | はい                       |
| _count                        | はい                  | はい                       | _count は 1,000 リソースに制限されています。 1,000 より大きい値に設定すると、1,000 件だけが返され、バンドルで警告が返されます。                               |
| _include                      | はい                  | はい                       | 含まれる項目は 100 に制限されています。 Cosmos DB 上の PaaS や OSS の _include には、:iterate のサポートが含まれません [(#2137)](https://github.com/microsoft/fhir-server/issues/2137)。                               |
| _revinclude                   | はい                  | はい                       |含まれる項目は 100 に制限されています。 Cosmos DB 上の PaaS や OSS の _revinclude には、:iterate のサポートが含まれません [(#2137)](https://github.com/microsoft/fhir-server/issues/2137)。  また、不適切な要求に対する正しくない状態コードがあります [#1319](https://github.com/microsoft/fhir-server/issues/1319)                            |
| _summary                      | はい             | はい                   |
| _total                        | 部分的              | 部分的                   | _total=none および _total=accurate                               |
| _sort                         | 部分的              | Partial                   | sort=_lastUpdatedは、Azure API for FHIR FHIR サービスでサポートされています。 2021 Azure API for FHIR 2021 年 4 月 20 日より後に作成された Cosmos および OSS DB データベースの場合、並べ替えは名、生年月日、および臨床日でサポートされます。 オープン ソースの問題 [#2344](https://github.com/microsoft/fhir-server/issues/2344)に記載されているチェーン検索_sortを使用したオープンな問題があります。         |
| _contained                    | いいえ                   | いいえ                        |
| _containedType                | いいえ                   | いいえ                        |
| _score                        | いいえ                   | いいえ                        |

> [!NOTE]
> 既定では、 `_sort` レコードは昇順に並べ替えます。 プレフィックスを使用して `'-'` 、降順で並べ替えを行います。 さらに、FHIR サービスと Azure API for FHIRでは、一度に 1 つのフィールドでのみ並べ替えを行える必要があります。

既定では、Azure API for FHIR は厳密な処理に設定されています。 これは、サーバーが不明なパラメーターまたはサポートされていないパラメーターを無視することを意味します。 厳密な処理を使用する場合は、**Prefer** ヘッダーを使用して `handling=strict` を設定できます。

 ## <a name="chained--reverse-chained-searching"></a>チェーンおよびリバース チェーン検索

[チェーン検索](https://www.hl7.org/fhir/search.html#chaining)を使用すると、別のリソースによって参照されるリソースの検索パラメーターを使用して検索できます。 たとえば、患者の名前が Jane である結果を探す場合は、次のコマンドを使用します。

`GET {{FHIR_URL}}/Encounter?subject:Patient.name=Jane`

同様に、リバース チェーン検索を実行できます。 これにより、リソースを参照する他のリソースの条件を指定して、リソースを取得できます。 チェーン検索とリバース チェーン検索のその他の例については、[FHIR 検索の例](search-samples.md)に関するページを参照してください。 

> [!NOTE]
> Azure API for FHIR と Cosmos DB によってサポートされるオープン ソースでは、チェーン検索とリバース チェーン検索に必要な各サブクエリで返される項目が 1,000 項目のみになるという制限があります。 1,000 を超える項目が見つかった場合は、次のエラー メッセージが表示されます。"チェーンされた式のサブクエリは 1000 を超える結果を返すはできないので、より選択的な条件を使用してください。" クエリを正常に実行するには、探している情報についてより具体的にする必要があります。

## <a name="pagination"></a>改ページ位置の自動修正

前述のように、検索の結果はページを使ったバンドルになります。 既定では、検索では 1 ページあたり 10 件の結果が返されますが、`_count` を指定することでこれを増やしたり減らしたりできます。 バンドル内には、検索の現在の結果を含む自己リンクがあります。 追加の一致がある場合、バンドルには次へのリンクが含まれます。 次へのリンクを引き続き使用して、結果の後続のページを取得できます。 `_count` は 1,000 項目以下に制限されます。 

現時点では、Azure API for FHIR はバンドル内の次へのリンクのみをサポートし、最初、最後、または前へのリンクはサポートされていません。

## <a name="next-steps"></a>次のステップ

検索の基本について学習したので、さまざまな検索パラメーター、修飾子、その他の FHIR 検索シナリオを使用して検索する方法の詳細について、検索サンプル ページを参照してください。

>[!div class="nextstepaction"]
>[FHIR 検索の例](search-samples.md)
