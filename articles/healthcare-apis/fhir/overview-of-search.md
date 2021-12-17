---
title: Azure の医療 Api における FHIR 検索の概要
description: この記事では、Azure の医療 Api で実装される FHIR 検索の概要について説明します。
author: CaitlinV39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/24/2021
ms.author: cavoeg
ms.openlocfilehash: dfdde4bb53624777d4e11cbd6652f749a6f3a394
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2021
ms.locfileid: "133368846"
---
# <a name="overview-of-fhir-search"></a>FHIR 検索の概要

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

FHIR の仕様により、FHIR リソースの検索の基礎が定義されます。 この記事では、FHIR でのリソース検索について、いくつかの主要な側面について説明します。 FHIR リソースの検索の詳細については、HL7 FHIR 仕様の [検索](https://www.hl7.org/fhir/search.html) に関するページを参照してください。 この記事全体を通して、検索構文の例を示します。 各検索は FHIR サーバーに対して行います。通常、URL は `https://<WORKSPACE NAME>-<ACCOUNT-NAME>.fhir.azurehealthcareapis.com` です。 この例では、この URL にプレースホルダー {{FHIR_URL}} を使用します。 

FHIR 検索は、特定のリソースの種類、指定された[コンパートメント](https://www.hl7.org/fhir/compartmentdefinition.html)、すべてのリソースに対して実行できます。 FHIR で検索を実行する最も簡単な方法は、`GET` 要求を使用する方法です。 たとえば、データベース内のすべての患者をプルする場合は、次の要求を使用できます。 

```rest
GET {{FHIR_URL}}/Patient
```

`POST` を使用して検索することもできます。これは、クエリ文字列が長すぎる場合に便利です。 `POST` を使用して検索するには、検索パラメーターをフォーム本文として送信できます。 これにより、クエリ文字列では表示と理解が困難になる可能性がある、より長く複雑な一連のクエリ パラメーターが可能になります。

検索要求が成功すると、`searchset` 型の FHIR バンドル応答が返されます。 検索に失敗した場合は、`OperationOutcome` にエラーの詳細が表示され、検索が失敗した理由を理解するのに役立ちます。

次のセクションでは、検索に関連するさまざまな側面について説明します。 これらの詳細を確認したら、Azure の医療 Api で FHIR サービスに対して行うことができる検索の例が記載された [サンプルページ](search-samples.md) を参照してください。

## <a name="search-parameters"></a>検索パラメーター

検索を行う場合は、リソースのさまざまな属性に基づいて検索します。 これらの属性は検索パラメーターと呼ばれます。 各リソースには、定義された検索パラメーターのセットがあります。 検索パラメーターを正しく検索するには、データベースで検索パラメーターを定義してインデックスを作成する必要があります。

各検索パラメーターには、定義済みの[データ型](https://www.hl7.org/fhir/search.html#ptypes)があります。 さまざまなデータ型のサポートの概要を次に示します。


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

すべてのリソースに適用される[一般的な検索パラメーター](https://www.hl7.org/fhir/search.html#all)があります。 次に、そのサポートと共に一覧を示します。

| **一般的な検索パラメーター** | **Azure API for FHIR** | **Azure Healthcare API の FHIR サービス** | **解説**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _id                         | はい                  | はい                       
| _lastUpdated                | はい                  | はい                       |
| _tag                        | はい                  | はい                       |
| _type                       | はい                  | はい                       |
| _security                   | はい                  | はい                       |
| _profile                    | はい                  | はい                       |
| _has                        | はい。                 | はい                       |  |
| _query                      | いいえ                   | いいえ                        |
| _filter                     | いいえ                   | いいえ                        |
| _list                       | いいえ                   | いいえ                        |
| _text                       | いいえ                   | いいえ                        |
| _content                    | いいえ                   | いいえ                        |

### <a name="resource-specific-parameters"></a>リソース固有のパラメーター

Azure Healthcare API の FHIR サービスでは、FHIR[](https://www.hl7.org/fhir/searchparameter-registry.html)仕様で定義されているほぼすべてのリソース固有の検索パラメーターがサポートされています。 サポートされない唯一の検索パラメーターは、次のリンクにあります。

* [STU3 でサポートされていない検索パラメーター](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/Stu3/unsupported-search-parameters.json)

* [R4 でサポートされていない検索パラメーター](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/R4/unsupported-search-parameters.json)

次の要求を使用して、[FHIR 機能ステートメント](https://www.hl7.org/fhir/capabilitystatement.html)の検索パラメーターの現在のサポートを確認することもできます。

```rest
GET {{FHIR_URL}}/metadata
```

機能ステートメントで検索パラメーターを表示するには、`CapabilityStatement.rest.resource.searchParam` に移動して各リソースと `CapabilityStatement.rest.searchParam` の検索パラメーターを表示し、すべてのリソースの検索パラメーターを検索します。

> [!NOTE]
> Azure Healthcare API の FHIR サービスでは、FHIR 仕様で定義されていない検索パラメーターが自動的に作成またはインデックス付けされません。 ただし、独自の[検索パラメーター](how-to-do-custom-search.md)を定義するサポートが提供されています。

### <a name="composite-search-parameters"></a>複合検索パラメーター
複合検索を使用すると、値ペアに対して検索できます。 たとえば、人物が身長 60 インチという測定値を検索する場合は、測定の 1 つのコンポーネントに高さのコード **および** 60 の値が含まれている必要があります。 観察結果には、値 60 と身長のコードに該当するエントリが、異なるコンポーネント セクションに含まれている可能性がありますが、体重 60 と身長 48 が格納された測定結果を取得したくはありません。 

Azure Healthcare API の FHIR サービスでは、次の検索パラメーターの型のペアがサポートされています。

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

[修飾子](https://www.hl7.org/fhir/search.html#modifiers)を使用すると、検索パラメーターを変更できます。 すべての FHIR 修飾子とサポートの概要を次に示します。 

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

特定の順序 (数値、日付、数量) を持つ検索パラメーターの場合は、パラメーターの[プレフィックス](https://www.hl7.org/fhir/search.html#prefix)を使用すると、一致を見つけるのに役立ちます。 Azure Healthcare API の FHIR サービスでは、すべてのプレフィックスがサポートされています。

 ### <a name="search-result-parameters"></a>検索結果のパラメーター
返されたリソースを管理するために、検索で使用できる検索結果パラメーターがあります。 各検索結果パラメーターの使い方の詳細については [HL7](https://www.hl7.org/fhir/search.html#return) の Web サイトを参照してください。 

| **検索結果のパラメーター**  | **Azure API for FHIR** | **Azure Healthcare API の FHIR サービス** | **解説**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _elements                     | はい                  | はい                       |
| _count                        | はい                  | はい                       | _count は 1,000 リソースに制限されています。 1,000 より大きい値に設定すると、1,000 件だけが返され、バンドルで警告が返されます。                               |
| _include                      | はい                  | はい                       | 含まれる項目は 100 に制限されています。 Cosmos DB 上の PaaS や OSS の _include には、:iterate のサポートが含まれません [(#2137)](https://github.com/microsoft/fhir-server/issues/2137)。                               |
| _revinclude                   | はい                  | はい                       |含まれる項目は 100 に制限されています。 Cosmos DB 上の PaaS や OSS の _revinclude には、:iterate のサポートが含まれません [(#2137)](https://github.com/microsoft/fhir-server/issues/2137)。 また、不適切な要求に対する正しくない状態コードがあります [#1319](https://github.com/microsoft/fhir-server/issues/1319)                            |
| _summary                      | はい             | はい                   |
| _total                        | 部分的              | 部分的                   | _total=none および _total=accurate                               |
| _sort                         | 部分的              | Partial                   | sort = _lastUpdated は、Azure API for FHIR および FHIR サービスでサポートされています。 fhir サービスと OSS SQL DB fhir サーバーでは、文字列と dateTime フィールドによる並べ替えがサポートされています。 2021年4月20日より後に作成された Azure API for fhir および OSS Cosmos DB データベースでは、first name、last name、誕生日、および臨床日に対して sort がサポートされています。             |
| _contained                    | いいえ                   | いいえ                        |
| _containedType                | いいえ                   | いいえ                        |
| _score                        | いいえ                   | いいえ                        |

> [!NOTE]
> 既定で `_sort` は、レコードは昇順で並べ替えられます。 プレフィックスを使用して、降順に並べ替えることができ `'-'` ます。 さらに、FHIR サービスと Azure API for FHIR では、一度に1つのフィールドでのみ並べ替えを行うことができます。

既定では、Azure の医療 Api の FHIR サービスは、厳密な処理に設定されています。 これは、サーバーが不明なパラメーターまたはサポートされていないパラメーターを無視することを意味します。 厳密な処理を使用する場合は、**Prefer** ヘッダーを使用して `handling=strict` を設定できます。

 ## <a name="chained--reverse-chained-searching"></a>チェーンおよびリバース チェーン検索

[チェーン検索](https://www.hl7.org/fhir/search.html#chaining)を使用すると、別のリソースによって参照されるリソースの検索パラメーターを使用して検索できます。 たとえば、患者の名前が Jane である結果を探す場合は、次のコマンドを使用します。

`GET {{FHIR_URL}}/Encounter?subject:Patient.name=Jane`

同様に、リバース チェーン検索を実行できます。 これにより、リソースを参照する他のリソースの条件を指定して、リソースを取得できます。 チェーン検索とリバース チェーン検索のその他の例については、[FHIR 検索の例](search-samples.md)に関するページを参照してください。 


## <a name="pagination"></a>改ページ位置の自動修正

前述のように、検索の結果はページを使ったバンドルになります。 既定では、検索では 1 ページあたり 10 件の結果が返されますが、`_count` を指定することでこれを増やしたり減らしたりできます。 バンドル内には、検索の現在の結果を含む自己リンクがあります。 追加の一致がある場合、バンドルには次へのリンクが含まれます。 次へのリンクを引き続き使用して、結果の後続のページを取得できます。 `_count` は 1,000 項目以下に制限されます。 

現時点では、Azure の医療 Api の FHIR サービスは、バンドル内の次のリンクのみをサポートしており、最初、最後、または前のリンクをサポートしていません。

## <a name="next-steps"></a>次のステップ

検索の基本について学習したので、さまざまな検索パラメーター、修飾子、その他の FHIR 検索シナリオを使用して検索する方法の詳細について、検索サンプル ページを参照してください。

>[!div class="nextstepaction"]
>[FHIR 検索の例](search-samples.md)
