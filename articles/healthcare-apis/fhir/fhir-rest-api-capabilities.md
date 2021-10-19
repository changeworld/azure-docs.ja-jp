---
title: Azure Healthcare API FHIR サービスの FHIR Rest API 機能
description: この記事では、Azure Healthcare API FHIR サービスの RESTful の相互作用と機能について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: cavoeg
ms.openlocfilehash: bca8eb3714a685c9477f512a38406cb77f1c6886
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "130187744"
---
# <a name="fhir-rest-api-capabilities-for-azure-healthcare-apis-fhir-service"></a>Azure Healthcare API FHIR サービスの FHIR Rest API 機能

この記事では、Azure Healthcare API FHIR サービス (ここでは FHIR サービスと呼ばれる) の RESTful 対話の微妙な違いについて説明します。


## <a name="conditional-createupdate"></a>条件付き作成/更新

FHIR サービスでは、FHIR 仕様で定義されている作成、条件付き作成、更新、および条件付き更新がサポートされています。 これらのシナリオで役立つヘッダーの 1 つは [、If-Match ヘッダー](https://www.hl7.org/fhir/http.html#concurrency) です。 ヘッダー `If-Match` が使用され、更新する前に更新されるバージョンが検証されます。 が `ETag` 想定される と一致しない場合 `ETag` は、エラー メッセージ *412 Precondition Failed が生成されます*。 

## <a name="delete"></a>削除

[](https://www.hl7.org/fhir/http.html#delete) FHIR 仕様で定義された削除では、削除後にリソースのバージョン以外の特定の読み取りが 410 HTTP 状態コードを返し、検索によってリソースが見つからない必要があります。 さらに、FHIR サービスを使用すると、リソースを完全に削除 (すべての履歴を含む) できます。 リソースを完全に削除するには、パラメーター設定 `hardDelete` を true (`DELETE {server}/{resource}/{id}?hardDelete=true`) にします。 このパラメーターを渡したり false に設定したりしない場合でも、リソースの以前のバージョン `hardDelete` は引き続き使用できます。

> [!NOTE]
> 履歴のみを削除する場合、FHIR サービスはカスタム操作 をサポートしています。これにより、リソースから履歴 `$purge-history` を削除できます。 

## <a name="conditional-delete"></a>条件付き削除

FHIR サービスでは、削除に加えて条件付き削除がサポートされています。これにより、検索条件を渡してリソースを削除できます。 既定では、条件付き削除を使用すると、一度に 1 つの項目を削除できます。 パラメーターを指定して `_count` 、一度に最大 100 項目を削除することもできます。 条件付き削除の使用例を次に示します。

条件付き削除を使用して 1 つの項目を削除するには、1 つの項目を返す検索条件を指定する必要があります。

DELETE `https://{{hostname}}/Patient?identifier=1032704`

同じ検索を実行できますが、hardDelete=true を含め、すべての履歴も削除できます。

DELETE `https://{{hostname}}/Patient?identifier=1032704&hardDelete=true`

複数のリソースを削除する場合は、 を含め、検索条件に一致する最大 `_count=100` 100 のリソースを削除できます。

DELETE `https://{{hostname}}/Patient?identifier=1032704&_count=100`

## <a name="patch-and-conditional-patch"></a>パッチと条件付き修正プログラム

パッチは、FHIR リソースの一部のみを更新する必要がある場合に重要な RESTful 操作です。 Patch を使用すると、レコード全体を更新することなく、リソースで更新する要素を指定できます。 FHIR では、FHIR でリソースにパッチを適用する方法として、JSON Patch、XML Patch、FHIR Path Patch の 3 種類が定義されています。 FHIR サービスでは、JSON パッチと条件付き JSON パッチがサポートされています (これにより、ID ではなく検索条件に基づいてリソースにパッチを適用できます)。 JSON Patch の使用例については、サンプル REST ファイル を [参照してください](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PatchRequests.http)。

> [!NOTE]
> STU3 に対して を使用する場合、および履歴バンドルを要求する場合、修正プログラムが適用されたリソースの `PATCH` は `Bundle.entry.request.method` にマップされます `PUT` 。 これは、STU3 に HTTPVerb 値セット の動詞の `PATCH` [定義が含まれているためです](http://hl7.org/fhir/STU3/valueset-http-verb.html)。

### <a name="testing-patch"></a>パッチのテスト

Patch 内には、修正プログラムを実行する前に条件が当てはまることを検証できるテスト操作があります。 たとえば、患者を死亡者として設定する場合 (既に死亡者としてマークされていない場合のみ)、次の例を使用できます。 

PATCH `http://{FHIR-SERVICE-NAME}/Patient/{PatientID}` Content-type: `application/json-patch+json`

```
[
    {
        "op": "test",
        "path": "/deceasedBoolean",
        "value": false
    },
    {
        "op": "replace",
        "path": "/deceasedBoolean",
        "value": true
    }
]

```

### <a name="patch-in-bundles"></a>バンドルの修正プログラム

既定では、JSON Patch はバンドル リソースではサポートされていません。 これは、バンドルが FHIR リソースのみをサポートし、JSON Patch が FHIR リソースではないのでです。 これを回避するには、バンドルの実行時に、コンテンツタイプが のバイナリ リソースを JSON 文字列の `"application/json-patch+json"` base64 エンコードとして扱います。 この回避策の詳細については [、Zulip にログインします](https://chat.fhir.org/#narrow/stream/179166-implementers/topic/Transaction.20with.20PATCH.20request)。 

次の例では、患者の性別を女性に変更します。 JSON パッチを取得し `[{"op":"replace","path":"/gender","value":"female"}]` 、base64 にエンコードしました。

POST `https://{FHIR-SERVICE-NAME}/` content-type: `application/json`

```
{
    "resourceType": "Bundle",
    "id": "bundle-batch",
    "type": "batch",
    "entry": [
        {
            "fullUrl": "Patient/{PatientID}",
            "resource": {
                "resourceType": "Binary",
                "contentType": "application/json-patch+json",
                "data": "W3sib3AiOiJyZXBsYWNlIiwicGF0aCI6Ii9nZW5kZXIiLCJ2YWx1ZSI6ImZlbWFsZSJ9XQ=="
            },
            "request": { 
                "method": "PATCH",
                "url": "Patient/{PatientID}"
            }
        }
    ]
}

```

## <a name="next-steps"></a>次の手順

この記事では、FHIR サービスの REST 機能の一部について説明しました。 次に、FHIR サービスでリソースを検索する主な側面について学習できます。 

>[!div class="nextstepaction"]
>[FHIR 検索の概要](overview-of-search.md)

(FHIR&#174;) は HL7 の商標であり [、HL7](https://hl7.org/fhir/) の許可を得て使用されます。


