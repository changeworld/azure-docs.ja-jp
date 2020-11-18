---
title: Azure App Configuration REST API - キー値のリビジョン
description: Azure App Configuration REST API を使用してキー値のリビジョンを操作するための参照ページ
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 7d1990d6bc524a69de2b22b4f7c5aeec88c3ce9d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423819"
---
# <a name="key-value-revisions"></a>キー値のリビジョン

api-version: 1.0

**キー値のリビジョン** によって、キー値リソースの履歴表現が定義されます。 リビジョンは、Free レベルのストアの場合は 7 日後、Standard レベルのストアの場合は 30 日後に有効期限が切れます。 リビジョンでは、次の操作がサポートされます。

- List

すべての操作で、``key`` は省略可能なパラメーターです。 省略されている場合は、**すべての** キーを意味します。
すべての操作で、``label`` は省略可能なパラメーターです。 省略されている場合は、**すべての** ラベルを意味します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-revisions"></a>リビジョンを一覧表示する

```http
GET /revisions?label=*&api-version={api-version} HTTP/1.1
```

**応答:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8"
Accept-Ranges: items
```

```json
{
    "items": [
        {
          "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
          "key": "{key}",
          "label": "{label}",
          "content_type": null,
          "value": "example value",
          "last_modified": "2017-12-05T02:41:26.4874615+00:00",
          "tags": []
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>改ページ位置の自動修正

返された項目の数が応答の制限を超えている場合、結果は改ページされます。 省略可能な ``Link`` 応答ヘッダーに従い、``rel="next"`` を使用してナビゲーションを行います。  あるいは、コンテンツによって、``@nextLink`` プロパティの形式で次のリンクが指定されます。

```http
GET /revisions?api-version={api-version} HTTP/1.1
```

**応答:**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Accept-Ranges: items
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="list-subset-of-revisions"></a>リビジョンのサブセットを一覧表示する

`Range` 要求ヘッダーを使用します。 応答には `Content-Range` ヘッダーが含まれます。 サーバーは、要求された範囲を満たせない場合、HTTP `416` (RangeNotSatisfiable) を使用して応答します。

```http
GET /revisions?api-version={api-version} HTTP/1.1
Range: items=0-2
```

**Response**

```http
HTTP/1.1 206 Partial Content
Content-Type: application/vnd.microsoft.appconfig.revs+json; charset=utf-8
Content-Range: items 0-2/80
```

## <a name="filtering"></a>フィルター処理

`key` と `label` のフィルター処理の組み合わせがサポートされています。
省略可能な `key` と `label` のクエリ文字列パラメーターを使用します。

```http
GET /revisions?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>サポートされているフィルター

|キー フィルター|結果|
|--|--|
|`key` を省略 (または `key=*`)|**すべての** キーに一致します|
|`key=abc`|**abc** という名前のキーに一致します|
|`key=abc*`|**abc** で始まるキー名に一致します|
|`key=*abc`|**abc** で終わるキー名に一致します|
|`key=*abc*`|**abc** を含むキー名に一致します|
|`key=abc,xyz`|キー名 **abc** または **xyz** に一致します (CSV の上限は 5 つ)|

|ラベル フィルター|結果|
|--|--|
|`label` を省略 (または `label=`)|ラベルなしのエントリに一致します|
|`label=*`|**すべての** ラベルに一致します|
|`label=prod`|**prod** というラベルに一致します|
|`label=prod*`|**prod** で始まるラベルに一致します|
|`label=*prod`|**prod** で終わるラベルに一致します|
|`label=*prod*`|**prod** を含むラベルに一致します|
|`label=prod,test`|**prod** または **test** というラベルに一致します (CSV の上限は 5 つ)|

### <a name="reserved-characters"></a>予約文字

`*`, `\`, `,`

予約文字が値の一部である場合は、`\{Reserved Character}` を使用してエスケープする必要があります。 予約されていない文字もエスケープできます。

### <a name="filter-validation"></a>フィルター検証

フィルター検証エラーが発生した場合、応答は HTTP `400` で、次のようなエラー詳細が含まれます。

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>例

- All

    ```http
    GET /revisions
    ```

- キー名が **abc** で始まる項目

    ```http
    GET /revisions?key=abc*&api-version={api-version}
    ```

- キー名が **abc** または **xyz** で、ラベルに **prod** が含まれている項目

    ```http
    GET /revisions?key=abc,xyz&label=*prod*&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>特定のフィールドを要求する

省略可能な `$select` クエリ文字列パラメーターを使用して、要求されたフィールドのコンマ区切りリストを指定します。 `$select` パラメーターを省略した場合、応答には既定のセットが含まれます。

```http
GET /revisions?$select=value,label,last_modified&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>時間ベースのアクセス

過去の時間のものとして結果の表現を取得します。 [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1) セクションを参照してください。

```http
GET /revisions?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**応答:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.revs+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
