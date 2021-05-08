---
title: Azure Front Door Standard または Premium のルール セットの一致条件を構成する
description: この記事では、Azure Front Door Standard または Premium のルール セットで利用できるさまざまな一致条件の一覧を示します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: 9e8defa9e929d21f210c48ffbd3b22e44195c17d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061623"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Azure Front Door Standard または Premium (プレビュー) のルール セットの一致条件

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

Azure Front Door Standard または Premium の[ルール セット](concept-rule-set.md)では、ルールは 0 個以上の一致条件と 1 つのアクションで構成されています。 この記事では、Azure Front Door Standard または Premium のルール セットで使用できる一致条件について詳しく説明します。

ルールの最初の部分は、単一の一致条件または一連の一致条件です。 ルールは、最大 10 個の一致条件で構成できます。 一致条件とは、要求の特定の種類を識別するものであり、その種類に対して指定されたアクションが実行されます。 複数の一致条件を使用すると、一致条件は AND ロジックを使用してグループ化されます。 複数の値をサポートするすべての一致条件には、OR ロジックが使用されます。

一致条件を使用して、次のことができます。

* 特定の IP アドレス、国、または地域に基づいて要求をフィルター処理します。
* ヘッダー情報別に要求をフィルター処理する。
* モバイル デバイスまたはデスクトップ デバイスからの要求をフィルター処理する。
* 要求ファイル名とファイル拡張子から要求をフィルター処理する。
* 要求 URL、プロトコル、パス、クエリ文字列、ポスト引数などから要求をフィルター処理する。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="device-type"></a><a name="IsDevice"></a> デバイスの種類

**デバイスの種類** の一致条件を使用して、モバイル デバイスまたはデスクトップ デバイスから行われた要求を識別します。  

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|-------|------------------|
| 演算子 | <ul><li>Azure portal: `Equal`、`Not Equal`</li><li>ARM テンプレート: `Equal`。_Not Equal_ を指定するには、`negateCondition` プロパティを使用する |
| 値 | `Mobile`, `Desktop` |

### <a name="example"></a>例

この例では、モバイル デバイスからの受信として検出されたすべての要求を照合します。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/device-type.png" alt-text="デバイスの種類の一致条件を示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "IsDevice",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "Mobile"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'IsDevice'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'Mobile'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters'
  }
}
```

---

## <a name="post-args"></a><a name="PostArgs"></a> ポストの引数

POST 要求の本文内で指定された引数に基づいて要求を識別するには、**ポストの引数** の一致条件を使用します。 1 つの一致条件は、POST 要求の本文内の 1 つの引数を照合します。 照合する値は複数指定できます。これは、OR ロジックを使用して結合されます。

> [!NOTE]
> **ポストの引数** の一致条件は、`application/x-www-form-urlencoded` コンテンツ タイプで動作します。

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|-|-|
| ポストの引数 | POST 引数の名前を表す文字列値。 |
| 演算子 | [標準の演算子リスト](#operator-list)にある任意の演算子。 |
| 値 | 照合する POST 引数の値を表す 1 つ以上の文字列または整数の値。 複数の値が指定された場合は、OR ロジックを使用して評価されます。 |
| 大文字と小文字の変換 | `Lowercase`, `Uppercase` |

### <a name="example"></a>例

この例では、要求本文に `customerName` 引数が指定され、かつ `customerName` の値が文字 `J` または`K` で始まるという条件で、すべての POST 要求を照合します。 先頭の文字が `J`、`j`、`K`、および `k` である値がすべて一致するように、大文字と小文字の変換を使用して、入力値を大文字に変換します。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/post-args.png" alt-text="ポストの引数の一致条件を示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "PostArgs",
  "parameters": {
    "selector": "customerName",
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
        "J",
        "K"
    ],
    "transforms": [
        "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'PostArgs'
  parameters: {
    selector: 'customerName'
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'J'
      'K'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters'
  }
}
```

---

## <a name="query-string"></a><a name="QueryString"></a> クエリ文字列

**クエリ文字列** の一致条件を使用して、特定のクエリ文字列を含む要求を識別します。 照合する値は複数指定できます。これは、OR ロジックを使用して結合されます。

