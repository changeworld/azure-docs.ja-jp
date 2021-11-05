---
title: API 形式のサポートの制限事項と詳細
titleSuffix: Azure API Management
description: Azure API Management での Open API、WSDL、および WADL 形式のサポートに関する既知の問題と制限事項の詳細です。
services: api-management
documentationcenter: ''
author: dlepow
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/26/2021
ms.author: danlep
ms.openlocfilehash: 5ddb4d615dc3d1bd7c9830d11a27341baf57edb3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031814"
---
# <a name="api-import-restrictions-and-known-issues"></a>API のインポートに関する制限事項と既知の問題

API をインポートする際は、いくつかの制限事項が発生する場合や、正常にインポートするために問題を特定して修正する必要があることがあります。 この記事では、次の内容について説明します。
* OpenAPI インポート時の API Management の動作。 
* API のインポート形式別のインポートの制限事項。 
* OpenAPI のエクスポートのしくみ。

## <a name="api-management-during-openapi-import"></a>OpenAPI インポート時の API Management

OpenAPI のインポート時に、API Management は次のことを行います。
* 必須とマークされているクエリ文字列パラメーターのみを確認する。
* クエリ文字列パラメーターをテンプレート パラメーターに変換する。 

別の動作を希望する場合は、次のいずれかを実行できます。 
* フォームベースのエディターを使用して手動で変更します。 
* OpenAPI 定義から "必須" 属性を削除します。これにより、テンプレート パラメーターに変換されることはなくなります。

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>OpenAPI/Swagger のインポートの制限事項

OpenAPI ドキュメントのインポート中にエラーが発生した場合は、事前に以下のどちらかで検証を行っていることを確認します。
* Azure portal のデザイナーを使用する ([デザイン] > [フロント エンド] > [OpenAPI 仕様エディター])、または 
* <a href="https://editor.swagger.io">Swagger エディター</a>などのサードパーティー ツールを使用する。

### <a name="general"></a><a name="open-api-general"> </a>全般

#### <a name="url-template-requirements"></a>URL テンプレートの要件

| 要件 | 説明 |
| ----------- | ----------- |
| **必須パスとクエリ パラメーターの一意の名前** | OpenAPI の場合: <ul><li>パラメーター名は、パス、クエリ、ヘッダーなどの場所でのみ一意である必要があります。</li></ul>API Management の場合:<ul><li>パスのクエリの両方のパラメーターによって操作を区別できます。</li><li>OpenAPI ではこの区別がサポートされていないため、URL テンプレート全体でパラメーター名を一意にする必要があります。</li></ul>  |
| **定義済みの URL パラメーター** | URL テンプレートの一部である必要があります。 |
| **利用可能なソース ファイルの URL** | 相対サーバー URL に適用されます。 |
| **`\$ref` ポインター** | 外部ファイルを参照できません。 |


#### <a name="openapi-specifications"></a>OpenAPI の仕様

**サポートされているバージョン**

API Management は以下のみをサポートします。
* OpenAPI バージョン 2。
* OpenAPI バージョン 3.0.x (最大バージョン 3.0.3)。

OpenAPI バージョン 3.1 は、現在 API Management ではサポートされていません。

**サイズの制限**

| サイズ制限 | 説明 |
| ---------- | ----------- |
| **最大 4 MB** | OpenAPI 仕様を API Management にインラインでインポートした場合。 |
| **サイズ制限は適用されない** | API Management サービスからアクセスできる場所の URL 経由で OpenAPI ドキュメントが提供される場合。 |

#### <a name="supported-extensions"></a>サポートされる拡張機能
以下の拡張機能のみがサポートされています。

