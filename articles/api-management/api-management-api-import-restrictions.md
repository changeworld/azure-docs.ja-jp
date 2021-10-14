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
ms.date: 09/24/2021
ms.author: danlep
ms.openlocfilehash: 3f16961ec4774708fa55a2a49e408a6b980cdb31
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057608"
---
# <a name="api-import-restrictions-and-known-issues"></a>API のインポートに関する制限事項と既知の問題

## <a name="about-this-list"></a>このリストについて

API をインポートする際は、いくつかの制限事項が発生する場合や、正常にインポートするために問題を特定して修正する必要があることがあります。 この記事では、次の内容について説明します。
* API のインポート形式別のすべての制限事項。 
* OpenAPI のエクスポートのしくみ。

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>OpenAPI/Swagger のインポートの制限事項

OpenAPI ドキュメントのインポート中にエラーが発生した場合は、事前に以下のどちらかで検証を行っていることを確認します。
* Azure portal のデザイナーを使用する ([デザイン] > [フロント エンド] > [OpenAPI 仕様エディター])、または 
* <a href="https://editor.swagger.io">Swagger エディター</a>などのサードパーティー ツールを使用する。

### <a name="general"></a><a name="open-api-general"> </a>全般

- パスとクエリの両方の間で必要なパラメーターは、一意の名前が必要です。
    - OpenAPI では、パラメーター名は、パス、クエリ、ヘッダーなどの場所でのみ一意である必要があります。 
    - API Management では、パスのクエリの両方のパラメーターによって操作を区別できます。 
        - OpenAPI ではこれがサポートされていないため、URL テンプレート全体でパラメーター名を一意にする必要があります。
- API Management にインラインでインポートする場合、OpenAPI 仕様の最大許容サイズは 4 MB です。 
    - API Management サービスからアクセスできる場所の URL 経由で OpenAPI ドキュメントが提供される場合、このサイズ制限は適用されません。
- `\$ref` ポインターは、外部ファイルを参照できません。
- 以下の拡張機能のみがサポートされています。
    - `x-ms-paths` 
    - `x-servers` 
- カスタム拡張機能:
    - インポート時に無視されます。
    - エクスポート用に保存または予約されません。
- セキュリティ定義は無視されます。
- `Recursion`: 
    - API Management では、再帰的に定義された定義はサポートされません。
    - たとえば、自身を参照するスキーマなどです。
- `server` オブジェクトは API 操作レベルでサポートされていません。
- API によって返される MIME 型を説明する `Produces` キーワードはサポートされていません。 
- 使用できるソース ファイルの URL が、相対的なサーバーの URL に適用されます。
- API 操作に対するインライン スキーマ定義はサポートされていません。 スキーマ定義:
    - API スコープで定義されます。
    - API 操作の要求または応答のスコープで参照できます。
- 定義された URL パラメーターは URL テンプレートに含まれている必要があります。

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI バージョン 2

OpenAPI バージョン 2 のサポートは、JSON 形式のみに制限されています。

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI バージョン 3

-   複数の `servers` が指定されている場合、API Management では、最初に見つかった HTTPS URL が使用されます。 
- HTTPS URL がない場合、サーバーの URL は空になります。
- `Examples` はサポートされていませんが、`example` はサポートされています。
- 次のフィールドは OpenAPI バージョン 3.x に含まれていますが、サポートされていません。
    - `explode`
    - `style`
    - `allowReserved`

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI のインポート、更新、およびエクスポートのメカニズム

### <a name="general"></a><a name="open-import-export-general"> </a>全般

API Management サービスからエクスポートされる API 定義は:
* 主に API Management サービスでホストされる API を呼び出す必要がある外部アプリケーションを対象としています。 
* 同じ、または別の API Management サービスにインポートするためのものではありません。 

さまざまなサービスまたは環境にわたる API 定義の構成管理については、Git での API Management サービスの使用に関するドキュメントを参照してください。 

### <a name="add-new-api-via-openapi-import"></a>OpenAPI のインポートを使用して新しい API を追加する

OpenAPI ドキュメントで見つかった操作ごとに、新しい操作が作成され、Azure リソース名と表示名がそれぞれ `operationId` と `summary` に設定されます。 
* `operationId` 値は正規化されます。
    *  `operationId` が指定されていない場合 (存在しないか、`null` であるか、空である)、Azure リソース名の値は、HTTP メソッドとパス テンプレートを組み合わせて生成されます (たとえば、`get-foo`)。
* `summary` 値はそのままインポートされ、その長さは 300 文字に制限されます。
    * `summary` が指定されていない場合 (存在しないか、`null` であるか、空である)、`display name` 値は `operationId` に設定されます。 

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

各操作の:
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

- **SOAP バインディング**  
    - "document" と "literal" のエンコード スタイルの SOAP バインディングのみがサポートされています。
    - "rpc" スタイルまたは SOAP エンコードはサポートされていません。
- **WSDL:Import**
    - サポートされていません。 代わりに、インポートを 1 つのドキュメントにマージする必要があります。
- **複数部分を含むメッセージ** 
    - このメッセージ型はサポートされていません。
- **WCF wsHttpBinding** 
    - Windows Communication Foundation で作成された SOAP サービスは、`basicHttpBinding` を使用する必要があります。
    - <ph id="ph1">`SUSER_SID`</ph> はサポートされていません。
- **MTOM** 
    - `MTOM` を使用するサービスが機能する "*場合があります*"。 
    - 現時点では、正式なサポートは提供されていません。
- **再帰** 
    - 再帰的に定義された型は、API Management ではサポートされていません。
    - たとえば、自身の配列の参照などです。
- **複数の名前空間** 
    - 1 つのスキーマで複数の名前空間を使用できますが、メッセージ部分を定義するために使用できるのはターゲット名前空間だけです。 
    - ターゲット以外の名前空間は予約されていません。 
        - これらの名前空間は、他の入力または出力の要素を定義するために使用されます。
        - そのような WSDL ドキュメントはインポートできますが、エクスポート時に、すべてのメッセージ部分に WSDL のターゲット名前空間が含まれます。
- **配列** 
    - SOAP-to-REST の変換では、次の例に示すラップされた配列のみがサポートされています。

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