> [!NOTE]
> 先頭に `?` を付けずに、クエリ文字列全体が 1 つの文字列として照合されます。

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|-|-|
| 演算子 | [標準の演算子リスト](#operator-list)にある任意の演算子。 |
| クエリ文字列 | 照合するクエリ文字列の値を表す 1 つ以上の文字列または整数の値。 クエリ文字列の先頭に `?` を含めないでください。 複数の値が指定された場合は、OR ロジックを使用して評価されます。 |
| 大文字と小文字の変換 | `Lowercase`, `Uppercase` |

### <a name="example"></a>例

この例では、クエリ文字列に文字列 `language=en-US` が含まれているという条件で、すべての要求を照合します。 一致条件で大文字と小文字の区別をするため、大文字と小文字の変換はしません。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/query-string.png" alt-text="クエリ文字列の一致条件を示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "QueryString",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "language=en-US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'QueryString'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'language=en-US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters'
  }
}
```

---

## <a name="remote-address"></a><a name="RemoteAddress"></a> リモート アドレス

**リモート アドレス** の一致条件は、要求者の位置情報または IP アドレスに基づいて要求を識別します。 照合する値は複数指定できます。これは、OR ロジックを使用して結合されます。

* IP アドレス ブロックを指定する場合は、CIDR 表記を使用します。 これは、IP アドレス ブロックの構文が、スラッシュおよびプレフィックス サイズが後に続くベース IP アドレスであることを意味します。 例:
    * **IPv4 の例**:`5.5.5.64/26` と指定した場合、アドレス 5.5.5.64 ～ 5.5.5.127 から配信される要求と一致します。
    * **IPv6 の例**:`1:2:3:/48` と指定した場合、アドレス 1:2:3:0:0:0:0:0 ～ 1:2:3: ffff:ffff:ffff:ffff:ffff から配信される要求と一致します。
* 複数の IP アドレスと IP アドレス ブロックを指定すると、'OR' ロジックが適用されます。
    * **IPv4 の例**: 2 つの IP アドレス `1.2.3.4` と `10.20.30.40` を追加した場合、条件はアドレス 1.2.3.4 または 10.20.30.40 のいずれかから届いた要求に対して照合されます。
    * **IPv6 の例**: 2 つの IP アドレス `1:2:3:4:5:6:7:8` と `10:20:30:40:50:60:70:80` を追加した場合、条件はアドレス 1:2:3:4:5:6:7:8 または 10:20:30:40:50:60:70:80 のいずれかから届いた要求に対して照合されます。

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|-|-|
| 演算子 | <ul><li>Azure portal: `Geo Match`、`Geo Not Match`、`IP Match`、または `IP Not Match`</li><li>ARM テンプレート: `GeoMatch`、`IPMatch`。_Geo Not Match_ または _IP Not Match_ を指定するには、`negateCondition` プロパティを使用する</li></ul> |
| 値 | <ul><li>`IP Match` または `IP Not Match` 演算子の場合: 1 つ以上の IP アドレス範囲を指定します。 複数の IP アドレスの範囲が指定された場合は、OR ロジックを使用して評価されます。</li><li>`Geo Match` または `Geo Not Match` 演算子の場合: 国番号を使用して 1 つ以上の場所を指定します。</li></ul> |

### <a name="example"></a>例

この例では、要求が米国から発信されていないという条件で、すべての要求を照合します。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/remote-address.png" alt-text="リモート アドレスの一致条件を示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RemoteAddress",
  "parameters": {
    "operator": "GeoMatch",
    "negateCondition": true,
    "matchValues": [
      "US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RemoteAddress'
  parameters: {
    operator: 'GeoMatch'
    negateCondition: true
    matchValues: [
      'US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters'
  }
}
```

---

## <a name="request-body"></a><a name="RequestBody"></a> 要求本文

**要求本文** の一致条件は、要求の本文に表示される特定のテキストに基づいて要求を識別します。 照合する値は複数指定できます。これは、OR ロジックを使用して結合されます。