| 拡張機能 | 説明 |
| ----------- | ----------- |
| **`x-ms-paths`** | <ul><li>URL のクエリ パラメーターで区別されるパスを定義できます。</li><li>[AutoRest のドキュメント](https://github.com/Azure/autorest/tree/main/docs/extensions#x-ms-paths)で説明されています。</li></ul> |
| **`x-servers`** | OpenAPI 2 用の [OpenAPI 3 `servers` オブジェクト](https://swagger.io/docs/specification/api-host-and-base-path/)のバックポート。 |

#### <a name="unsupported-extensions"></a>サポートされていない拡張機能
| 拡張機能 | 説明 |
| ----------- | ----------- |
| **`Recursion`** | API Management では、再帰的に定義された定義はサポートされません。<br />たとえば、自身を参照するスキーマなどです。 |
| **`Server` オブジェクト** | API 操作レベルでサポートされていません。 |
| **`Produces` キーワード** | API によって返される MIME の種類について説明します。 <br />サポートされていません。 |

#### <a name="custom-extensions"></a>カスタム拡張機能
- インポート時に無視されます。
- エクスポート用に保存または予約されません。

#### <a name="unsupported-definitions"></a>サポートされていない定義 
API 操作に対するインライン スキーマ定義はサポートされていません。 スキーマ定義:
- API スコープで定義されます。
- API 操作の要求または応答のスコープで参照できます。

#### <a name="ignored-definitions"></a>無視される定義
セキュリティ定義は無視されます。

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI バージョン 2

OpenAPI バージョン 2 のサポートは、JSON 形式のみに制限されています。

### <a name="openapi-version-30x"></a><a name="open-api-v3"> </a>OpenAPI バージョン 3.0.x

サポートされている最新の OpenAPI バージョン 3.0 は 3.0.3 です。

#### <a name="https-urls"></a>HTTPS URL
- 複数の `servers` が指定されている場合、API Management では、最初に見つかった HTTPS URL が使用されます。 
- HTTPS URL がない場合、サーバーの URL は空になります。

#### <a name="supported"></a>サポートされています
- `example`

#### <a name="unsupported"></a>サポートされていない
次のフィールドは [OpenAPI バージョン 3.0.x](https://swagger.io/specification/) に含まれていますが、サポートされていません。

| Object | フィールド |
| ----------- | ----------- |
| **OpenAPI** | `externalDocs` |
| **Components** | <ul><li>`responses`</li><li>`parameters`</li><li>`examples`</li><li>`requestBodies`</li><li>`headers`</li><li>`securitySchemes`</li><li>`links`</li><li>`callbacks`</li></ul> |
| **PathItem** | <ul><li>`trace`</li><li>`servers`</li></ul> |
| **操作** | <ul><li>`externalDocs`</li><li>`callbacks`</li><li>`security`</li><li>`servers`</li></ul> |
| **パラメーター** | <ul><li>`allowEmptyValue`</li><li>`style`</li><li>`explode`</li><li>`allowReserved`</li></ul> |

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI のインポート、更新、およびエクスポートのメカニズム

### <a name="general"></a><a name="open-import-export-general"> </a>全般

API Management サービスからエクスポートされる API 定義は:
* 主に API Management サービスでホストされる API を呼び出す必要がある外部アプリケーションを対象としています。 
* 同じ、または別の API Management サービスにインポートするためのものではありません。 

さまざまなサービスまたは環境にわたる API 定義の構成管理については、Git での API Management サービスの使用に関するドキュメントを参照してください。 

### <a name="add-new-api-via-openapi-import"></a>OpenAPI のインポートを使用して新しい API を追加する

OpenAPI ドキュメントで見つかった操作ごとに、次の操作で新しい操作が作成されます。
* Azure リソース名を `operationId` に設定します。
    * `operationId` 値は正規化されます。
    *  `operationId` が指定されていない場合 (存在しないか、`null` であるか、空である)、Azure リソース名の値は、HTTP メソッドとパス テンプレートを組み合わせて生成されます。
        * たとえば、「 `get-foo` 」のように入力します。

* 表示名を `summary` に設定します。 
    * `summary` 値:
        * そのままインポートされます。
        * 長さは 300 文字に制限されています。
    * `summary` が指定されていない場合 (存在しないか、`null` であるか、空である)、表示名の値は `operationId` に設定されます。 

### <a name="update-an-existing-api-via-openapi-import"></a>OpenAPI のインポートを使用して既存の API を更新する

インポート中、既存の API 操作は:
* OpenAPI ドキュメントで説明されている API に一致するように変更されます。 
* `operationId` 値を既存の操作の Azure リソース名と比較することによって、OpenAPI ドキュメント内の操作と照合されます。 
    * 一致が見つかった場合、既存の操作のプロパティは "インプレースで" 更新されます。
    * 一致が見つからない場合:
        * 新しい操作は、HTTP メソッドとパス テンプレートを組み合わせることによって作成されます (たとえば、`get-foo`)。 
        * 新しい操作ごとに、インポートにより、同じ HTTP メソッドとパス テンプレートを使用する既存の操作からのポリシーのコピーが試みられます。

一致しない既存の操作はすべて削除されます。

インポートをより予測しやすくするには、次のガイドラインに従ってください。

- すべての操作に対して `operationId` プロパティを指定します。
- 最初のインポート後に `operationId` を変更しないでください。
- `operationId` と HTTP メソッドまたはパス テンプレートを同時に変更しないでください。

### <a name="export-api-as-openapi"></a>API を OpenAPI としてエクスポートする

各操作について、次のようになります。
* Azure リソース名は `operationId` としてエクスポートされます。
* 表示名は `summary` としてエクスポートされます。

**`operationId` の正規化規則**
- 小文字に変換します。
- 英数字以外の文字のシーケンスをそれぞれ 1 つのダッシュで置き換えます。
    - たとえば、`GET-/foo/{bar}?buzz={quix}` は `get-foo-bar-buzz-quix-` に変換されます。
- 両側のダッシュがトリミングされます。
    - たとえば、`get-foo-bar-buzz-quix-` は `get-foo-bar-buzz-quix` になります
- リソース名の最大制限より 4 文字少ない 76 文字に収まるように切り捨てます。
- 残りの 4 文字は、必要に応じて `-1, -2, ..., -999` の形式で、重複除去サフィックスに使用します。

## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

WSDL ファイルを使用して、SOAP パススルー と SOAP-to-REST の API を作成できます。

### <a name="soap-bindings"></a>SOAP バインディング 
- "document" と "literal" のエンコード スタイルの SOAP バインディングのみがサポートされています。
- "rpc" スタイルまたは SOAP エンコードはサポートされていません。

### <a name="wsdlimport"></a>WSDL:Import
サポートされていません。 代わりに、インポートを 1 つのドキュメントにマージする必要があります。

### <a name="messages-with-multiple-parts"></a>複数部分を含むメッセージ 
このメッセージ型はサポートされていません。

### <a name="wcf-wshttpbinding"></a>WCF wsHttpBinding 
- Windows Communication Foundation で作成された SOAP サービスは、`basicHttpBinding` を使用する必要があります。
- <ph id="ph1">`SUSER_SID`</ph> はサポートされていません。

### <a name="mtom"></a>MTOM 
- `MTOM` を使用するサービスが機能する "*場合があります*"。 
- 現時点では、正式なサポートは提供されていません。

### <a name="recursion"></a>再帰
- 再帰的に定義された型は、API Management ではサポートされていません。
- たとえば、自身の配列の参照などです。

### <a name="multiple-namespaces"></a>複数の名前空間
1 つのスキーマで複数の名前空間を使用できますが、メッセージ部分を定義するために使用できるのはターゲット名前空間だけです。 これらの名前空間は、他の入力または出力の要素を定義するために使用されます。

ターゲット以外の名前空間は、エクスポート時に予約されていません。 他の名前空間を使用してメッセージ部分を定義した WSDL ドキュメントをインポートできますが、すべてのメッセージ部分では、エクスポート時に WSDL ターゲットの名前空間が使用されます。

### <a name="arrays"></a>配列 
SOAP-to-REST の変換では、次の例に示すラップされた配列のみがサポートされています。

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

現時点では、WADL のインポートに関する既知の問題はありません。
