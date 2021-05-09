---
title: FHIR の Azure API での検索の概要
description: この記事では、Azure API for FHIR で実装されている FHIR 検索の概要について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/3/2021
ms.author: cavoeg
ms.openlocfilehash: 37e75fb4f1e63e52bf81458641e56da9ae677b91
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108770895"
---
# <a name="overview-of-fhir-search"></a>FHIR 検索の概要

FHIR 仕様では、FHIR リソースの検索の基礎が定義されています。 この記事では、FHIR でリソースを検索するための主要な側面について説明します。 FHIR リソースの検索の詳細については、HL7 FHIR [仕様の「」を](https://www.hl7.org/fhir/search.html) 参照してください。 この記事では、検索構文の例を紹介します。 各検索は FHIR サーバーに対して行われ、通常はの URL があり `https://<FHIRSERVERNAME>.azurewebsites.net` ます。 この例では、この URL にプレースホルダー {{FHIR_URL}} を使用します。 

FHIR 検索は、特定のリソースの種類、指定された [コンパートメント](https://www.hl7.org/fhir/compartmentdefinition.html)、またはすべてのリソースに対して行うことができます。 FHIR で検索を実行する最も簡単な方法は、要求を使用することです `GET` 。 たとえば、データベース内のすべての患者をプルする場合は、次の要求を使用できます。 

```rest
GET {{FHIR_URL}}/Patient
```

また、を使用して検索することもでき `POST` ます。これは、クエリ文字列が長すぎる場合に便利です。 を使用して検索するには `POST` 、検索パラメーターをフォーム本文として送信します。 これにより、クエリ文字列の表示と理解が困難になる可能性のある、より複雑な一連のクエリパラメーターを使用できるようになります。

検索要求が成功した場合は、型の FHIR バンドル応答が返され `searchset` ます。 検索に失敗した場合は、にエラーの詳細が表示され、 `OperationOutcome` 検索が失敗した理由を理解するのに役立ちます。

以下のセクションでは、検索に関連するさまざまな側面について説明します。 これらの詳細を確認したら、Azure API for FHIR で行うことができる検索の例が記載された [サンプルページ](search-samples.md) を参照してください。

## <a name="search-parameters"></a>検索パラメーター

検索を実行すると、リソースのさまざまな属性に基づいて検索が実行されます。 これらの属性は、検索パラメーターと呼ばれます。 各リソースには、定義済みの検索パラメーターのセットがあります。 検索を正常に検索するには、検索パラメーターを定義し、データベース内でインデックスを作成する必要があります。

各検索パラメーターには、定義された [データ型](https://www.hl7.org/fhir/search.html#ptypes)があります。 さまざまなデータ型のサポートについて以下に説明します。


| **検索パラメーターの種類**  | **サポート対象 - PaaS** | **サポート対象 - OSS (SQL)** | **サポート対象 - OSS (Cosmos DB)** | **コメント**|
| -------------------------  | -------------------- | ------------------------- | ------------------------------- |------------|
|  number                    | はい                  | はい                       | はい                             |
|  date                      | はい                  | はい                       | はい                             |
|  string                    | はい                  | はい                       | はい                             |
|  token                     | はい                  | はい                       | はい                             |
|  reference                 | はい                  | はい                       | はい                             |
|  複合                 | 部分的              | 部分的                   | 部分的                         | サポートされている複合型の一覧については、この記事の後半で説明します。 |
|  数量                  | はい                  | はい                       | はい                             |
|  uri                       | はい                  | はい                       | はい                             |
|  special                   | いいえ                   | いいえ                        | いいえ                              |

### <a name="common-search-parameters"></a>一般的な検索パラメーター

すべてのリソースに適用される [一般的な検索パラメーター](https://www.hl7.org/fhir/search.html#all) があります。 次に、Azure API for FHIR でのサポートと共に以下の一覧を示します。

| **一般的な検索パラメーター** | **サポート対象 - PaaS** | **サポート対象 - OSS (SQL)** | **サポート対象 - OSS (Cosmos DB)** | **コメント** |
| --------------------------  | -------------------- | ------------------------- | ------------------------------- | ------------|
| _id                         | はい                  | はい                       | はい                             |             |
| _lastUpdated                | はい                  | はい                       | はい                             |             |
| _tag                        | はい                  | はい                       | はい                             |             | 
| _type                       | はい                  | はい                       | はい                             |             |
| _security                   | はい                  | はい                       | はい                             |             |
| _profile                    | はい                  | はい                       | はい                             | 2021年2月20日より前に R4 データベースを作成した場合は、再 [インデックスジョブ](how-to-run-a-reindex.md) を実行して **_profile** を有効にする必要があります。|
| _has                        | 部分的              | はい                       | 部分的                         | _Has のサポートは、Azure API for FHIR と、Cosmos DB によってサポートされる OSS バージョンの MVP に含まれています。 詳細については、以下の「チェーン」セクションを参照してください。 |
| _query                      | いいえ                   | いいえ                        | いいえ                              |             |
| _filter                     | いいえ                   | いいえ                        | いいえ                              |             |
| _list                       | いいえ                   | いいえ                        | いいえ                              |             |
| _text                       | いいえ                   | いいえ                        | いいえ                              |             |
| _content                    | いいえ                   | いいえ                        | いいえ                              |             |

### <a name="resource-specific-parameters"></a>リソース固有のパラメーター

FHIR 用 Azure API を使用すると、FHIR 仕様で定義されているほとんどすべての [リソース固有の検索パラメーター](https://www.hl7.org/fhir/searchparameter-registry.html) がサポートされます。 以下のリンクでは、サポートされていない検索パラメーターのみを使用できます。

* [STU3 サポートされていない検索パラメーター](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/Stu3/unsupported-search-parameters.json)

* [R4 サポートされていない検索パラメーター](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/R4/unsupported-search-parameters.json)

次の要求を使用して、 [Fhir 機能ステートメント](https://www.hl7.org/fhir/capabilitystatement.html) での検索パラメーターの現在のサポートを確認することもできます。

```rest
GET {{FHIR_URL}}/metadata
```

機能ステートメントの検索パラメーターを表示するには、に移動して `CapabilityStatement.rest.resource.searchParam` 各リソースの検索パラメーターを表示し、 `CapabilityStatement.rest.searchParam` すべてのリソースの検索パラメーターを検索します。

> [!NOTE]
> Azure API for FHIR では、FHIR 仕様で定義されていない検索パラメーターは自動的に作成またはインデックス化されません。 ただし、独自の [検索パラメーター](how-to-do-custom-search.md)を定義するためのサポートを提供しています。

### <a name="composite-search-parameters"></a>複合検索パラメーター
複合検索では、値のペアを検索できます。 たとえば、ユーザーが60インチであった高さの観測を検索している場合は、観測の1つのコンポーネントに高さのコード **と** 60 の値が含まれていることを確認する必要があります。 観測には、60の値と高さ48が格納されていましたが、別のコンポーネントのセクションにおいては、60の値と高さのコードが含まれています。 

Azure API for FHIR では、次の検索パラメーターの型の組み合わせがサポートされています。

* 参照、トークン
* トークン、日付
* Token、Number、Number
* トークン、数量
* Token、String
* トークン、トークン

詳細については、「HL7 [複合検索パラメーター](https://www.hl7.org/fhir/search.html#composite)」を参照してください。 

> [!NOTE]
> 複合検索パラメーターでは、FHIR 仕様に従って修飾子をサポートしていません。

 ### <a name="modifiers--prefixes"></a>修飾子 & プレフィックス

[修飾子](https://www.hl7.org/fhir/search.html#modifiers) を使用すると、検索パラメーターを変更できます。 次に、すべての FHIR 修飾子の概要と、FHIR 用の Azure API でのサポートを示します。 

| **修飾子** | **サポート対象 - PaaS** | **サポート対象 - OSS (SQL)** | **サポート対象 - OSS (Cosmos DB)** |
| ------------- | -------------------- | ------------------------- | ------------------------------- |
|  : 不足しています     | はい                  | はい                       | はい                             |
|  : exact       | はい                  | はい                       | はい                             | 
|  : 次のものが含まれます    | はい                  | はい                       | はい                             | 
|  : テキスト        | はい                  | はい                       | はい                             | 
|  : 型 (リファレンス) | はい             | はい                       | はい                             | 
|  : not         | はい                  | はい                       | はい                             | 
|  : 以下 (uri) | はい                  | はい                       | はい                             |  
|  : 上 (uri) | いいえ                   | いいえ                        | いいえ                              | 
|  : in (token)  | いいえ                   | いいえ                        | いいえ                              | 
|  : 以下 (トークン) | いいえ                 | いいえ                        | いいえ                              | 
|  : 上 (トークン) | いいえ                 | いいえ                        | いいえ                              | 
|  : not in (token) | いいえ                | いいえ                        | いいえ                              | 

特定の順序 (数値、日付、および数量) を持つ検索パラメーターについては、パラメーターの [プレフィックス](https://www.hl7.org/fhir/search.html#prefix) を使用して一致項目を検索できます。 Azure API for FHIR では、すべてのプレフィックスがサポートされています。

 ### <a name="search-result-parameters"></a>検索結果のパラメーター
返されたリソースを管理するために、検索で使用できる検索結果パラメーターがあります。 各検索結果パラメーターの使用方法の詳細については、 [HL7](https://www.hl7.org/fhir/search.html#return) の web サイトを参照してください。 

| **検索結果のパラメーター**  | **サポート対象 - PaaS** | **サポート対象 - OSS (SQL)** | **サポート対象 - OSS (Cosmos DB)** | **コメント**                 |
| ----------------------------  | -------------------- | ------------------------- | ------------------------------- | -----------------------------|
| _elements                     | はい                  | はい                       | はい                             |  問題 [1256](https://github.com/microsoft/fhir-server/issues/1256)                              |
| _count                        | はい                  | はい                       | はい                             | _count は、1000のリソースに制限されています。 1000よりも大きい値を設定した場合は、1000だけが返され、バンドルで警告が返されます。                               |
| _include                      | はい                  | はい                       | はい                             | 含まれる項目は 100 に制限されています。 Cosmos DB での PaaS および OSS の _include には、反復処理のサポート [(#1313)](https://github.com/microsoft/fhir-server/issues/1313)は含まれません。                               |
| _revinclude                   | はい                  | はい                       | はい                             |  含まれる項目は 100 に制限されています。 Cosmos DB での PaaS および OSS の _revinclude には、反復処理のサポート [(#1313)](https://github.com/microsoft/fhir-server/issues/1313)は含まれません。  イシュー [#1319](https://github.com/microsoft/fhir-server/issues/1319)                            |
| _summary                      | はい             | はい                   | はい                        |                               |
| _total                        | 部分的              | 部分的                   | 部分的                         | _total = none および _total = 正確                               |
| _sort                         | 部分的              | 部分的                   | 部分的                         | sort = _lastUpdated がサポートされています。 2021年4月20日より後に作成された Azure API for FHIR および OSS Cosmos DB データベースでは、first name、last name、および臨床日でも並べ替えがサポートされています。                               |
| _contained                    | いいえ                   | いいえ                        | いいえ                              |                                |
| _containedType                | いいえ                   | いいえ                        | いいえ                              |                                |
| _score                        | いいえ                   | いいえ                        | いいえ                              |                                |

既定では、Azure API for FHIR は、厳密な処理に設定されています。 これは、サーバーが不明またはサポートされていないパラメーターを無視することを意味します。 厳格な処理を使用する場合 **は、use header と** set を使用でき `handling=strict` ます。

 ## <a name="chained--reverse-chained-searching"></a>チェーン & 逆チェーン検索

[チェーン検索](https://www.hl7.org/fhir/search.html#chaining)では、別のリソースによって参照されているリソースに対して検索パラメーターを使用して検索できます。 たとえば、患者の名前が Jane である場合は、次のように入力します。

`GET {{FHIR_URL}}/Encounter?subject:Patient.name=Jane`

同様に、逆の連鎖検索を行うこともできます。 これにより、リソースを参照する他のリソースに関する条件を指定するリソースを取得できます。 チェーンと逆のチェーン検索のその他の例については、 [Fhir の検索例](search-samples.md) に関するページを参照してください。 

> [!NOTE]
> Azure API for FHIR と Cosmos DB によってサポートされるオープンソースでは、チェーン検索と逆連鎖検索に必要な各サブクエリが100項目のみを返すという制限があります。 100個を超える項目が見つかった場合は、次のエラーメッセージが表示されます。 "チェーン式のサブクエリが100を超える結果を返すことができません。より選択的な条件を使用してください。" クエリを成功させるには、探している内容についてより具体的な情報を取得する必要があります。

## <a name="pagination"></a>改ページ位置の自動修正

前述のように、検索結果はページングバンドルになります。 既定では、検索はページごとに10件の結果を返しますが、を指定することにより、この値を増減でき `_count` ます。 バンドル内には、検索の現在の結果を含む自己リンクがあります。 その他の一致がある場合、バンドルには次のリンクが含まれます。 引き続き次のリンクを使用して、結果の後続のページを取得できます。 `_count` は、1000項目以下に制限されています。 

現時点では、Azure API for FHIR では、バンドルの次のリンクのみがサポートされており、最初、最後、または前のリンクはサポートされていません。

## <a name="next-steps"></a>次のステップ

検索の基本について学習したので、さまざまな検索パラメーター、修飾子、およびその他の FHIR 検索シナリオを使用して検索する方法の詳細については、「検索のサンプル」ページを参照してください。

>[!div class="nextstepaction"]
>[FHIR 検索の例](search-samples.md)