> [!NOTE]
> 要求本文のサイズが 64 KB を超える場合、最初の 64 KB のみが **要求本文** の一致条件に対して考慮されます。

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|-|-|
| 演算子 | [標準の演算子リスト](#operator-list)にある任意の演算子。 |
| 値 | 照合する要求本文テキストの値を表す 1 つ以上の文字列または整数の値。 複数の値が指定された場合は、OR ロジックを使用して評価されます。 |
| 大文字と小文字の変換 | `Lowercase`, `Uppercase` |

### <a name="example"></a>例

この例では、要求本文に文字列 `ERROR` が含まれているという条件で、すべての要求を照合します。 `error` および他の大文字と小文字のバリエーションによってもこの一致条件がトリガーされるように、一致を評価する前に要求本文を大文字に変換します。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-body.png" alt-text="要求本文の一致条件を示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestBody",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "ERROR"
    ],
    "transforms": [
      "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestBody'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'ERROR'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters'
  }
}
```

---

## <a name="request-file-name"></a><a name="UrlFileName"></a> 要求ファイル名

**要求ファイル名** の一致条件は、指定したファイル名が要求 URL に含まれている要求を識別します。 照合する値は複数指定できます。これは、OR ロジックを使用して結合されます。

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|-|-|
| 演算子 | [標準の演算子リスト](#operator-list)にある任意の演算子。 |
| 値 | 照合する要求ファイル名の値を表す 1 つ以上の文字列または整数の値。 複数の値が指定された場合は、OR ロジックを使用して評価されます。 |
| 大文字と小文字の変換 | `Lowercase`, `Uppercase` |

### <a name="example"></a>例

この例では、要求ファイル名が `media.mp4` であるという条件で、すべての要求を照合します。 `MEDIA.MP4` および他の大文字と小文字のバリエーションによってもこの一致条件がトリガーされるように、一致を評価する前にファイル名を小文字に変換します。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-name.png" alt-text="要求ファイル名の一致条件を示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileName",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "media.mp4"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileName'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'media.mp4'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters'
  }
}
```

---

## <a name="request-file-extension"></a><a name="UrlFileExtension"></a> 要求ファイル拡張子

**要求ファイル拡張子** の一致条件は、指定したファイル拡張子が要求 URL のファイル名に含まれている要求を識別します。 照合する値は複数指定できます。これは、OR ロジックを使用して結合されます。

> [!NOTE]
> 先頭にピリオドを含めないでください。 たとえば、`.html` の代わりに `html` を使用します。

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|-|-|
| 演算子 | [標準の演算子リスト](#operator-list)にある任意の演算子。 |
| 値 | 照合する要求ファイル拡張子の値を表す 1 つ以上の文字列または整数の値。 先頭にピリオドを含めないでください。 複数の値が指定された場合は、OR ロジックを使用して評価されます。 |
| 大文字と小文字の変換 | `Lowercase`, `Uppercase` |

### <a name="example"></a>例

この例では、要求ファイル拡張子が `pdf` または `docx`であるという条件で、すべての要求を照合します。 `PDF`、`DocX`、および他の大文字と小文字のバリエーションによってもこの一致条件がトリガーされるように、一致を評価する前に要求ファイル拡張子を小文字に変換します。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-extension.png" alt-text="要求ファイル拡張子の一致条件を示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileExtension",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "pdf",
      "docx"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters"
  }
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileExtension'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'pdf'
      'docx'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters'
  }
}
```

---

## <a name="request-header"></a><a name="RequestHeader"></a> 要求ヘッダー

**要求ヘッダー** の一致条件は、要求内に特定のヘッダーが含まれる要求を識別します。 この一致条件を使用して、ヘッダーが存在するかどうかを (その値は問わずに) 確認したり、指定された値にヘッダーが一致するかどうかを確認したりできます。 照合する値は複数指定できます。これは、OR ロジックを使用して結合されます。

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|-|-|
| ヘッダー名 | POST 引数の名前を表す文字列値。 |
| 演算子 | [標準の演算子リスト](#operator-list)にある任意の演算子。 |
| 値 | 照合する要求ヘッダーの値を表す 1 つ以上の文字列または整数の値。 複数の値が指定された場合は、OR ロジックを使用して評価されます。 |
| 大文字と小文字の変換 | `Lowercase`, `Uppercase` |

### <a name="example"></a>例

この例では、要求に `MyCustomHeader` という名前のヘッダーが含まれている (その値は問わない) という条件で、すべての要求を照合します。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-header.png" alt-text="要求ヘッダーの一致条件を示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestHeader",
  "parameters": {
    "selector": "MyCustomHeader",
    "operator": "Any",
    "negateCondition": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestHeader'
  parameters: {
    selector: 'MyCustomHeader',
    operator: 'Any'
    negateCondition: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters'
  }
}
```

---

## <a name="request-method"></a><a name="RequestMethod"></a> 要求メソッド

