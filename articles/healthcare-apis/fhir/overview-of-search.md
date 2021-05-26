---
title: FHIR の Azure API での検索の概要
description: この記事では、Azure API for FHIR で実装されている FHIR 検索の概要について説明します。
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/17/2021
ms.author: cavoeg
ms.openlocfilehash: 2d1bccbd40689a06a13b7a2283ad02a0ac43ce84
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110477829"
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

検索を実行すると、リソースのさまざまな属性に基づいて検索が実行されます。 これらの属性は検索パラメーターと呼ばれる。 各リソースには、定義された検索パラメーターのセットがあります。 検索パラメーターを正しく検索するには、データベースで検索パラメーターを定義してインデックスを作成する必要があります。

各検索パラメーターには、定義済みのデータ [型 があります](https://www.hl7.org/fhir/search.html#ptypes)。 さまざまなデータ型のサポートの概要を次に示します。


| **検索パラメーターの種類**  | **サポート対象 - PaaS** | **サポート対象 - OSS (SQL)** | **サポート対象 - OSS (Cosmos DB)** | **コメント**|
| -------------------------  | -------------------- | ------------------------- | ------------------------------- |------------|
|  number                    | はい                  | Yes                       | はい                             |
|  date                      | はい                  | Yes                       | はい                             |
|  string                    | はい                  | Yes                       | Yes                             |
|  token                     | Yes                  | Yes                       | Yes                             |
|  reference                 | Yes                  | Yes                       | Yes                             |
|  複合                 | Partial              | Partial                   | Partial                         | サポートされている複合型の一覧については、この記事の後半で説明します。 |
|  数量                  | Yes                  | Yes                       | Yes                             |
|  uri                       | Yes                  | Yes                       | Yes                             |
|  special                   | いいえ                   | いいえ                        | いいえ                              |

### <a name="common-search-parameters"></a>一般的な検索パラメーター

すべてのリソース [に適用される一](https://www.hl7.org/fhir/search.html#all) 般的な検索パラメーターがあります。 以下に示すのは、以下の一覧と、次のコード内でのサポートAzure API for FHIR。

| **一般的な検索パラメーター** | **サポート対象 - PaaS** | **サポート対象 - OSS (SQL)** | **サポート対象 - OSS (Cosmos DB)** | **コメント** |
| --------------------------  | -------------------- | ------------------------- | ------------------------------- | ------------|
| _id                         | Yes                  | Yes                       | Yes                             |             |
| _lastUpdated                | Yes                  | Yes                       | Yes                             |             |
| _tag                        | Yes                  | Yes                       | Yes                             |             | 
| _type                       | Yes                  | Yes                       | Yes                             |             |
| _security                   | Yes                  | Yes                       | Yes                             |             |
| _profile                    | Yes                  | Yes                       | Yes                             | 2021 年 2 月 20 日より前に R4 データベースを [](how-to-run-a-reindex.md)作成した場合は、インデックスの再作成ジョブを実行して、 を有効にする **_profile。**|
| _has                        | Partial              | はい                       | 部分的                         | サポートされている_hasは、MVP の Azure API for FHIR と、サポートされている OSS バージョンCosmos DB。 詳細については、以下のチェーンに関するセクションを参照してください。 |
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
* トークン、文字列
* トークン、トークン

詳細については、「HL7 複合検索パラメーター」 [を参照してください](https://www.hl7.org/fhir/search.html#composite)。 

> [!NOTE]
> 複合検索パラメーターでは、FHIR 仕様に基く修飾子はサポートされていません。

 ### <a name="modifiers--prefixes"></a>プレフィックス&修飾子

[修飾子を](https://www.hl7.org/fhir/search.html#modifiers) 使用すると、検索パラメーターを変更できます。 次に示すのは、すべての FHIR 修飾子の概要と、Azure API for FHIR。 

| **修飾子** | **サポート対象 - PaaS** | **サポート対象 - OSS (SQL)** | **サポート対象 - OSS (Cosmos DB)** |
| ------------- | -------------------- | ------------------------- | ------------------------------- |
|  :missing     | Yes                  | Yes                       | Yes                             |
|  :exact       | Yes                  | Yes                       | Yes                             | 
|  :contains    | Yes                  | Yes                       | Yes                             | 
|  :text        | Yes                  | Yes                       | Yes                             | 
|  :type (リファレンス) | Yes             | Yes                       | Yes                             | 
|  :not         | Yes                  | Yes                       | Yes                             | 
|  :below (uri) | Yes                  | Yes                       | Yes                             |  
|  :above (uri) | Yes                  | Yes                       | Yes                             | 
|  :in (トークン)  | いいえ                   | いいえ                        | いいえ                              | 
|  :below (トークン) | いいえ                 | いいえ                        | いいえ                              | 
|  :above (トークン) | いいえ                 | いいえ                        | いいえ                              | 
|  :not-in (トークン) | いいえ                | いいえ                        | いいえ                              | 

特定の順序 (数値、日付、数量) を持つ検索パラメーターの場合は、[](https://www.hl7.org/fhir/search.html#prefix)パラメーターのプレフィックスを使用して、一致を見つけるのに役立ちます。 Azure API for FHIR では、すべてのプレフィックスがサポートされています。

 ### <a name="search-result-parameters"></a>検索結果のパラメーター
返されたリソースを管理するために、検索で使用できる検索結果パラメーターがあります。 各検索結果パラメーターの使用方法の詳細については、 [HL7](https://www.hl7.org/fhir/search.html#return) の web サイトを参照してください。 

| **検索結果のパラメーター**  | **サポート対象 - PaaS** | **サポート対象 - OSS (SQL)** | **サポート対象 - OSS (Cosmos DB)** | **コメント**                 |
| ----------------------------  | -------------------- | ------------------------- | ------------------------------- | -----------------------------|
| _elements                     | Yes                  | Yes                       | Yes                             |                                |
| _count                        | Yes                  | Yes                       | Yes                             | _count は、1000のリソースに制限されています。 1000よりも大きい値を設定した場合は、1000だけが返され、バンドルで警告が返されます。                               |
| _include                      | Yes                  | Yes                       | Yes                             | 含まれる項目は 100 に制限されています。 Cosmos DB での PaaS および OSS の _include には、反復処理のサポート [(#1313)](https://github.com/microsoft/fhir-server/issues/1313)は含まれません。                               |
| _revinclude                   | Yes                  | Yes                       | Yes                             |  含まれる項目は 100 に制限されています。 Cosmos DB での PaaS および OSS の _revinclude には、反復処理のサポート [(#1313)](https://github.com/microsoft/fhir-server/issues/1313)は含まれません。  イシュー [#1319](https://github.com/microsoft/fhir-server/issues/1319)                            |
| _summary                      | Yes             | Yes                   | Yes                        |                               |
| _total                        | Partial              | Partial                   | Partial                         | _total = none および _total = 正確                               |
| _sort                         | Partial              | Partial                   | Partial                         | sort = _lastUpdated がサポートされています。 2021年4月20日より後に作成された Azure API for FHIR および OSS Cosmos DB データベースでは、first name、last name、および臨床日でも並べ替えがサポートされています。                               |
| _contained                    | いいえ                   | いいえ                        | いいえ                              |                                |
| _containedType                | いいえ                   | いいえ                        | いいえ                              |                                |
| _score                        | いいえ                   | いいえ                        | いいえ                              |                                |

既定では、Azure API for FHIR は、厳密な処理に設定されています。 これは、サーバーが不明なパラメーターまたはサポートされていないパラメーターを無視することを意味します。 厳密な処理を使用する場合は、Prefer ヘッダーを使用して **を設定** できます `handling=strict` 。

 ## <a name="chained--reverse-chained-searching"></a>チェーン&逆連鎖検索

チェーン [検索を使用](https://www.hl7.org/fhir/search.html#chaining) すると、別のリソースによって参照されるリソースの検索パラメーターを使用して検索できます。 たとえば、患者の名前が Jane である検出を見つける場合は、次のコマンドを使用します。

`GET {{FHIR_URL}}/Encounter?subject:Patient.name=Jane`

同様に、逆チェーン検索を実行できます。 これにより、リソースを参照する他のリソースの条件を指定するリソースを取得できます。 チェーン検索とリバース チェーン検索のその他の例については [、FHIR 検索の例に関するページを参照](search-samples.md) してください。 

> [!NOTE]
> Azure API for FHIR と Cosmos DB によってサポートされるオープン ソースでは、チェーン検索とリバース チェーン検索に必要な各サブクエリで返される項目が 100 項目のみになるという制限があります。 見つかった項目が 100 を超える場合は、次のエラー メッセージが表示されます。"チェーンされた式のサブクエリは 100 を超える結果を返すはできない、より選択的な条件を使用してください。" クエリを正常に実行するには、探している情報についてより具体的に説明する必要があります。

## <a name="pagination"></a>改ページ位置の自動修正

前述のように、検索の結果はページ付きバンドルになります。 既定では、検索では 1 ページあたり 10 件の結果が返されますが、 を指定することでこれを増やしたり減らしたりできます `_count` 。 バンドル内には、検索の現在の結果を含む自己リンクがあります。 追加の一致がある場合、バンドルには次のリンクが含まれています。 次のリンクを引き続き使用して、結果の後続のページを取得できます。 `_count` は 1,000 項目以下に制限されます。 

現時点では、Azure API for FHIRはバンドル内の次のリンクのみをサポートし、最初、最後、または前のリンクはサポートされていません。

## <a name="next-steps"></a>次のステップ

これで、検索の基本について学習しました。さまざまな検索パラメーター、修飾子、その他の FHIR 検索シナリオを使用して検索する方法の詳細については、検索サンプル ページを参照してください。

>[!div class="nextstepaction"]
>[FHIR 検索の例](search-samples.md)