**要求メソッド** の一致条件は、指定した HTTP 要求メソッドを使用する要求を識別します。 照合する値は複数指定できます。これは、OR ロジックを使用して結合されます。

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|-|-|
| 演算子 | <ul><li>Azure portal: `Equal`、`Not Equal`</li><li>ARM テンプレート: `Equal`。_Not Equal_ を指定するには、`negateCondition` プロパティを使用する |
| 要求メソッド | `GET`、`POST`、`PUT`、`DELETE`、`HEAD`、`OPTIONS`、`TRACE` のうち、1 つ以上の HTTP メソッド。 複数の値が指定された場合は、OR ロジックを使用して評価されます。 |

### <a name="example"></a>例

この例では、要求で `DELETE` メソッドが使用されているという条件で、すべての要求を照合します。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-method.png" alt-text="要求メソッドの一致条件を示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestMethod",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "DELETE"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestMethod'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'DELETE
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters'
  }
}
```

---

## <a name="request-path"></a><a name="UrlPath"></a> 要求パス

**要求パス** の一致条件は、指定したパスが要求 URL に含まれている要求を識別します。 照合する値は複数指定できます。これは、OR ロジックを使用して結合されます。

> [!NOTE]
> パスは、URL のホスト名とスラッシュの後の部分です。 たとえば、URL `https://www.contoso.com/files/secure/file1.pdf` では、パスは `files/secure/file1.pdf` です。

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|-|-|
| 演算子 | [標準の演算子リスト](#operator-list)にある任意の演算子。 |
| 値 | 照合する要求パスの値を表す 1 つ以上の文字列または整数の値。 先頭にスラッシュを含めないでください。 複数の値が指定された場合は、OR ロジックを使用して評価されます。 |
| 大文字と小文字の変換 | `Lowercase`, `Uppercase` |

### <a name="example"></a>例

この例では、要求ファイル パスが `files/secure/` で始まるという条件で、すべての要求を照合します。 `files/SECURE/` および他の大文字と小文字のバリエーションによってもこの一致条件がトリガーされるように、一致を評価する前に要求ファイル パスを小文字に変換します。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-path.png" alt-text="要求パスの一致条件を示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlPath",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "files/secure/"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlPath'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'files/secure/'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters'
  }
}
```

---

## <a name="request-protocol"></a><a name="RequestScheme"></a> 要求プロトコル

**要求プロトコル** の一致条件は、指定したプロトコル (HTTP または HTTPS) を使用している要求を識別します。

> [!NOTE]
> "*プロトコル*" は "*スキーム*" とも呼ばれます。

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|-|-|
| 演算子 | <ul><li>Azure portal: `Equal`、`Not Equal`</li><li>ARM テンプレート: `Equal`。_Not Equal_ を指定するには、`negateCondition` プロパティを使用する |
| 要求メソッド | `HTTP`, `HTTPS` |

### <a name="example"></a>例

この例では、要求で `HTTP` プロトコルが使用されているという条件で、すべての要求を照合します。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-protocol.png" alt-text="要求プロトコルの一致条件を示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestScheme",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "HTTP"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestScheme'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'HTTP
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters'
  }
}
```

---

## <a name="request-url"></a><a name="RequestUrl"></a> 要求 URL

指定された URL に一致する要求を識別します。 URL 全体が評価されます。これには、プロトコルとクエリ文字列が含まれますが、フラグメントは含まれません。 照合する値は複数指定できます。これは、OR ロジックを使用して結合されます。

> [!TIP]
> このルール条件を使用する場合は、必ずプロトコルを含めてください。 たとえば、単に `www.contoso.com` ではなく、`https://www.contoso.com` を使用します。

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|-|-|
| 演算子 | [標準の演算子リスト](#operator-list)にある任意の演算子。 |
| 値 | 照合する要求 URL の値を表す 1 つ以上の文字列または整数の値。 複数の値が指定された場合は、OR ロジックを使用して評価されます。 |
| 大文字と小文字の変換 | `Lowercase`, `Uppercase` |

### <a name="example"></a>例

この例では、要求 URL が `https://api.contoso.com/customers/123` で始まるという条件で、すべての要求を照合します。 `https://api.contoso.com/Customers/123` および他の大文字と小文字のバリエーションによってもこの一致条件がトリガーされるように、一致を評価する前に要求 URL を小文字に変換します。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-url.png" alt-text="要求 URL の一致条件を示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestUri",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "https://api.contoso.com/customers/123"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestUri'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'https://api.contoso.com/customers/123'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters'
  }
}
```

---

## <a name="operator-list"></a><a name = "operator-list"></a> 演算子リスト

標準の演算子リストからの値を受け入れるルールの場合、次の演算子が有効です。

| 演算子                   | 説明                                                                                                                    | ARM テンプレートのサポート                                            |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Any                        | 値が何であるかに関係なく、何らかの値がある場合に一致します。                                                                     | `operator`: `Any`                                               |
| 等しい                      | 値が指定した文字列に完全に一致した場合に一致します。                                                                   | `operator`: `Equal`                                             |
| Contains                   | 指定した文字列が値に含まれている場合に一致します。                                                                          | `operator`: `Contains`                                          |
| より小さい                  | 値の長さが指定した整数よりも短い場合に一致します。                                                       | `operator`: `LessThan`                                          |
| より大きい               | 値の長さが指定した整数よりも長い場合に一致します。                                                    | `operator`: `GreaterThan`                                       |
| 以下         | 値の長さが指定した整数以下である場合に一致します。                                           | `operator`: `LessThanOrEqual`                                   |
| 以上      | 値の長さが指定した整数以上である場合に一致します。                                        | `operator`: `GreaterThanOrEqual`                                |
| [次で始まる]                | 値が指定した文字列で始まる場合に一致します。                                                                       | `operator`: `BeginsWith`                                        |
| 指定の値で終わる                  | 値が指定した文字列で終わる場合に一致します。                                                                         | `operator`: `EndsWith`                                          |
| 正規表現                      | 値が指定した正規表現に一致した場合に一致します。 [詳細については下を参照してください。](#regular-expressions)        | `operator`: `RegEx`                                             |
| いずれでもありません                    | 値がない場合に一致します。                                                                                                | `operator`: `Any` および `negateCondition` : `true`                |
| 等しくない                  | 値が指定した文字列に一致しない場合に一致します。                                                                    | `operator`: `Equal` および `negateCondition` : `true`              |
| 指定値を含まない               | 指定した文字列が値に含まれていない場合に一致します。                                                                  | `operator`: `Contains` および `negateCondition` : `true`           |
| 次の値より小さくない              | 値の長さが指定した整数より短くない場合に一致します。                                                   | `operator`: `LessThan` および `negateCondition` : `true`           |
| 次の値より大きくない           | 値の長さが指定した整数より長くない場合に一致します。                                                | `operator`: `GreaterThan` および `negateCondition` : `true`        |
| 次の値以下ではない     | 値の長さが指定した整数以下ではない場合に一致します。                                       | `operator`: `LessThanOrEqual` および `negateCondition` : `true`    |
| 次の値以上ではない | 値の長さが指定した整数以上ではない場合に一致します。                                    | `operator`: `GreaterThanOrEqual` および `negateCondition` : `true` |
| 次の値で始まらない            | 値が指定した文字列で始まらない場合に一致します。                                                               | `operator`: `BeginsWith` および `negateCondition` : `true`         |
| 次の値で終わらない              | 値が指定した文字列で終わらない場合に一致します。                                                                 | `operator`: `EndsWith` および `negateCondition` : `true`           |
| 正規表現ではない                  | 値が指定した正規表現に一致しない場合に一致します。 [詳細については下を参照してください。](#regular-expressions) | `operator`: `RegEx` および `negateCondition` : `true`              |

> [!TIP]
> *より小さい* または *以上* のような数値演算子の場合、比較は長さに基づいて行われます。 一致条件の値は、比較する長さを示す整数にする必要があります。

### <a name="regular-expressions"></a><a name="regular-expressions"></a> 正規表現

正規表現では、次の操作はサポートされていません。

* 前方参照と部分式の取得。
* 任意のゼロ幅アサーション。
* サブルーチン参照と再帰的パターン。
* 条件付きパターン。
* バックトラッキング制御動詞。
* `\C` 1 バイト ディレクティブ。
* `\R` 改行一致ディレクティブ。
* `\K` 一致の開始のリセット ディレクティブ。
* コールアウトと埋め込みコード。
* アトミック グループ化と所有の量指定子。

## <a name="next-steps"></a>次のステップ

* [ルール セット](concept-rule-set.md)の詳細について説明します。
* [最初のルール セットを構成する](how-to-configure-rule-set.md)方法について確認します。
* [ルール セットのアクション](concept-rule-set-actions.md)の詳細について説明します。
